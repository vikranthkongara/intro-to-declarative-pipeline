# Introduction to Declarative Pipelines

This repository contains the instructions and learning materials associated with a hands on training workshop called **Introduction to Declarative Pipelines** that is designed to teach the following key concepts:

  * What are Jenkins Pipelines and why should you use them?
  * Why should you use [Declarative](https://jenkins.io/doc/book/pipeline/syntax/#declarative-pipeline) vs [Scripted](https://jenkins.io/doc/book/pipeline/syntax/#scripted-pipeline) pipelines?
  * What tools are available for creating pipelines?
  * How can you create a simple pipeline via Jenkins?
  * What are the key features of Declarative pipelines?
  * How do you create multibranch pipelinest?
  * How do you  create a pipeline using Blue Ocean's pipeline editor?
  * What are Distributed Pipelines - an architecture that distributes your Pipelines by providing per team Jenkins Masters
  * CloudBees Pipeline features that enable and enhance **Distributed Pipelines**
  
A PDF version of the presentation that accompanies this workshop can be found here: [Introduction%20to%20Declarative%20Pipeline%20-%20Hands%20On.pdf](Introduction%20to%20Declarative%20Pipeline%20-%20Hands%20On.pdf)

[**Set-up Instructions**](Setup.md)

**The exercises covered in the workshop are available at the following links:**

  * **Declarative Syntax Basics**: [Exercise-01.md](Exercise-01.md)
  * **Declarative Advanced Syntax**: [Exercise-02.md](Exercise-02.md)
  * **Distributed Pipelines with Pipeline As Code**: [Exercise-03.md](Exercise-03.md)
  * **Distributed Pipelines with CloudBees**: [Exercise-04.md](Exercise-04.md)

**Important Note**: The training content contained in this workshop is kept up to date with the latest releases of pipeline plugins and not all features will be available in Jenkins environments that are not updated regulary or within CAP (the CloudBees Assurance Program). The following are the currently required Pipeline plugin versions:

 -  **pipeline-model-definition** ***1.2.6***

# Training Prerequisites

In order to follow along with the hands on portion of the workshop students should have the following resources available to them:

  * Internet access
  * An account on Github.com and a basic understanding of how to use Github to do things like fork a repository, edit files in the web UI, and create pull requests
  * A personal access token for your Github account ([Github-Personal-Access-Token.md](Github-Personal-Access-Token.md)) with the following permissions:
    - repo: all
    - admin:repo_hook: all
    - admin:org_hook
    - user: all
  * Access to a Jenkins server
  
Detailed set-up instructions are available at **[Setup](Setup.md)**

# Revision History

 See: [Releases](https://github.com/PipelineHandsOn/intro-to-declarative-pipeline/releases)

# License

The content in this repository (with the important exception of the PDF, see note below) is Open Source material released under the Apache 2.0 License. Please see the [LICENSE](LICENSE) file for full license details.

**Important Note**: The ```Introduction to Declarative Pipeline - Hands On.pdf``` file contains corporate identify materials that are proprietary to CloudBees, Inc. that cannot be reused without written permission from CloudBees, Inc. 

# Disclaimer

Although the code in this repository was originally created by employees of CloudBees, Inc. to use in training customers your use of this material is not sponsored or supported by CloudBees, Inc.

# Contributors 

* Contributor: [Kurt Madel](https://github.com/kmadel)
* Contributor: [Brent McConnell](https://github.com/brentmcconnell)
 
# Questions, Feedback, Pull Requests Etc.

If you have any questions, feedback, suggestions, etc. please submit them via issues or, even better, submit a Pull Request!
 
