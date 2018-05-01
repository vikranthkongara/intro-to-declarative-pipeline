# Declarative Advanced Syntax

## Interactive Input

For this exercise we are going to add a new stage after the **Say Hello** stage that will demonstrate how to pause the job and prompt for interactive input. 

>NOTE: The declarative `input` directive blocks the `stage` from executing and acquiring an agent - this is an important enhancement as previously a more complicated work-around was required to not tie up an agent with an input step. If the `input` is approved, the stage will then continue.

1. Add the following `stage` block to your pipeline after `stage('Say Hello') {}` block:

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

2. **Save & Run** your pipeline and note the input prompt during the ```Deploy``` stage.  This input prompt is also available in the Console log and classic Stage View.<p><img src="img/2-input-basic.png" width=550/>

>NOTE: Jenkins will wait indefinitely for a user response. Let's fix that by setting a timeout.
3. Set a `timeout` for the `stage` using `stage` `options` by replacing your **Deploy** `stage` with the following:

```
    stage('Deploy') {
      options {
        timeout(time: 30, unit: 'SECONDS') 
      }
      input {
        message "Should we continue?"
      }
      steps {
        echo "Continuing with deployment"
      }
    }
```

4. **Save & Run** your pipeline and wait at least 30 seconds. Your pipeline should be automatically aborted.<p><img src="img/2-input-timeout.png" width=550/>

## Input Parameters

In this example we will update the **Deploy** stage with an input that returns data to the pipeline for use later in a subsequent step or stage.  This form of input is useful when needing to query users for additional data before continuing pipeline processing.

>NOTE: There are a number of Pipeline steps and/or parameters for steps that are not supported by the Blue Ocean Editor. The `ok` parameter of the `input` step is one such parameter. So for this exercise we will use the GitHub file editor.

1. Open your `Jenkinsfile` in the GitHub repository you created in the workshop setup and click on the **pencil** icon to open the editor.<p><img src="img/2-input-parameters-github-edit.png" width=550/>
2. Replace the **Deploy** stage with the following in the GitHub editor

```
    stage('Deploy') {
      options {
        timeout(time: 30, unit: 'SECONDS') 
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

3. At the bottom of the GitHub editor page, enter a commit message and click the **Commit changes** button to commit your changes to the **master** branch.<p><img src="img/2-input-params-commit.png" width=550/>
4. Run your pipeline from the **Branches** view of the Blue Ocean Activity View for your pipeline.
>NOTE: Committing changes to GitHub will not automatically trigger your job unless a webhook has been configured. 
5. Select an option from the **Wait for interactive input** dialog and click the **Deploy** (no longer **Proceed**) button.<p><img src="img/2-input-param-select-option.png" width=550/>
>NOTE: If you take longer than 30 seconds to select an option then your pipeline will timeout and be aborted.
6. View the output of the **Print Message** step for the **Deploy** stage.

## Post Actions

What happens if your input step times out? **Post Actions** are designed to handle a variety of conditions (not only failures) that could occur outside the standard pipeline flow.

In this example we will add a **Post Action** to our **Deploy** stage to handle a time out (aborted run). 

1. Add the following to the bottom of your `pipeline` - right before the close curly brace for the entire `pipeline` using the GitHub editor:

```
  post {
    aborted {
      echo 'Why didn\'t you push my button?'
    }
  }
```

2. Commit your changes.
3. Run your pipeline from the **Branches** view of the Blue Ocean Activity View for your pipeline.
4. Wait for 30 seconds and you should see the following line in your console output: `Why didn't you push my button?` **OR** you could just click the **Abort** button.<p><img src="img/2-post-action-abort.png" width=550/>
5. Finally, remove the `Deploy` stage from your pipeline so that you will not have to manually approve the job each time it runs for the rest of the workshop.

## Script Block

In this exercise we will combine the simplicity of Declarative Pipeline with more advanced features of Pipeline available via the `script {}` block.  

Scripted Pipelines may contain advanced flow control and variable assignment that are not available in Declarative Pipelines without using a `script` block.  A `script` block allows you to insert the more advanced scripted syntax of pipeline into a Declarative Pipeline.

1. After removing the `stage('Deploy')` block from the previous example add two new stages called `stage('Get Kernel')` and `stage('Say Kernel')` to your pipeline after the **Say Hello** stage:

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

2. **Save & Run** your pipeline.
3. Note the output of the **Print Message** step in the `Say Kernel` stage.
4. Remove the `Get Kernel` and `Say Kernel` stages from your pipeline.
5. **Save & Run** your pipeline.

## Parallelization

In this exercise we are going to add another stage to our pipeline that runs two steps in parallel on two different agents (one running Java 8 and one running Java 9). The following code also includes `sleep` steps to demonstrate what happens when parallel steps complete execution at different times.

>**NOTE:** The following code demonstrates a new set of features added to Declarative Pipeline in Version 1.2 (parallel stages) and 1.2.1 (failFast for a parallel stage).

1. Add the following stage after `stage('Say Hello')`:

```
      stage('Testing') {
        failFast true
        parallel {
          stage('Java 8') {
            agent { label 'jdk8' }
            steps {
              sh 'java -version'
              sleep time: 10, unit: 'SECONDS'
            }
          }
          stage('Java 9') {
            agent { label 'jdk9' }
            steps {
              sh 'java -version'
              sleep time: 20, unit: 'SECONDS'
            }
          }
        }
      }
```

2. **Save & Run** your pipeline.
3. Notice in the Blue Ocean Pipeline Run Details View that the **Testing** stage has two sub-stags: ***Jave 8*** and ***Jave 9***.<p><img src="img/2-parallel-details-view.png" width=450/>

## Next Exercises
You may move onto **[Distributed Pipelines with CloudBees](./Exercise-03.md)** once your instructor tells you to.

Before you proceed you may want to check that your Pipeline looks like the following:

```
pipeline {
  agent {
    label 'jdk8'
  }
  stages {
    stage('Say Hello') {
      steps {
        echo "Hello ${params.Name}!"
        sh 'java -version'
        echo "${TEST_USER_USR}"
        echo "${TEST_USER_PSW}"
      }
    }
    stage('Testing') {
      failFast true
      parallel {
        stage('Java 8') {
          agent {
            label 'jdk8'
          }
          steps {
            sh 'java -version'
            sleep(time: 10, unit: 'SECONDS')
          }
        }
        stage('Java 9') {
          agent {
            label 'jdk9'
          }
          steps {
            sh 'java -version'
            sleep(time: 20, unit: 'SECONDS')
          }
        }
      }
    }
  }
  environment {
    MY_NAME = 'Mary'
    TEST_USER = credentials('test-user')
  }
  post {
    aborted {
      echo 'Why didn\'t you push my button?'
      
    }
    
  }
  parameters {
    string(name: 'Name', defaultValue: 'whoever you are', description: 'Who should I say hi to?')
  }
}
```

