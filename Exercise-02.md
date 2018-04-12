# Declarative Advanced Syntax

## Exercise 2.1 - Interactive Input

For **Exercise 2.1** we are going to add a new stage after the **Say Hello** stage that will demonstrate how to ask interactively for user input. 

**Important Note** The following code demonstrates a new set of features added to Declarative Pipeline in Version 1.2.6:

  - Add options {} for stage - supports block-scoped "wrappers" like timeout and Declarative options like skipDefaultCheckout
  - Add input {} directive for stage - runs the input step with the supplied configuration before entering the when or agent for a stage, and makes any parameters provided as part of the input step available as environment variables.

The declarative `input` directive blocks the `stage` from executing and acquiring an agent - this is an important enhancement as previously a more complicated work-around was required to not tie up an agent with an input step. If the `input` is approved, the stage will then continue.

Insert the following `stage` block into your pipeline after `stage('Say Hello') {}` block:

```
    stage('Deploy') {
      input {
        message "Should we continue?"
      }
      steps {
        echo "Continuing with deployment"
      }
    }
```
Execute the job and note the input prompt during the ```Deploy``` stage.  This input prompt is available in the Console log as well as in Stage View and Blue Ocean.

**Note**: To keep Jenkins from waiting indefinitely for a user response you should set a ```timeout``` for the `stage` like shown below:

```
    stage('Deploy') {
      options {
        timeout(time: 1, unit: 'MINUTES') 
      }
      input {
        message "Should we continue?"
      }
      steps {
        echo "Continuing with deployment"
      }
    }
```

## Exercise 2.2 - Input Parameters

In this example we will replace the **Deploy** stage with an input that returns data to the pipeline for use later in a subsequent step or stage.  This form of input is useful when needing to query users for additional data before continuing pipeline processing.

Replace the **Deploy** stage with the following and rerun the job:
```
    stage('Deploy') {
      input {
        message "Which Version?"
        ok "Deploy"
        parameters {
            choice(name: 'APP_VERSION', choices: "v1.1\nv1.2\nv1.3", description: 'What to deploy?')
        }
      }
      steps {
        echo "Deploying ${APP_VERSION}."
      }
    }
```


## Exercise 2.3 - Post Actions

What happens if your input step times out? **Post Actions** are designed to handle a variety of conditions (not only failures) that could occur outside the standard pipeline flow.

In this example we will add a Post Action to our **Deploy** stage to handle a time out (aborted run). Modify your **Deploy** stage to look like:

```
    stage('Deploy') {
      options {
        timeout(time: 1, unit: 'MINUTES') 
      }
      input {
        message "Which Version?"
        ok "Deploy"
        parameters {
            choice(name: 'APP_VERSION', choices: "v1.1\nv1.2\nv1.3", description: 'What to deploy?')
        }
      }
      steps {
        echo "Deploying ${APP_VERSION}."
      }
    }  
```

Next, add the following to the bottom of your `pipeline` - right before the close curly brace for the entire `pipeline`:

```
  post {
    aborted {
      echo 'Why didn\'t you push my button?'
    }
  }
```

On the next build wait for the input time and you will see the following line in your console output: ```Why didn't you push my button?```.

**Note**: After completing this exercise remove the ```Deploy``` stage from your pipeline so that you will not have to manually approve the job each time it runs.

## Exercise 2.4 - Script Block

In this exercise we will combine the simplicity of declarative pipeline with more advanced features of pipeline available via the `script {}` block.  

Scripted Pipelines can be very advanced and contain advanced flow control and variable assignment that are not available in declarative pipelines without using a `script` block.  A `script` block allows you to insert the more advanced scripted syntax of pipeline into a declarative pipeline.

After removing the ```stage('Deploy')``` block from the previous example add two new stages called ```stage('Get Kernel')``` and ```stage('Say Kernel')``` to your pipeline after the **Say Hello** stage:

```
    stage('Get Kernel') {
      steps {
        script {
          try {
            KERNEL_VERSION = sh (script: "uname -r", returnStdout: true)
          } catch(err) {
            echo "CAUGHT ERROR: ${err}"
            throw err
          }
        }
      }
    }
    stage('Say Kernel') {
      steps {
        echo "${KERNEL_VERSION}"
      }
    }

```
**Note:** Notice the use of the `script` block in the above example.  This allows you to use more advanced scripting options in your declarative pipelines.

**Note**: After completing this exercise remove the `Get Kernel` and `Say Kernel` stages from your pipeline.


## Exercise 2.5 - Parallelization

In this exercise we are going to add another stage to our pipeline that runs two steps in parallel on two different docker based agents (one running Java 7 and one running Java 8). The following code also includes ```sleep``` steps to demonstrate what happens when parallel steps complete execution at different times:

**Important Note** The following code demonstrates a new set of features added to Declarative Pipeline in Version 1.2 (parallel stages) and 1.2.1 (failFast for a parallel stage).

Add the following stage after ```stage('Say Hello')```:

```
      stage('Testing') {
        failFast true
        parallel {
          stage('Java 7') {
            agent { docker 'openjdk:7-jdk-alpine' }
            steps {
              sh 'java -version'
              sleep time: 10, unit: 'SECONDS'
            }
          }
          stage('Java 8') {
            agent { docker 'openjdk:8-jdk-alpine' }
            steps {
              sh 'java -version'
              sleep time: 20, unit: 'SECONDS'
            }
          }
        }
      }
```

**Note**: If your build breaks double check your pipeline script to make sure that the agent at the top of of the pipeline was reverted back to ```agent any``` as described in [exercise 1.2](./Exercise-01.md#exercise-12---agent-labels).

