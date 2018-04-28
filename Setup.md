# Workshop Setup

## Jenkins Set-up
Setup a work environment for the lessons provided in this workshop.  Ask the instructor for the URL of the server you will be using during the workshop.

Todays URL for the CloudBees Jenkins environment is **https://cje.workshop.beedemo.net**

The URL for the GitHub Repository with these instructions and the exercises is **https://github.com/cloudbees/intro-to-declarative-pipeline/**

### Create a Jenkins Account

1. Goto to the Workshop URL provided by the instructor;
2. Click on the **Create an account** link in the middle of the page under the **Login** button.<p><img src="img/setup-create-an-account.png" width=300/>
3. Complete the **Sign up** form (all fields are required) and click the **Sign up** button;
4. You should see a **Success** page - click on **the top page** link;

### Create a Team Master

Next, everyone will get their own Jenkins masters referred to as a Team Master and we will create, edit and interact with our Pipelines in Blue Ocean.

1. If not in Blue  Ocean, click on the **Teams** link in the left menu; <p><img src="img/setup-classic-ui-Teams-link.png" width=400/>
2. Click on the **Create team** button in the center of your screen;<p><img src="img/setup-create-a-team.png" width=300/>
3. **Name this team** - enter a name for your team - perhaps your first initial with your last name (Team names must be unique) and then click **Next**;
4. **Choose an icon for this team** - select an icon and color for your team and then click **Next**;
5. **Add people to this team** - your user will show up as a **Team Admin** and we won't be adding any additional users, but feel free to look around and then click **Next**;
6. **Select team master creation recipe** - click on the drop-down and but select the **Basic** recipe;
7. Finally, click the **Create team** button. <p><img src="img/setup-create-team-with-basic.png" width=400/>
8. While your master is being  created, move onto the next section **Create a GitHub.com user account**

## Create a GitHub.com user account
Setup a GitHub.com user account that will be used later in this workshop. If you have an existing GitHub.com account you will be able to use it if you are comfortable using that account to create a GitHub Organization later in the workshop.

1. Visit https://github.com/join and fill in the required fills to create a user account.
2. Select "Unlimited public repositories for free" when choosing your plan.
3. Verify your email account to ensure you account is activated.  An activated account will be **required** in the next few exercises.

## Create a GitHub Personal Access Token
The following instructions cover how to create a Github Personal Access Token that you will use within Jenkins to connect Pipelines, Multibranch Pipelines, and Github Organization Projects to your Github repositories.

1. Log into Github.com
2. Select the **Settings** option from the profile menu in the upper right hand corner of the browser window <p><img src="img/settings.png" width=200/>
3. Click on **Personal access tokens** under **Developer settings**
4. Fill out a description for the token (e.g. **MyJenkinsToken**)
5. Select the following options. <p> <img src="img/permissions.png" width=400/>
6. Click on **Generate Token**
7. As the success message says: **Make sure to copy your new personal access token now. You won’t be able to see it again!**  

## Create a GitHub Organization

Create a Github organization to use for this workshop:

1. On Github navigate to **Organizations**: https://github.com/settings/organizations (after logging in)
2. Click on **New Organization**
3. Fill in the **Organization Name**, **Billing Email**, and click on **Create Organization**

>NOTE: Even though you have to provide an email for billing, you will not be charged anything as long as you choose the free option.

## Create an empty GitHub repository for your first example Pipeline
The following instructions cover how to create a Github repository under your personal account.

1. Log into Github.com
2. Click on the **+** dropdown at the top right of the screen to the left of your avatar and select **New repositroy** <p><img src="img/setup-github-new-repo.png" width=200/>
3. Enter a **Repository name** for your repository - such as **intro-pipeline**
4. Check the **Initialize this repository with a README** checkbox
5. Click the **Create repository** button <p><img src="img/setup-github-create-repo-completed-form.png" width=300/>

## Finished Set-up
You should see the following **Create a new Pipeline** screen for your Team:
<p><img src="img/setup-success.png" width=600/>
  
If you see this screen then move onto **[Declarative Syntax Basics](./Exercise-01.md)**

>NOTE: If you see the following screen then please follow **[these instructions](./License-Screen.md)**.
<p><img src="img/license-error-1.png" width=400/>
