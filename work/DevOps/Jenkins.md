---
id: Jenkins
aliases: []
tags: []
---

# Jenkins Snippet Documentation

## Table of Contents

- [Overview](#overview)
- [Pipeline Script](#pipeline-script)
- [Stage Definitions](#stage-definitions)
- [Environment Variables](#environment-variables)
- [Post Actions](#post-actions)
- [Parameters](#parameters)
- [Example Snippet](#example-snippet)

## Overview

This document provides an overview of Jenkins snippets used in our CI/CD pipeline. Each section covers different components and their purposes within the pipeline.

## Pipeline Script

The Pipeline script is the core of Jenkins' Continuous Integration/Continuous Deployment functionality. It defines the workflow.

```groovy
pipeline {
    agent any
    stages {
        // stages go here
    }
}

```

## Stage Definitions

Stages are the building blocks of the pipeline. Each stage defines a specific task that needs to be performed.

```groovy
stage('Build') {
    steps {
        // steps go here
    }
}
```

## Environment Variables

Environment variables are used to store sensitive information such as API keys, passwords, etc. These variables are encrypted and stored in Jenkins' credentials store.

```groovy
environment {
    // environment variables go here
    var1 = 'value1'
    var2 = 'value2'
}
```

- [Retrieve Username and Password from Credential Store of Jenkins](#retrieve-username-and-password-from-credential-store-of-jenkins)
- [Retrieve a secret file from Credential Store of Jenkins](#retrieve-a-secret-file-from-credential-store-of-jenkins)

### Encrypted Variables

Encrypted variables are used to store sensitive information such as API keys, passwords, etc. These variables are encrypted and stored in Jenkins' credentials store.

#### Retrieve Username and Password from Credential Store of Jenkins

The following example shows how to retrieve a username and password from a credential store of Jenkins.

```groovy
pipeline {
    agent any

    environment {
        // Reference a single username and password credential
        MY_CREDENTIALS = credentials('my-credentials-id') // Replace with your credential ID
    }

    stages {
        stage('Example') {
            steps {
                script {
                    def (USERNAME, PASSWORD) = MY_CREDENTIALS.split(':')
                    echo "Username: ${USERNAME}"
                    echo "Password: ${PASSWORD}"
                }
            }
        }
    }
}
```

#### Retrieve a secret file from Credential Store of Jenkins

To retrieve a secret file from a credential store of Jenkins, use the following syntax:

```groovy
pipeline {
    agent any

    environment {
        // Reference a single secret file credential
        MY_SECRET_FILE = credentials('my-secret-file-id') // Replace with your credential ID
    }

    stages {
        stage('Example') {
            steps {
                script {
                    def SECRET_FILE = readFile(file: MY_SECRET_FILE)
                    echo "Secret File: ${SECRET_FILE}"
                }
            }
        }
    }
}
```

## Post Actions

Post actions are actions that are performed after a stage has completed. These actions can be used to send notifications, update Jira tickets, etc.

```groovy
post {
    // post actions go here
}
```

### Different Post Conditions

Different post conditions can be used to perform different actions after a stage has completed.

- [Always](#always)
- [Success](#success)
- [Failure](#failure)
- [Unstable](#unstable)
- [Changed](#changed)

#### Always

The `always` post condition is used to perform an action regardless of the result of the stage.

```groovy
post {
    always {
        // post actions go here
    }
}
```

#### Success

The `success` post condition is used to perform an action only if the stage was successful.

```groovy
post {
    success {
        // post actions go here
    }
}
```

#### Failure

The `failure` post condition is used to perform an action only if the stage failed.

```groovy
post {
    failure {
        // post actions go here
    }
}
```

#### Unstable

The `unstable` post condition is used to perform an action only if the stage was unstable.

```groovy
post {
    unstable {
        // post actions go here
    }
}
```

#### Changed

The `changed` post condition is used to perform an action only if the stage was changed.

```groovy
post {
    changed {
        // post actions go here
    }
}
```

### Different Post Action Types

Different post action types can be used to perform different actions after a stage has completed.

- [Email](#email)
- [Slack](#slack)
- [Jira](#jira)

#### Email

The `email` post action type is used to send an email notification after a stage has completed.

```groovy
post {
    always {
        emailext (
            subject: '${DEFAULT_SUBJECT}',
            body: '${DEFAULT_CONTENT}',
            recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'CulpritsRecipientProvider']]
        )
    }
}
```

#### Slack

The `slack` post action type is used to send a Slack notification after a stage has completed.

```groovy
post {
    always {
        slackSend (
            color: '#FF0000',
            message: "Stage '${env.STAGE_NAME}' failed in ${env.JOB_NAME} (${env.BUILD_NUMBER})"
        )
    }
}
```

#### Jira

The `jira` post action type is used to update a Jira ticket after a stage has completed.

```groovy
post {
    always {
        jiraSend (
            site: 'my-jira-site',
            issueSelector: [$class: 'DefaultIssueSelector'],
            summary: "Stage '${env.STAGE_NAME}' failed in ${env.JOB_NAME} (${env.BUILD_NUMBER})",
            description: "Stage '${env.STAGE_NAME}' failed in ${env.JOB_NAME} (${env.BUILD_NUMBER})",
            additionalFields: [
                field: 'customfield_12345',
                value: 'value'
            ]
        )
    }
}
```

## Parameters

Parameters are used to pass values to stages. These values can be used to customize the pipeline for different environments or to pass values between stages.

```groovy
parameters {
    // parameters go here
}
```

### Different Parameter Types

Different parameter types can be used to pass different types of values to stages.

- String
- Boolean
- Choice
- Text

### String

The `string` parameter type is used to pass a string value to a stage.

```groovy
parameters {
    string(name: 'MY_STRING', defaultValue: 'default-value', description: 'A string parameter')
}
```

### Boolean

The `boolean` parameter type is used to pass a boolean value to a stage.

```groovy
parameters {
    booleanParam(name: 'MY_BOOLEAN', defaultValue: false, description: 'A boolean parameter')
}
```

### Choice

The `choice` parameter type is used to pass a choice value to a stage.

```groovy
parameters {
    choice(name: 'MY_CHOICE', choices: ['choice1', 'choice2', 'choice3'], description: 'A choice parameter')
}
```

### Text

The `text` parameter type is used to pass a text value to a stage.

```groovy
parameters {
    text(name: 'MY_TEXT', defaultValue: 'default-value', description: 'A text parameter')
}
```

## Triggers

Triggers are used to automatically start a pipeline when a certain event occurs. For example, a pipeline can be automatically started when a new commit is pushed to a specific branch.

```groovy
triggers {
    // triggers go here
}
```

### Different Trigger Types

Different trigger types can be used to automatically start a pipeline when a certain event occurs.

- [SCM](#scm)
- [Timer](#timer)

#### SCM

The `scm` trigger type is used to automatically start a pipeline when a certain event occurs.

```groovy
triggers {
    scm('H/5 * * * *')
}
```

#### Timer

The `timer` trigger type is used to automatically start a pipeline after a certain amount of time.

```groovy
triggers {
    cron('H/5 * * * *')
}
```

## def functions

The `def` function is used to define a variable in a pipeline.

```groovy
def MY_VARIABLE = 'value'
```

### Different def Functions

Different `def` functions can be used to define different types of variables in a pipeline.

- String
- Boolean
- Integer
- List
- Map

#### String

The `string` def function is used to define a string variable in a pipeline.

```groovy
def MY_STRING = 'value'
```

#### Boolean

The `boolean` def function is used to define a boolean variable in a pipeline.

```groovy
def MY_BOOLEAN = true
```

#### Integer

The `integer` def function is used to define an integer variable in a pipeline.

```groovy
def MY_INTEGER = 1
```

#### List

The `list` def function is used to define a list variable in a pipeline.

```groovy
def MY_LIST = ['value1', 'value2', 'value3']
```

#### Map

The `map` def function is used to define a map variable in a pipeline.

```groovy
def MY_MAP = [key1: 'value1', key2: 'value2', key3: 'value3']
```

## Defining functions in a pipeline

Functions are used to define reusable blocks of code in a pipeline. Functions can be used to perform complex operations or to encapsulate logic.

```groovy
def MY_FUNCTION = {
    // code goes here
}
```

### Different Function Types

Different function types can be used to define different types of functions in a pipeline.

- [Simple](#simple)
- [Return](#return)

#### Simple

The `simple` function type is used to define a simple function in a pipeline.

```groovy
def MY_FUNCTION = {
    // code goes here
}
```

#### Return

The `return` function type is used to define a function that returns a value in a pipeline.

```groovy
def MY_FUNCTION = {
    // code goes here
    return 'value'
}
```

#### Defining a function with parameters

A function can be defined with parameters to pass values to the function.

```groovy
def MY_FUNCTION(String param1, String param2) {
    // code goes here
}
```

### Calling a function

A function can be called by using the `call` keyword.

```groovy
MY_FUNCTION.call('param1', 'param2')
```

### Calling a function with parameters

A function can be called with parameters by using the `call` keyword.

```groovy
MY_FUNCTION.call('param1', 'param2')
```

## Example Snippet

Below is an example of a Jenkins snippet that defines a pipeline with a single stage.

```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Hello World'
            }
        }
    }
}
```

### Function to run commands on a remote machine

```groovy

def remoteExecute(username, host, password, commands) {
        sh """
            ssh -o StrictHostKeyChecking=no -i ${password} "${username}"@"${host}" '${commands}'
        """
}

```

### Shell command to change the node version

```groovy

def changeNodeVersion(version) {
    sh """
        <!-- curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.2/install.sh | bash -->
        export NVM_DIR="$HOME/.nvm"
        [ -s "$NVM_DIR/nvm.sh" ] && \\. "$NVM_DIR/nvm.sh"
        nvm install ${version}
        nvm alias default ${version}
        nvm use default
    """
}

```

### Clone a repository

```groovy

def cloneRepo(repo, branch, credentialsId) {
    withCredentials([usernamePassword(credentialsId: credentialsId, passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
        sh """
            git clone --depth 1 --branch ${branch} https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${repo}.git
        """
    }
}
```
