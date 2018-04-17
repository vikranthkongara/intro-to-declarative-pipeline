# Distributed Pipelines with Pipeline As Code

## Exercise 3.1 - Shared Libraries

In **Exercise 3.1** we are going to add a stage to our pipeline that uses a **Shared Library** to import functionality that allows us to say 'hi'.

More information on using Shared Libraries is available here: https://jenkins.io/doc/book/pipeline/shared-libraries/

1. Add the following line at the top of your pipeline **above** the ```pipeline``` line:

```library 'SharedLibs'```

2. Then add the following stage after the stage you created in **Exercise 3.1**:

```
      stage('Shared Lib') {
         steps {
             helloWorld("Jenkins")
         }
      }
```

The ```helloWorld``` function we are calling can be seen at: https://github.com/PipelineHandsOn/shared-libraries/blob/master/vars/helloWorld.groovy

## Exercise 3.2 - Create GitHub Org and Fork Repos

In **Exercise 3.2** we are going to start by forking an existing Github project that has multiple branches and Jenkinsfiles in each branch.

But first let's create a Github organization to fork the repo into:

1. On Github navigate to **Organizations**: https://github.com/settings/organizations (after logging in)
2. Click on **New Organization**
3. Fill in the **Organization Name**, **Billing Email**, and click on **Create Organization**

Now lets fork the repo into the new organization:

1. Navigate to the rest server application we are going to work with: https://github.com/PipelineHandsOn/sample-rest-server
2. Click on **Fork**
3. Select the **Organization** you want to fork into

## Exercise 3.3 - GitHub Organization Project

In this exercise we are going to create a GitHub Organization project from our newly forked repository.

First let's add your Github credentials to the Jenkins' Credentials manager:

1. Navigate back to your personal folder in Jenkins
2. Click on **Credentials**
3. Click on **[YourFolderName]** under **Stores Scoped to [YourFolderName]**
4. Click on **Global Credentials (Unrestricted)**
5. Click on **Add Credentials**
6. Fill out the form (**Username with password**)
  - **Username**: The Github organization name
  - **Password**: Your Github personal access token
  - **ID**: Create an ID for your credentials (something like **yourorg-id**)
  - **Description**: Can be left blank if you want
7. Click on **OK**

Now let's create the Github Organization project:

1. Click on **New Item**
2. Enter your organization name as the **Item Name**
3. Select **GitHub Organization**
4. Click **Ok**
5. Select the credentials you created above from the **Credentials** drop down
6. Select **All** from the **Strategy** drop down under **Discover Branches**
7. Click **Save**

Once you click on save Jenkins will search your organization for any projects with Jenkinsfiles in them, import those projects as Multibranch projects, and begin building each branch with a Jenkinsfile in it.

When the project was created it also should have created webhooks in Github. Verify that the webhooks were created in Github by checking **Webhooks** within your organization's Github **Settings**.

## Exercise 3.4 - Conditional Execution

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

Notice how after you commit your changes the Github web hooks trigger a build of the development branch in Jenkins.

## Exercise 3.5 - PRs and Merging

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



