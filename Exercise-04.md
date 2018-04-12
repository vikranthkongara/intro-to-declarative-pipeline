# Distributed Pipelines with CloudBees

## Exercise 4.1 - Checkpoints

In this exercise we are going to quickly create a new pipeline to demonstrate how **Checkpoints** work and how end users can interact with Checkpoints once a job as been built. To test this create a new pipeline job in your personal folder copying and pasting the following code into the Pipeline Script textbox:

```
pipeline {
   agent none
   stages {
      stage('One') {
         agent any
         steps {
            echo 'Stage One - Step 1'
         }
      }
      stage('Checkpoint') {
         agent none
         steps {
            checkpoint 'Checkpoint'
         }
      }
      stage('Two') {
         agent any
         steps {
            echo 'Stage Two - Step 1'
         }
      }
   }
}
```

After saving the job click on **Build Now** to run it.

When the job has completed running you will see a **Resume** icon in the build's **Stage View**. Clicking on the **Resume** icon gives you the ability to:

* **Delete** - Delete the cached artifacts and configuration for that build;
* **Restart** - Restart the build from the checkpoint.

**Note**: Deleting a checkpoint doesn't make the **Resume** icon vanish.

## Exercise 4.2 - Cross Team Collaboration
In this exercise we are going to set-up two Pipeline jobs that demonstrate CloudBee's Cross Team Collaboration feature. We will need two separate Pipelines - one that publishes an event - and two - another that is triggered by an event.

### Master Events

For the first part of **Cross Team Collaboration** we will create an event that is only publishe on your master.

#### Publish Event

First you have to publish an event from a Pipeline - any other Pipeline may set a trigger to listen for this event. Create a Pipeline job named `notify-event` with the following content:

```
pipeline {
    agent none
    stages {
        stage('Publish Event') {
            steps {
                publishEvent(generic('beeEvent'))
            }
        }
    }
}
```

Replace `beeEvent` with your `{username}Event` so my event would be `kmadelEvent`.

#### Event Trigger

Next, create a Pipeline job name `notify-trigger` and set a `trigger` to listen for the event you created above with the following content:

```
pipeline {
    agent none
    triggers {
        eventTrigger(event(generic('beeEvent')))
    }
    stages {
        stage('Event Trigger') {
            when {
                expression { 
                    return currentBuild.rawBuild.getCause(com.cloudbees.jenkins.plugins.pipeline.events.EventTriggerCause)
                }
            }
            steps {
                echo 'triggered by published event'
            }
        }
    }
}
```

After creating both of these Pipeline jobs you will need to run the **Event Trigger** job once so that the trigger is registered. Once that is complete, click on **Build Now** to run the **Publish Event** job. Once that job has completed, the **Event Trigger** job will be triggered after a few seconds. The logs will show that the job was triggered by an `Event Trigger` and the `when` expression will be true.

### Cross-Master Events

For the second part of **Cross Team Collaboration** we will create an event that will be published **across Team Masters** via CloudBees Operations Center. The Cross Team Collaboration feature has a configurable router for routing events and we will change the router used for this exercise. You will need to select a partner to work with - one person will be the notifier and the other person will update their **event-trigger** job to be triggered when the notifier's job is run.

1. First you need to update the **Notification Router Implementation** to use the **Operations Center Messaging** router by clicking on the **Manage Jenkins** link - on the left side at the root of your Team Master (classic ui).
2. Next, scroll down and click on **Configure Notification** link.
3. Under **Notification Router Implementation** select the **Operations Center Messaging** option as opposed to the currently selected **Local only** option.
4. Now, the trigger job of the second partner needs to be updated to listen for the notifier's `event` string - ask your partner what their event string is - and then run the job once to register the trigger.
5. Next, the notifier will run their `notify-event` job and you will see the `notify-trigger` job get triggered.


## Exercise 4.3 - Custom Marker Files

In the following exercise we are going to demonstrate how you can use the Custom Marker feature of CloudBees Jenkins Enterprise to assign pipeline to a job based on an arbitrary file name like pom.xml.

In order to complete the following exercise you will need to fork the following repository into the Github organization you created in **Exercise 3.4**:

* https://github.com/PipelineHandsOn/maven-project

Once that repository is forked:

1. Click on the Github organization project you created in **Exercise 3.4**.
2. Click on **Configure**
3. Under **Project Recognizers** select **Custom Script**
4. In **Marker file** type ```pom.xml```
5. Under **Pipeline** - **Definition** select **Pipeline script from SCM**
6. Select **Git** from **SCM**
7. In **Repository URL** enter: ```https://github.com/PipelineHandsOn/custom-marker-files```
8. Select your credentials from the **Credentials** menu
9. In **Script path** enter: ```Jenkins-pom```
10. Click on **Save**
11. Click on **Scan Organization Now**

When the scan is complete your **Github Organization** project should now have both the **sample-rest-server** project and the **maven-project**.

