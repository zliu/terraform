---

copyright:
  years: 2017, 2018
lastupdated: "2018-11-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:download: .download}


# Getting started with Terraform 
{: #index}

Terraform is an Open Source software that is developed by HashiCorp that enables predictable and consistent provisioning of {{site.data.keyword.Bluemix_notm}} resources by using a high-level scripting language. You can use Terraform to automate your {{site.data.keyword.Bluemix_notm}} resource provisioning, rapidly build complex, multi-tier cloud environments, and enable Infrastructure as Code (IaC).  
{: shortdesc}

**How does it work?**</br>
Let's say you want to spin up multiple copies of your service that uses a cluster of virtual servers, a load balancer, and a database server. You could learn how to create each resource, review the API or the commands that you need, and write a bash script to spin up these components. But it's easier, faster, and more orderly to specify the type of resource that you want and let Terraform do it all for you. 

The Terraform configuration files describe the components that you need. Based on your configuration, Terraform creates an execution plan and describes the actions that need to be executed to get to the desired state. You can review the execution plan, change it, or simply execute the plan. When you change your configuration, Terraform can determine what changed and create incremental execution plans that you can apply to your {{site.data.keyword.Bluemix_notm}} resources. 

**What do I need to get started?**</br>
To provision {{site.data.keyword.Bluemix_notm}} infrastructure and platform resources, you must have a [Pay-As-You-Go or Subscription {{site.data.keyword.Bluemix_notm}} account ![External link icon](../icons/launch-glyph.svg "External link icon")](https://console.bluemix.net/registration/). 

Sounds great? Get started by installing the Terraform CLI and the {{site.data.keyword.Bluemix_notm}} Provider plug-in. Then, configure your desired {{site.data.keyword.Bluemix_notm}} resources and watch Terraform spin them up. 

## Installing the Terraform CLI and the IBM Cloud Provider plug-in
{: #install}

To use Terraform to manage {{site.data.keyword.Bluemix_notm}} resources, you must install the Terraform CLI and the {{site.data.keyword.Bluemix_notm}} Provider plug-in for Terraform. 
{: shortdesc}

**What is the {{site.data.keyword.Bluemix_notm}} Provider plug-in and why do I need it?**</br>
To support a multi-cloud approach, Terraform works with multiple cloud providers. A cloud provider is responsible for understanding the resources that you provision, their API, and the methods to expose these resources in the cloud. The {{site.data.keyword.Bluemix_notm}} Provider plug-in is aware of the {{site.data.keyword.Bluemix_notm}} resources that are supported and how you can configure and provision them. 

1. Install Terraform on your local machine. 
   1. Create a folder on your local system that is called `terraform` and navigate into your folder. 
      ```
      mkdir terraform && cd terraform
      ```
      {: pre}

   2. [Download the Terraform binary to your local machine ![External link icon](../icons/launch-glyph.svg "External link icon")](https://www.terraform.io/downloads.html). 

   3. Extract the Terraform package and copy the binary into your `terraform` directory. 
   4. Point the `$PATH` environment variable to your Terraform binary.
      ```
      export PATH=$PATH:$HOME/terraform
      ```
      {: pre}
      
   5. Verify that the installation is successful. 
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

2. Install the {{site.data.keyword.Bluemix_notm}} Provider plug-in. 
   1. [Download the latest version of the {{site.data.keyword.Bluemix_notm}} Provider binary ![External link icon](../icons/launch-glyph.svg "External link icon")](https://github.com/IBM-Cloud/terraform-provider-ibm/releases). 
   2. Create a hidden folder for your plug-in. 
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

      
## Configuring the IBM Cloud Provider plug-in
{: #cloud_provider_config}

Terraform uses the {{site.data.keyword.Bluemix_notm}} Provider plug-in to securely communicate with the {{site.data.keyword.Bluemix_notm}} REST API. To securely access {{site.data.keyword.Bluemix_notm}}, you must configure your {{site.data.keyword.Bluemix_notm}} Provider plug-in to use your {{site.data.keyword.Bluemix_notm}} credentials for authentication. 
{: shortdesc}

**What credentials do I need?**</br>
The credentials that you need depend on the type of resource that you want to provision. For example, to provision infrastructure resources, you must provide your {{site.data.keyword.Bluemix_notm}} infrastructure credentials. Other resources, such as Cloud Foundry services, require an {{site.data.keyword.Bluemix_notm}} platform API key and the Cloud Foundry org and space where you want to provision the service. 

Before you begin, [install the {{site.data.keyword.Bluemix_notm}} CLI ![External link icon](../icons/launch-glyph.svg "External link icon")](/docs/cli/index.html#overview). 

1. Create a folder on your local machine for your first Terraform project and navigate into the folder. This folder is used to store all configuration files and variable definitions. 
   ```
   mkdir myproject && cd myproject
   ```
   {: pre}
      
2. Retrieve your {{site.data.keyword.Bluemix_notm}} infrastructure user name and API key. The example in this Getting Started tutorial shows how to provision a virtual server in {{site.data.keyword.Bluemix_notm}}. To provision this resource, you must configure the {{site.data.keyword.Bluemix_notm}} Provider to use your {{site.data.keyword.Bluemix_notm}} infrastructure credentials. Other {{site.data.keyword.Bluemix_notm}} resources might require other credentials to get provisioned successfully. For more information, see [Retrieving your {{site.data.keyword.Bluemix_notm}} credentials](configure_provider.html#retrieve_credentials).  
   1. Log in to the [{{site.data.keyword.Bluemix_notm}} infrastructure portal ![External link icon](../icons/launch-glyph.svg "External link icon")](https://control.bluemix.net/).
   2. From the menu ![Menu icon](../icons/icon_hamburger.svg "Menu icon"), select **Infrastructure**.
   3. From the menu bar, select **Account** > **Users** > **User List**.
   4. Find the user whose user name and API key you want to retrieve. 
   5. Click **Generate** to generate an API key or **View** to view your existing API key. A pop-up window opens that shows the infrastructure user name and API key. 
   
3. Create a Terraform configuration file that is named `terraform.tfvars` to store your {{site.data.keyword.Bluemix_notm}} infrastructure credentials. Make sure to save this file in the folder that you created for your first Terraform project. Variables that are defined in the `terraform.tfvars` file are automatically loaded by Terraform when the Terraform CLI is initialized. 
   ```
   softlayer_username = "<infrastructure_username>"
   softlayer_api_key = "<infrastructure_apikey>"
   ```
   {: codeblock}
   
   <table>
   <caption>Understanding the configuration file components</caption>
   <thead>
   <th colspan=2><img src="images/idea.png" alt="Idea icon"/> Understanding the configuration file components</th>
   </thead>
   <tbody>
   <tr>
   <td><code>softlayer_username</code></td>
   <td>Enter the infrastructure user name that you retrieved earlier.  </td>
   </tr>
   <tr>
   <td><code>softlayer_api_key</code></td>
   <td>Enter the infrastructure api key that you retrieved earlier. </td>
   </tr>
   </tbody>
   </table>
   
4. Create a Terraform provider configuration file that is named `provider.tf`. Use this file to specify the cloud provider that you want to use and reference the credentials from your `terraform.tfvars` file to access cloud provider resources. 
   ```
   variable "softlayer_username" {}
   variable "softlayer_api_key" {}

   provider "ibm" {
    region = "us-south"
    softlayer_username = "${var.softlayer_username}"
    softlayer_api_key  = "${var.softlayer_api_key}"
   }
   ```
   {: codeblock}
   

## Using a configuration file to provision a virtual server in IBM Cloud
{: #sample_infrastructure_config}

Now that you are all set, you can start provisioning resources in {{site.data.keyword.Bluemix_notm}}.
{: shortdesc}

The following example shows how to specify and provision a virtual server in {{site.data.keyword.Bluemix_notm}}. For specifying different types of resources, see the [{{site.data.keyword.Bluemix_notm}} resource reference ![External link icon](../icons/launch-glyph.svg "External link icon")](https://ibm-cloud.github.io/tf-ibm-docs/). 

**Important:** The following example shows how you can configure a virtual server in {{site.data.keyword.Bluemix_notm}} by using JSON syntax. A virtual server is an {{site.data.keyword.Bluemix_notm}} infrastructure resource that incurs costs. Be sure to review the [pricing calculator ![External link icon](../icons/launch-glyph.svg "External link icon")](https://console.bluemix.net/pricing/configure/iaas/virtual-server-group) before you proceed.

1. Create a configuration file that is named `sample.tf` with the following content. Store this file in the folder that you created earlier. 
   ```
   resource "ibm_compute_vm_instance" "vm1" {
   hostname = "vm1"
   domain = "example.com"
   os_reference_code = "DEBIAN_8_64"
   datacenter = "dal09"
   network_speed = 10
   hourly_billing = true
   private_network_only = false
   cores = 1
   memory = 1024
   disks = [25]
   local_disk = false
   }
   ```
   {: codeblock}
   
2. Initialize Terraform. 
   ```
   terraform init
   ```
   {: pre}
   
   Example output: 
   ```
   Initializing provider plugins...

   The following providers do not have any version constraints in configuration, so the latest version was installed.

   To prevent automatic upgrades to new major versions that may contain breaking changes, it is recommended to add version = "..." constraints to the corresponding provider blocks in configuration, with the constraint strings suggested below.

   * provider.ibm: version = "~> 0.11"

   Terraform has been successfully initialized!

   You may now begin working with Terraform. Try running "terraform plan" to see any changes that are required for your infrastructure. All Terraform commands should now work.

   If you ever set or change modules or backend configuration for Terraform, rerun this command to reinitialize your working directory. If you forget, other commands will detect it and remind you to do so if necessary.
   ```
   {: screen}
   
3. Generate a Terraform execution plan. When you execute this command, Terraform validates the syntax of your configuration file and resource definitions against the specifications that are provided by the {{site.data.keyword.Bluemix_notm}} Provider plug-in. 
   ```
   terraform plan
   ```
   {: pre}

   Example output: 
   ```
   Refreshing Terraform state in-memory prior to plan...
   The refreshed state will be used to calculate this plan, but will not be persisted to local or remote state storage.

   An execution plan has been generated and is shown below.
   Resource actions are indicated with the following symbols:
     + create

   Terraform will perform the following actions:

     + ibm_compute_vm_instance.vm1
         id:                           <computed>
         block_storage_ids.#:          <computed>
         cores:                        "1"
         datacenter:                   "dal09"
         disks.#:                      "1"
         disks.0:                      "25"
         domain:                       "example.com"
         file_storage_ids.#:           <computed>
         hostname:                     "vm1"
         hourly_billing:               "true"
         ip_address_id:                <computed>
         ip_address_id_private:        <computed>
         ipv4_address:                 <computed>
         ipv4_address_private:         <computed>
         ipv6_address:                 <computed>
         ipv6_address_id:              <computed>
         ipv6_enabled:                 "false"
         ipv6_static_enabled:          "false"
         local_disk:                   "false"
         memory:                       "1024"
         network_speed:                "10"
         os_reference_code:            "DEBIAN_8_64"
         private_interface_id:         <computed>
         private_network_only:         "false"
         private_security_group_ids.#: <computed>
         private_subnet:               <computed>
         private_subnet_id:            <computed>
         private_vlan_id:              <computed>
         public_bandwidth_limited:     <computed>
         public_bandwidth_unlimited:   "false"
         public_interface_id:          <computed>
         public_ipv6_subnet:           <computed>
         public_ipv6_subnet_id:        <computed>
         public_security_group_ids.#:  <computed>
         public_subnet:                <computed>
         public_subnet_id:             <computed>
         public_vlan_id:               <computed>
         secondary_ip_addresses.#:     <computed>
         wait_time_minutes:            "90"

   Plan: 1 to add, 0 to change, 0 to destroy.
   ------------------------------------------------------------------------
   Note: You didn't specify an "-out" parameter to save this plan, so Terraform can't guarantee that exactly these actions will be performed if "terraform apply" is subsequently run.
   ```
   {: screen}
   
4. Review the execution plan to verify the type of resource that is planned to be provisioned by Terraform.

5. Create your virtual server. Confirm the creation by entering **yes** when prompted. 
   ```
   terraform apply
   ```
   {: pre} 
   
   Example output: 
   ```
   Creating...
     block_storage_ids.#:        "" => "<computed>"
     cores:                      "" => "1"
     datacenter:                 "" => "dal09"
     disks.#:                    "" => "1"
     disks.0:                    "" => "25"
     domain:                     "" => "example.com"
     file_storage_ids.#:         "" => "<computed>"
     hostname:                   "" => "vm1"
     hourly_billing:             "" => "true"
     ip_address_id:              "" => "<computed>"
     ip_address_id_private:      "" => "<computed>"
     ipv4_address:               "" => "<computed>"
     ipv4_address_private:       "" => "<computed>"
     ipv6_address:               "" => "<computed>"
     ipv6_address_id:            "" => "<computed>"
     ipv6_enabled:               "" => "false"
     local_disk:                 "" => "false"
     memory:                     "" => "1024"
     network_speed:              "" => "10"
     os_reference_code:          "" => "DEBIAN_8_64"
     private_network_only:       "" => "false"
     private_subnet:             "" => "<computed>"
     private_vlan_id:            "" => "<computed>"
     public_bandwidth_limited:   "" => "<computed>"
     public_bandwidth_unlimited: "" => "false"
     public_ipv6_subnet:         "" => "<computed>"
     public_subnet:              "" => "<computed>"
     public_vlan_id:             "" => "<computed>"
     secondary_ip_addresses.#:   "" => "<computed>"
     wait_time_minutes:          "" => "90"
   ibm_compute_vm_instance.vm1: Still creating... (10s elapsed)
   ibm_compute_vm_instance.vm1: Still creating... (20s elapsed)
   ibm_compute_vm_instance.vm1: Still creating... (30s elapsed)
   ibm_compute_vm_instance.vm1: Still creating... (40s elapsed)
   ibm_compute_vm_instance.vm1: Still creating... (50s elapsed)
   ibm_compute_vm_instance.vm1: Still creating... (1m0s elapsed)
   ibm_compute_vm_instance.vm1: Creation complete after 1m04s (ID: 62364997)
   
   Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
   ```
   {: screen}
   
6. List the virtual server that is provisioned. 
   ```
   terraform show
   ```
   {: pre}
   
   Example output: 
   ```
   ibm_compute_vm_instance.vm1:
     id = 62364997
     block_storage_ids.# = 0
     cores = 1
     datacenter = dal09
     dedicated_acct_host_only = false
     disks.# = 1
     disks.0 = 25
     domain = example.com
     file_storage_ids.# = 0
     hostname = vm1
     hourly_billing = true
     ip_address_id = 120354689
     ip_address_id_private = 120356235
     ipv4_address = 169.53.33.54
     ipv4_address_private = 10.120.45.183
     ipv6_enabled = false
     local_disk = false
     memory = 1024
     network_speed = 10
     notes = 
     os_reference_code = DEBIAN_8_64
     private_network_only = false
     private_subnet = 10.120.45.128/26
     private_vlan_id = 2451153
     public_bandwidth_unlimited = false
     public_subnet = 169.53.33.48/28
     public_vlan_id = 2451151
     secondary_ip_addresses.# = 0
     wait_time_minutes = 90
   ```
   {: screen}
   

**What's next?** </br>
Now that you provisioned your first virtual server, [explore other {{site.data.keyword.Bluemix_notm}} resources ![External link icon](../icons/launch-glyph.svg "External link icon")](https://ibm-cloud.github.io/tf-ibm-docs/) that you can provision with Terraform. 
