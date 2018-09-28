---

copyright:
  years: 2017, 2018
lastupdated: "2018-09-26"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:download: .download}

# Setting up the Terraform CLI and the IBM Cloud Provider plug-in
{: #setup_cli}

Before you can automate your {{site.data.keyword.Bluemix_notm}} resource provisioning, you must install the Terraform CLI and the {{site.data.keyword.Bluemix_notm}} Provider plug-in.
{: shortdesc}

## Installing the Terraform CLI and the IBM Cloud Provider plug-in
{: #install}

To use Terraform to manage {{site.data.keyword.Bluemix_notm}} resources, you must install the Terraform CLI and the {{site.data.keyword.Bluemix_notm}} Provider plug-in for Terraform. 
{: shortdesc}

**What is the {{site.data.keyword.Bluemix_notm}} Provider plug-in and why do I need it?**</br>
To support a multi-cloud approach, Terraform works with cloud providers. A cloud provider is responsible for understanding the resources that you can provision, their API, and the methods to expose these resources in the cloud. To provision resources in {{site.data.keyword.Bluemix_notm}}, you must install the {{site.data.keyword.Bluemix_notm}} Provider plug-in for Terraform. The plug-in is aware of the {{site.data.keyword.Bluemix_notm}} resources that you can provision with Terraform and the syntax to describe them.  

1. Install Terraform on your local machine. 
   1. Create a folder on your local system that is called `terraform` and navigate into your folder. 
      ```
      mkdir terraform && cd terraform
      ```
      {: pre}

   2. [Download the Terraform binary to your local machine ![External link icon](../icons/launch-glyph.svg "External link icon")](https://www.terraform.io/downloads.html). 

   3. Unzip the Terraform package and copy the binary into your `terraform` directory. 
   4. Point the `$PATH` environment variable to your Terraform binary.
      ```
      export PATH=$PATH:$HOME/terraform
      ```
      {: pre}
      
   5. Verify that the installation is successful by using a `terraform` command.
      ```
      terraform
      ```
      {: pre}
      
      Example output: 
      ```
      Usage: terraform [-version] [-help] <command> [args]

      The available commands for execution are listed below.
      The most common, useful commands are shown first, followed by less common or more advanced commands. If you're just getting started with Terraform, stick with the common commands. For the other commands, please read the help and docs before usage.

      Common commands:
          apply              Builds or changes infrastructure
          console            Interactive console for Terraform interpolations
          destroy            Destroy Terraform-managed infrastructure
          env                Workspace management
          fmt                Rewrites config files to canonical format
          get                Download and install modules for the configuration
          graph              Create a visual graph of Terraform resources
          import             Import existing infrastructure into Terraform
          init               Initialize a Terraform working directory
          output             Read an output from a state file
          plan               Generate and show an execution plan
          providers          Prints a tree of the providers used in the configuration
          push               Upload this Terraform module to Atlas to run
          refresh            Update local state file against real resources
          show               Inspect Terraform state or plan
          taint              Manually mark a resource for recreation
          untaint            Manually unmark a resource as tainted
          validate           Validates the Terraform files
          version            Prints the Terraform version
          workspace          Workspace management

      All other commands:
          debug              Debug output management (experimental)
          force-unlock       Manually unlock the terraform state
          state              Advanced state management
      ```
      {: screen}  

2. Install the {{site.data.keyword.Bluemix_notm}} Provider plug-in for Terraform. 
   1. [Download the latest version of the {{site.data.keyword.Bluemix_notm}} Provider binary ![External link icon](../icons/launch-glyph.svg "External link icon")](https://github.com/IBM-Cloud/terraform-provider-ibm/releases). 
   2. Create a hidden folder for your plug-in. The {{site.data.keyword.Bluemix_notm}} Provider plug-in is used only by the Terraform CLI and is not meant to be accessed by the user.  
      ```
      mkdir $HOME/.terraform.d/plugins
      ```
      {: pre}
      
   3. Move the {{site.data.keyword.Bluemix_notm}} Provider plug-in into your hidden folder. 
      ```
      mv $HOME/Downloads/terraform-provider-ibm* $HOME/.terraform.d/plugins/
      ```
      {: pre}
      
   4. Navigate into your hidden directory and verify that the installation is complete. 
      ```
      cd $HOME/.terraform.d/plugins && ./terraform-provider-ibm_*
      ```
      {: pre}
      
      Example output: 
      ```
      2018/09/25 17:30:14 {{site.data.keyword.Bluemix_notm}} Provider version 0.11.3  fdc4aa0f0547177f3ea8b14c7a58a849e240f64a
      This binary is a plugin. These are not meant to be executed directly.
      Please execute the program that consumes these plugins, which will load any plugins automatically
      ```
      {: screen}

