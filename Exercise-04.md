# Distributed Pipelines with Pipeline As Code

## Shared Libraries

In this exercise we are going to add a stage to our pipeline that uses a **Shared Library** to import functionality that allows us to say 'hi'.

More information on using Shared Libraries is available here: https://jenkins.io/doc/book/pipeline/shared-libraries/

>Note: The `libraries ` declaration does not currently work with the Blue Ocean Pipeline Editor. So the changes for this exercise will need to be done directly on your file in source control. See https://help.github.com/articles/editing-files-in-your-repository/ 

1. Add the following line after the top level the `agent` directive:

```
  libraries {
    lib("SharedLibs")
  }
```

2. Then add the following stage after the stage you created in **Exercise 3.1**:

```
      stage('Shared Lib') {
         steps {
             helloWorld("Jenkins")
         }
      }
```

>The `helloWorld` function we are calling can be seen at: https://github.com/PipelineHandsOn/shared-libraries/blob/master/vars/helloWorld.groovy

## Conditional Execution

In this exercise we are going to edit the Jenkinsfile file in the **development** branch of our project to add a branch specific stage.

**Important Note** The following code demonstrates a new set of features added to Declarative Pipeline in Version 1.2.6:

  - Add beforeAgent option for when - if true, when conditions will be evaluated before entering the agent.

1. Within your **sample-rest-server** project select the **development** branch from the **Branch** drop down menu
2. Click on the **Jenkinsfile** in the file list
3. Click on the **Edit this file** button (pencil)
4. Insert the following stage after the existing **build** stage:

```
      stage('Development Tests') {
         when {
            beforeAgent true
            branch 'development'
         }
         steps {
            echo "Run the development tests!"
         }
      }
```

5. Fill out the commit information, select 'Commit directly to the development branch.', and click on **Commit Changes**

>Notice how after you commit your changes the Github web hooks trigger a build of the development branch in Jenkins.

## PRs and Merging

In this exercise we are going to edit the development branch's Jenkinsfile again but make our commit against a feature branch and user a pull request to merge the edits into our development branch.

1. Click on the **Edit this file** button (pencil)
2. Insert the following stage after the existing **build** stage:

```
      stage('Masters Tests') {
         when {
            branch 'master'
         }
         steps {
            echo "Run the master tests!"
         }
      }
```

3. Fill out the commit information, select 'Create a new branch for this commit and start a pull request.' and click on **Propose file change**
4. Flip back to your Jenkins job and notice that the new feature branch appears in your projects
5. Return back to the Github **Open a pull request** page
6. Click on the **Create pull request** button
7. Go to your Jenkins job and notice that that the PR has been added to the Pull Requests tab
8. In Github click on **Merge pull request** and then **Confirm** to close the PR and merge the results into the development branch
9. Optionally you can also delete the feature branch you created

Finally, we should merge our work into our master branch to verify that our changes work there:

1. Return back to your repository's main page where you will be on the master branch by default
2. Click on **New pull request**
3. Select your base fork (not the project we forked from)
4. Compare **master** to **development**
5. Click **View pull request**
6. Click **Merge pull request**
7. Click **Confirm merge**

>Notice how after you merge your changes into master the Github web hooks trigger a build of the master branch in Jenkins.

## Advanced Jenkins Kubernetes Agents

In this exercise we will explore the [Jenkins Kubernetes plugin](https://github.com/jenkinsci/kubernetes-plugin) and will update a Jenkinsfile to use the `podTemplate` and `container` directives in your Declarative Pipeline. The plugin creates a [Kubernetes Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/) for each agent requested by a Jenkins job, with at least one Docker container running as the JNLP agent, and stops the pod and all containers after the build is complete (or after a set amount of time as is the case here).

>NOTE: The **Jenksin Kubernetes Plugin** is automatically installed and configured to provision dynamic ephemeral agents by CloudBees Jenkins Enterprise on Kubernetes. 

1. Copy and paste the following code into the **Pipeline Script** text box near the bottom of the page:

```
pipeline {
  agent {
    kubernetes {
      label 'kubernetes'
      containerTemplate {
        name 'go'
        image 'golang:1.10.1-alpine'
        ttyEnabled true
        command 'cat'
      }
    }
  }
  stages {
    stage('golang in k8s') {
        steps {
            container('go') {
                sh 'go version'
            }
        }
    }
  }
}
```
**Note:** Notice the use of the **container** directive.  This tells Jenkins which container in a Pod to use for the steps in the stage.  In this exercise a single container (golang) was explicitly defined in the pipeline, however a second container is implicitly created to handle the JNLP communiction between Jenkins and the Pod.

```
pipeline {
  agent {
    kubernetes {
      label 'kubernetes'
      containerTemplate {
        name 'go'
        image 'golang:1.10.1-alpine'
        ttyEnabled true
        command 'cat'
      }
    }
  }
  stages {
    stage('golang in k8s') {
        steps {
            container('go') {
                sh 'go version'
            }
            container('jnlp') {
                sh 'java -version'
            }
        }
     }
  }
}
```
**Note:** In the above example you were able to execute a java command in the implicitly defined **jnlp** container in the Pod.  The JNLP container is a part of every Pod created by the Jenkins Kubernetes plugin.
