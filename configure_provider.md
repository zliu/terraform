---

copyright:
  years: 2018
lastupdated: "2018-09-28"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:download: .download}

# Configuring the IBM Cloud Provider plug-in
{: #configure_provider}

Terraform uses the {{site.data.keyword.Bluemix_notm}} Provider plug-in to securely communicate with the {{site.data.keyword.Bluemix_notm}} REST API. To securely access {{site.data.keyword.Bluemix_notm}}, you must configure your {{site.data.keyword.Bluemix_notm}} Provider plug-in to use your {{site.data.keyword.Bluemix_notm}} credentials for authentication.

## Retrieving your IBM Cloud credentials
{: #retrieve_credentials}

Before you can configure the {{site.data.keyword.Bluemix_notm}} Provider plug-in, you must retrieve your {{site.data.keyword.Bluemix_notm}} platform and infrastructure credentials. 
{: shortdesc}

**What credentials do I need?**</br>
The credentials that you need depend on the type of resource that you want to provision. For example, to provision infrastructure resources, you must provide your {{site.data.keyword.Bluemix_notm}} infrastructure credentials. Other resources, such as Cloud Foundry services, require an {{site.data.keyword.Bluemix_notm}} platform API key and the Cloud Foundry org and space where you want to provision the service. 

1. To provision {{site.data.keyword.Bluemix_notm}} platform services, create an {{site.data.keyword.Bluemix_notm}} plaform API key. Note the **API Key** that is created for you. 
   ```
   ibmcloud iam api-key-create <apikey_name>
   ```
   {: pre}
   
   Example output: 
   ```
   Creating API key mykey as user@company.com...
   OK
   API key mykey was created

   Please preserve the API key! It cannot be retrieved after it's created.
                 
   Name          mykey   
   Description      
   Created At    2018-09-27T20:22+0000   
   API Key       a1BcdEfghIJkLmNopqrSTUV45W-ABC12DefGH3ij2klm   
   Locked        false   
   UUID          ApiKey-1a2v3c45-ab1c-1a2b-1234-a123b456712
   ```
   {: screen}
   
   Your API key is displayed in the **API Key** section of your CLI output. 
   
2. To provision Cloud Foundry services, retrieve the Cloud Foundry org and space where you want to create your service.
   ```
   ibmcloud target --cf
   ```
   {: pre}
      
3. To provision infrastructure resources such as virtual servers or Kubernetes clusters, retrieve your {{site.data.keyword.Bluemix_notm}} infrastructure user name and API key. 
   1. Log in to the [{{site.data.keyword.Bluemix_notm}} infrastructure portal ![External link icon](../icons/launch-glyph.svg "External link icon")](https://control.bluemix.net/).
   2. From the expanding menu, select **Infrastructure**.
   3. From the menu bar, select **Account** > **Users** > **User List**.
   4. Find the user whose user name and API key you want to retrieve. 
   5. Click **Generate** to generate an API key or **View** to view your existing API key. A pop-up window opens that shows the infrastructure user name and API key. 
   
## Preparing the credentials for Terraform
{: prepare_credentials}

Choose an option for how to provide your {{site.data.keyword.Bluemix_notm}} credentials to Terraform. 
{: shortdesc} 

Want to set up a cloud environment that includes multiple cloud providers? Include the required credentials for each cloud provider. 
{: tip}

Before you begin, [retrieve your {{site.data.keyword.Bluemix_notm}} credentials](#retrieve_credentials). 

To prepare your credentials: 

1. Create a folder on your local machine for your Terraform project and navigate into the folder. This folder is used to store all configuration files and variable definitions.
   ```
   mkdir myproject && cd myproject
   ```
   {: pre}

2. Create a Terraform configuration file that is named `terraform.tfvars` to store the credentials that you retrieved. Variables defined in the `terraform.tfvars` file are automatically loaded by Terraform when the Terraform CLI is initialized. 
   ```
   softlayer_username = "<infrastructure_username>"
   softlayer_api_key = "<infrasturcture_apikey>"
   bluemix_api_key = "<platform_api_key>"
   org_name = "<cf_org>"
   space_name = "<cf_space>"
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
   <td>Enter the infrastructure API key that you retrieved earlier. </td>
   </tr>
   <tr>
   <td><code>bluemix_api_key</code></td>
   <td>Enter the {{site.data.keyword.Bluemix_notm}} platform API key that you retrieved earlier. </td>
   </tr>
   <tr>
   <td><code>org_name</code></td>
   <td>Enter the Cloud Foundry organization that you retrieved earlier. </td>
   </tr>
   <tr>
   <td><code>space_name</code></td>
   <td>Enter the Cloud Foundry space that you retrieved earlier.</td>
   </tr>  
   </tbody>
   </table>
   
3. Create a Terraform provider configuration file that is named `provider.tf`. Use this file to specify the {{site.data.keyword.Bluemix_notm}} provider and reference the credentials from your `terraform.tfvars` file that you want to provide to Terraform.
   ```
   provider "ibm" {
   bluemix_api_key    = "${var.bluemix_api_key}"
   softlayer_username = "${var.softlayer_username}"
   softlayer_api_key  = "${var.softlayer_api_key}"
   org_name = "${var.org_name}"
   space_name = "${var.space_name}"
   }
   ```
   {: codeblock}
  
Now that you are all set, you can go ahead and start [specifying and provisioning your {{site.data.keyword.Bluemix_notm}} resources](manage_resources.html#manage_resources) with Terraform. 

