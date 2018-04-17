# Declarative Syntax Basics

## Exercise 1.0 - Setup to use Blue Ocean Pipeline Editor

**Note**: You need to have a Github personal access token ([Github-Personal-Access-Token.md](Github-Personal-Access-Token.md)) before proceeding.

We will setup the [Blue Ocean Pipeline Editor](https://jenkins.io/doc/book/blueocean/pipeline-editor/) so we can use the visual editor for many of following exercises:

1. If not already in Blue Ocean, click on the **Teams** or **Open Blue Ocean** link in the left side navigation bar
2. Click on the **Create a new Pipeline** button (if you have already created a Pipeline, you will need to click the **New Pipeline** button in the upper right) <p><img src="img/1-0-create-a-new-pipeline.png" width=300/>
3. Click on one of the options in the **Where do you store your code?** section (**GitHub** for this course) <p><img src="img/1-0-select-scm.png" width=300/>
4. Enter your **Github token** (your GitHub Personal Access Token)
5. Next, select your GitHub user account (NOTE: Be sure to select your user account or the GitHub Organization where you created the empty repository for this workshop. The repository should not have an existing Jenkinsfile.) <p><img src="img/1-0-select-github-org.png" width=300/>
6. Under **Choose a repository** select the repository you created during setup and then click the **Create Pipeline** button <p><img src="img/1-0-choose-repo-create-pipeline.png" width=320/>

Next, the Blue Ocean editor will open - if the editor does not load after a minute or so, refresh your browser. 

## Exercise 1.1 - Basic Declarative Syntax Structure

In **Exercise 1.1** we will create a simple declarative pipeline using the Blue Ocen Pipeline editor.

Declarative Pipelines must be enclosed within a `pipeline` block and must contain a top-level `agent` declaration, and then contains exactly one `stages` block. The `stages` block must have at least one `stage` block but can have an unlimited number of additional stages. Each `stage` block must have exactly one `steps` block. The Blue Ocean editor takes care of much of this for you but we will need to add a `stage` and `steps`.

Using the Blue Ocean Pipeline editor we setup in Exercise 1.0, do the following:

1. You should see the **You don't have any branches that contain a Jenkinsfile** dialog, click on the **Create Pipeline** button (NOTE: If you already had a `Jenkinsfile` in your repository then the editor should open straight-away) <p><img src="img/1-1-create-pipeline-no-jenkinsfile.png" width=300/>
2. Click on the **+** icon next to the pipeline's **Start** node to add a `stage`
3. Click into **Name your stage** and type in the text 'Say Hello'
4. Click on the **+ Add step** button
5. Click on the **Print Message** step and type in 'Hello World!' as the **Message**
6. Click on the **<-** (arrow) next to the **'Say Hello / Print Message'** text to add another step <p><img src="img/1-1-print-message-then-add-step.png" width=300/>
7. Click on the **+ Add step** button
8. Click on the **Shell Script** step and enter `java -version` into the text area
9. Press the key combination `CTRL + S` to open the Blue Ocean free-form editor and you should see a Pipeline similar to the one below (click anywhere outside the editor to close it):

```
pipeline {
   agent any
   stages {
      stage('Say Hello') {
         steps {
            echo 'Hello World!'   
            sh 'java -version'
         }
      }
   }
}
```

8. Click the **Save** button <p><img src="img/1-1-shell-step-save.png" width=300/>
9. Enter a commit message into the **Save Pipeline** pop up and click **Save & Run** <p><img src="img/1-1-shell-step-save.png" width=300/>

>Your Pipeline is actually being committed and pushed to your GitHub repository, and will run right away.

10. Click on your Pipeline to see the `steps` execute. <p><img src="img/1-1-click-to-see-pipeline-run.png" width=400/>
11. Expand the **'java -version — Shell Script' `step` and you should see the following: <p><img src="img/1-1-java-version-step-expanded.png" width=420/>

## Exercise 1.2 - Agent Labels

In **Exercise 1.2** we will update the pipeline we created in Exercise 1.1 to use a specific `agent` using the `label` syntax. As you saw from the build logs of the previous exercise, the Java version of the `agent any` was less than 9. We want to update our pipeline to use a version 9 JDK by replacing the `any` parameter with a `label` declaration:

1. Click on the **pencil** icon in the top right to edit your Pipeline <p><img src="img/1-2-click-pencil.png" width=520/>
2. Use the key combination `CTRL + S` to open up the free-form editor
3. Replace the `agent any` declaration with the following `agent` declaration *NOTE: Don't worry about formatting too much as the Blue Ocean editor will reformat evertying before it is committed to your repository):

```
  agent {
    label 'jdk9'
  }
```

2. Click the **Update** button
3. Click the **Save** button 
4. Enter a commit message into the **Save Pipeline** pop up and click **Save & Run**. 

You should see the following output for the `sh` step after your Pipeline runs:

```
openjdk version "9.0.4"
OpenJDK Runtime Environment (build 9.0.4+12-Debian-4)
OpenJDK 64-Bit Server VM (build 9.0.4+12-Debian-4, mixed mode)
```

In addition to `any` and `label` you may also specify `none` and no global agent will be allocated for the entire Pipeline run and each `stage` section will need to contain its own `agent` section.

Before going on to the next exercise let's revert our pipeline to using:

```
   agent any
```

Your Pipeline should look like the following Pipeline:

```
pipeline {
   agent any
   stages {
      stage('Say Hello') {
         steps {
            echo 'Hello World!'   
            sh 'java -version'
         }
      }
   }
}
```

## Exercise 1.3 - Agents with Docker

In **Exercise 1.3** we will update the pipeline we created in Exercise 1.1 to execute steps in a Docker container. The `docker` agent directive allows you to use any abritary Docker image to run Pipeline steps inside. To update the pipeline:

1. In the `steps` block replace the `sh 'java -version'` step with the following step:

```
  sh 'go version'
```

2. **Save & Run** your pipeline and check the Console Log. The build should fail with the error: `go: not found`

3. Click on configure and update the `agent` portion of the pipeline to read:

```
   agent {
      docker { 
        image 'golang:1.10.1-alpine'
        label 'docker-cloud' 
      }
   }
```

4. **Save & Run** your pipeline and check the Console Log to see how Jenkins pulls the appropriate docker image and runs your build inside the container created from that image. 

5. Now, remove the `label 'docker-cloud'` line from your pipeline job and **Save & Run** your pipeline.  The job should still work... but why?? *Pipeline Model Definition* is why.  The *Pipeline Model Definition* allows an administrator to define the default `label` a job should use if one is not specified with for the `docker` directive.  In this case the *Pipeline Model Definition* is already set to `docker-cloud`.  This means that you will get an agent capable of running Docker containers everytime you use the `docker` `agent` directive, so using the `label` parameter is redundant unless you need a specific Docker enabled agent.

Before going on to the next exercise let's revert our pipeline to using:

```
   agent any
```

And remove the `sh 'go version'` step.

## Exercise 1.4 - Environment Directive

For **Exercise 1.4** we are going to update our pipeline to demonstrate how to use the `environment` directive to set and use environment variables. We will also see how this directive supports a special helper method `credentials()` that allows access to pre-defined Jenkins Credentials based on their id.

### Simple Environment Variables

1. At the top of the pipeline insert the following code between the `agent` and `stages` blocks:  

```
   environment {
      MY_NAME = 'Mary'
   }
```

2. Then update the `echo 'Hello World!'` line to read `echo "Hello ${MY_NAME}!"` and run your build again to view the results.  Notice the change from '' to "".  Using double quotes will trigger extrapolation of environment variables.

### Credentials

We can also use environmental variables to import credentials.

1. Add the following line to our ```environment``` block:

```TEST_USER = credentials('test-user')```

2. Add the following ```echo``` steps within the ```steps``` of the Say Hello ```stage```:

```
            echo "${TEST_USER_USR}"
            echo "${TEST_USER_PSW}"
```

**Note**: After executing the build look at the console output and make note of the fact that the credential user name and password are masked when output via the echo command.

## Exercise 1.5 - Parameters

In **Exercise 1.5** we will alter our pipeline to accept external input in the form of a Parameter.

1. At the top of your pipeline insert the following block of code between the ```environment``` and ```stages``` blocks:

```
   parameters {
      string(name: 'Name', defaultValue: 'whoever you are', 
	     description: 'Who should I say hi to?')
   }
```

2. Update the ```echo "Hello ${MY_NAME}!'``` line to read ```echo "Hello ${params.Name}!"```
3. **Save & Run** your pipline and view the results.

**Note**: Jenkins UI won't update properly when you save the pipeline to show the ```Build with parameters``` option so you need to run a build, view the results, and then return to the project to see the updated option.
