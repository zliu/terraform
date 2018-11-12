---

copyright:
  years: 2017, 2018
lastupdated: "2018-10-26"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:download: .download}

# Tutorial: Deploying Red Hat OpenShift Container Platform on IBM Cloud
{: #redhat}

Use this tutorial to create a highly available Red Hat® OpenShift Container Platform 3.9 environment on top of IBM® Cloud infrastructure. 
{: shortdesc}

[Red Hat® OpenShift Container Platform ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.openshift.com/products/container-platform/) is built around a core of containers, with orchestration and management provided by Kubernetes, on a foundation of Atomic Host and Red Hat® Enterprise Linux. OpenShift Origin is the community distribution of Kubernetes that is optimized for continuous app development and multi-tenant deployment. The community project provides developer and operations-centric tools on top of Kubernetes to enable rapid app development, deployment, scaling, and long-term app lifecycle maintenance. 

This tutorial shows how you can set up OpenShift Container Platform 3.9 on top of {{site.data.keyword.Bluemix_notm}} to take advantage of the high availability capabilities of native Kubernetes and {{site.data.keyword.Bluemix_notm}} to create a highly available OpenShift Container Platform 3.9 environment. Review the following image to find an architectural overview of the infrastructure components that are needed for the Red Hat OpenShift Container Platform to work properly.

<img src="../images/infra-diagram.png" alt="Infrastructure components for the Red Hat® OpenShift Container Platform on {{site.data.keyword.Bluemix_notm}}" width="800" style="width: 800px; border-style: none"/>

When you complete this tutorial, the following infrastructure components are provisioned for you: 
- 1 OpenShift Container Platform master node
- 3 OpenShift Container Platform infrastructure nodes
- 2 OpenShift Container Platform application nodes
- 1 OpenShift Container Platform Bastion node
- Native {{site.data.keyword.Bluemix_notm}} infrastructure services, such as VLANs, security groups, and network gateways

## Objectives
{: #objectives}

In this tutorial, you set up Red Hat OpenShift Container Platform version 3.9 on top of {{site.data.keyword.Bluemix_notm}}  infrastructure and deploy your first `nginx` app in the OpenShift cluster. In particular, you will: 

- Set up your environment and all the software that you need for your Red Hat OpenShift Container Platform installation, such as Terraform, {{site.data.keyword.Bluemix_notm}} Provider plug-in, and the Terraform OpenShift project. 
- Configure the {{site.data.keyword.Bluemix_notm}} Provider plug-in and define your Red Hat OpenShift Container Platform infrastructure components.
- Provision {{site.data.keyword.Bluemix_notm}} infrastructure for your Red Hat OpenShift Container Platform components by using Terraform. 
- Install Red Hat OpenShift Container Platform on top of {{site.data.keyword.Bluemix_notm}} infrastructure. 
- Store the `nginx` image in your internal Docker registry. 
- Deploy the `nginx` app in your OpenShift cluster and expose this app to the public. 

## Time required
{: #time}

60 minutes

## Audience
{: #audience}

This tutorial is intended for network administrators who want to deploy Red Hat OpenShift Container Platform on top of {{site.data.keyword.Bluemix_notm}} and for software developers who want to create their first app in the OpenShift cluster. 

## Prerequisites
{: #prerequisites}

- If you do not have one, create an {{site.data.keyword.Bluemix_notm}} [Pay-As-You-Go or Subscription {{site.data.keyword.Bluemix_notm}} account ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://console.bluemix.net/registration/). 
- Make sure that you have an existing [Red Hat account ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://sso.redhat.com/auth/realms/redhat-external/protocol/saml?SAMLRequest=fZLLTsMwEEV%2FJTuvXCelr1hJpKgVUqWCUAss2CDXnZBIfgSPQwtfj5Oqomy69Ojec%2BfhDIVWLS87X5stfHaAPioRwfnGmqU12GlwO3BfjYSX7SYntfctcsaElIA4cnCohR9Jq5kWRnyABuNZD2VVY4RqfoBEq0ANjx75B0C0124ROmAOhNLIzmUKJw8uMFjrrLfSqoFLovUqJ%2B9JFUuIZ5KmM5B0Mk6BpkkaU0inYjGphNind0GK2MHaoBfG52QcJwuaxDRePCdzPkn5dP5GoldwOHQ2HsUkOmllkPdBOemc4VZgg9wIDci95LvyYcODkIvLkq4t7W3PZQ5SZL2aD925wtWo6RH2GbuuZufLPAbKevVkVSO%2Fo1Ipe1yGLXnIiXdd2O29dVr427l9pTnQapDytp8XfbgTYcU58%2F8HKH4B&RelayState=https%3A%2F%2Faccess.redhat.com%2Fmanagement%2Fsubscriptions&SigAlg=http%3A%2F%2Fwww.w3.org%2F2000%2F09%2Fxmldsig%23rsa-sha1&Signature=t4s738AUmTxKfEMZkNpOI8e1wz72ftoZ92HQIbqfs%2BShcdv3ShuJ4%2FIGIhuMYA%2BoaFZwaPcamWlo7F0VDtSN%2FHXcXj78e5s%2B99vJ3K39V4CYCmWOO3fFbpRIV5T0jxzwsp45YEeFKZd45zeQ0X2UwCxPw41JVOqq6NqIqMAJ0y%2Bb92nmE9fFMKlBCS4A%2BTHN1ub1YCUEvgKUNEOasyGdGYXHf0fh9NSUAHO8UJAPSnR0YmBLr4oWteeRuu5MkmqWxEx0F2FOIXtgncMjTsHhyqUllYKjK5%2Buf8YgbeU4ptZoniLmQEKzzrd1KJTutc3ce4W7X7h0zteTEqSdou7LLQ%3D%3D#active) that has an [active Openshift subscription ![External link icon](../icons/launch-glyph.svg "External link icon")](https://access.redhat.com/products/red-hat-openshift-container-platform). 
- Install [Docker and the {{site.data.keyword.Bluemix_notm}} CLI ![External link icon](../../icons/launch-glyph.svg "External link icon")](/docs/cli/index.html#overview). 

## Lesson 1: Configure your environment
{: #configure environment}

In this tutorial, you provision {{site.data.keyword.Bluemix_notm}} infrastructure for the Red Hat OpenShift Container Platform by using Terraform. Before you can start the infrastructure provisioning process, you must ensure that you set up Terraform, the {{site.data.keyword.Bluemix_notm}} Provider plug-in, and the Terraform OpenShift project. 
{: shortdesc}

1. Create a Docker container that installs Terraform and the {{site.data.keyword.Bluemix_notm}} Provider plug-in. To execute Terraform commands, you must be logged in to the container. 
   You can also [install Terraform and the {{site.data.keyword.Bluemix_notm}} Provider plug-in](../setup_cli.html#setup_cli) on your local machine to run Terraform commands without using a Docker container. 
   {: tip}
   1. Download the latest version of the Docker image for Terraform and the {{site.data.keyword.Bluemix_notm}} Provider plug-in to your local machine. 
      ```
      docker pull ibmterraform/terraform-provider-ibm-docker
      ```
      {: pre}
   
      Example output: 
      ```
      Using default tag: latest
      latest: Pulling from ibmterraform/terraform-provider-ibm-docker
      911c6d0c7995: Pull complete 
      fed331e93a76: Pull complete 
      82a1ea1a0cd7: Pull complete 
      a4b4f00ab356: Pull complete 
      78858415d97d: Pull complete 
      515c9be5f236: Pull complete 
      94021f117e26: Pull complete 
      a50b454f6bba: Pull complete 
      dd63d43987e3: Pull complete 
      a098dba94337: Pull complete  
      Digest: sha256:df316f5ed26cbec1bc1ad7a6f6d2c978f767408080a4a4db954c94c91e8271e5
      Status: Downloaded newer image for ibmterraform/terraform-provider-ibm-docker:latest
      ```
      {: screen}
   
   2. Create a container from your image and log in to your container. When the container is created, Terraform and the {{site.data.keyword.Bluemix_notm}} Provider plug-in are automatically installed. 
      ```
      docker run -it ibmterraform/terraform-provider-ibm-docker:latest
      ```
      {: pre}
   
2. Set up the IBM Terraform Openshift Project.
   1. Install OpenSSH inside the container that you created in the previous step. 
      ```
      apk add --no-cache openssh
      ```
      {: pre}
   
      Example output: 
      ```
      fetch http://dl-cdn.alpinelinux.org/alpine/v3.7/main/x86_64/APKINDEX.tar.gz
      fetch http://dl-cdn.alpinelinux.org/alpine/v3.7/community/x86_64/APKINDEX.tar.gz
      (1/6) Installing openssh-keygen (7.5_p1-r9)
      (2/6) Installing openssh-client (7.5_p1-r9)
      (3/6) Installing openssh-sftp-server (7.5_p1-r9)
      (4/6) Installing openssh-server-common (7.5_p1-r9)
      (5/6) Installing openssh-server (7.5_p1-r9)
      (6/6) Installing openssh (7.5_p1-r9)
      Executing busybox-1.27.2-r11.trigger
      OK: 326 MiB in 50 packages
      ```
      {: screen}
   
   2. Download the Terraform configuration files to deploy the Red Hat OpenShift Container Platform. 
      ```
      git clone https://github.com/IBM-Cloud/terraform-ibm-openshift.git
      ```
      {: pre}
   
      Example output: 
      ```
      Cloning into 'terraform-ibm-openshift'...
      remote: Enumerating objects: 375, done.
      remote: Total 375 (delta 0), reused 0 (delta 0), pack-reused 375
      Receiving objects: 100% (375/375), 681.75 KiB | 1.22 MiB/s, done.
      Resolving deltas: 100% (190/190), done.
      ```
      {: screen}

   3. Navigate into the installation directory. 
      ```
      cd terraform-ibm-openshift
      ```
      {: pre}

3. Generate an SSH key. The SSH key is used to access {{site.data.keyword.Bluemix_notm}} infrastructure resources during provisioning.  
   1. Create an SSH key inside the container that you created earlier. Enter the email address that you want to associate with your SSH key. Make sure to accept the default file name, file location, and missing passphrase by pressing **Enter**.
      ```
      ssh-keygen -t rsa -b 4096 -C "<email_address>"
      ```
      {: pre}
   
      Example output: 
      ```
      Generating public/private rsa key pair.
      Enter file in which to save the key (/root/.ssh/id_rsa): 
      Created directory '/root/.ssh'.
      Enter passphrase (empty for no passphrase): 
      Enter same passphrase again: 
      Your identification has been saved in /root/.ssh/id_rsa.
      Your public key has been saved in /root/.ssh/id_rsa.pub.
      The key fingerprint is:
      SHA256:67LDt8zjbPoX+uKFGrVs2CrsyNk1izzBOkDf8tBb3Xc myemail@example.com
      The key's randomart image is:
      +---[RSA 4096]----+
      |                 |
      |                 |
      |                 |
      | .               |
      |. ..o   S .      |
      |.  +oo * =.. . E |
      | . o+oB B.... .  |
      | .o=+=+%*..      |
      |  +o=o*@X*.      |
      +----[SHA256]-----+
      ```
      {: screen}

   2. Verify that the SSH key is created successfully. The creation is successful if you can see one **id_rsa** and one **id_rsa.pub** file. 
      ```
      cd /root/.ssh && ls
      ```
      {: pre}
   
      Example output: 
      ```
      id_rsa      id_rsa.pub
      ```
      {: screen}

4. Navigate back into your OpenShift installation directory. 
   ```
   cd /go/bin/terraform-ibm-openshift
   ```
   {: pre}
    
5. Open the Terraform `variables.tf` file and review the default values that are set in the file. The `variables.tf` file specifies all information that you want to pass on to Terraform during the provisioning of your infrastructure resources. You can change the default values, but do not add sensitive data, such as your infrastructure user name and API key, to this file. The `variables.tf` file is usually stored under version control and shared across users. 
   ```
   vi variables.tf
   ```
   {: pre}
   
   <table>
   <thead>
   <th>Variable name</th>
   <th>Description</th>
   <th>Default value</th>
   </thead>
   <tbody>
   <tr>
   <td><code>app_count</code></td>
   <td>Enter the number of app nodes that you want to create. App nodes are used to run your app pods.</td>
   <td>1</td>
   </tr>
   <tr>
   <td><code>app_lbass_name</code></td>
   <td>Enter the name of the local load balancer that you want to use to load balance incoming requests for your app nodes. </td>
   <td>openshift-app</td>
   </tr>
   <tr>
   <td><code>bastion_flavor</code></td>
   <td>Enter the flavor that you want to use for your Bastion virtual machine. The Bastion host is the only ingress point for SSH in the OpenShift cluster from external entities. When connecting to the OpenShift Container Platform infrastructure, the Bastion host forwards the request to the infrastructure or app server. For more information, see [Bastion instance ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://access.redhat.com/documentation/en-us/reference_architectures/2018/html/deploying_and_managing_openshift_3.9_on_google_cloud_platform/components_and_considerations#bastion_instance) </td>
   <td>B1_4X8X100</td>
   </tr>
   <tr>
   <td><code>datacenter</code></td>
   <td>Enter the zone where you want to provision your {{site.data.keyword.Bluemix_notm}} infrastructure. To find existing zones, run <code>ibmcloud ks zones</code>. </td>
   <td>mel01</td>
   </tr>
   <tr>
   <td><code>ibm_sl_api_key</code></td>
   <td>The {{site.data.keyword.Bluemix_notm}} infrastructure API key to access infrastructure resources. Do not enter this information in this file. Instead, you are prompted to enter this information when you create the infrastructure resources. </td>
   <td>n/a</td>
   </tr>
   <tr>
   <td><code>ibm_sl_username</code></td>
   <td>The {{site.data.keyword.Bluemix_notm}} infrastructure user name to access infrastructure resources. Do not enter this information in this file. Instead, you are prompted to enter this information when you create the infrastructure resources. </td>
   <td>n/a</td>
   </tr>
   <tr>
   <td><code>infra_count</code></td>
   <td>Enter the number of infrastructure nodes that you want to create. Infrastructure nodes are used to run infrastructure-related pods, such as router or registry pods. </td>
   <td>1</td>
   </tr>
   <tr>
   <td><code>infra_lbass_name</code></td>
   <td>Enter the name of the local load balacner that you want to use to load balance incoming requests for your infrastructure nodes. </td>
   <td>openshift-infra</td>
   </tr>
   <tr>
   <td><code>master_count</code></td>
   <td>Enter the number of master nodes that you want to create. The master runs the API server, controller manager server and etcd database instance.</td>
   <td>n/a</td>
   </tr>
   <tr>
   <td><code>rhn_password</code></td>
   <td>The [Red Hat Network password ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.redhat.com/apps/register/connect/) to access the OpenShift project. You can enter this information here or provide it as part of your OpenShift deployment in [Lesson 2](#provision_infrastructure).  </td>
   <td>n/a</td>
   </tr>
   <tr>
   <td><code>rhn_username</code></td>
   <td>The [Red Hat Network user name with OpenShift subscription![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.redhat.com/apps/register/connect/) to access the OpenShift project. You can enter this information here or provide it as part of your OpenShift deployment in [Lesson 2](#provision_infrastructure).  </td>
   <td>n/a</td>
   </tr>
   <tr>
   <td><code>private_vlanid</code></td>
   <td>Enter the VLAN ID of your existing private VLAN that you want to use. To find existing VLAN IDs, run <code>ibmcloud sl vlan list</code> and review the <strong>ID</strong> column. </td>
   <td>n/a</td>
   </tr>
   <tr>
   <td><code>public_vlanid</code></td>
   <td>Enter the VLAN ID of your existing public VLAN that you want to use. To find existing VLAN IDs, run <code>ibmcloud sl vlan list</code> and review the <strong>ID</strong> column. </td>
   <td>n/a</td>
   </tr>
   <tr>
   <td><code>ssh_label</code></td>
   <td>Enter a label to assign to your SSH key. </td>
   <td>ssh_key_openshift</td>
   </tr>
   <tr>
   <td><code>ssh_private_key</code></td>
   <td>Enter the path to the SSH private key that you created earlier. </td>
   <td><code>~/.ssh/id_rsa</code></td>
   </tr>
   <tr>
   <td><code>ssh_public_key</code></td>
   <td>Enter the path to the SSH public key that you created earlier. </td>
   <td><code>~/.ssh/id_rsa.pub</code></td>
   </tr>
   <tr>
   <td><code>subnet_size</code></td>
   <td>Enter the number of subnets that you want to be able to create with your public and private VLAN. This value is required only if you decide to create a new private and public VLAN pair. </td>
   <td>64</td>
   </tr>
   <tr>
   <td><code>vlan_count</code></td>
   <td>Enter <code>1</code> to automatically create a new private and public VLAN with your infrastructure, or `0` if you want to use existing VLANs. To find existing VLANs, run <code>ibmcloud sl vlan list</code>. The zone where your existing VLAN routers are provisioned is included in the <strong>primary_router</strong> column of your CLI output. </td>
   <td>1</td>
   </tr>
   <tr>
   <td><code>vm_domain</code></td>
   <td>Enter the domain name that you want to use for your virtual machine nodes. </td>
   <td><code>ibm.com</code></td>
   </tr>
   </tbody>
   </table>
   
## Lesson 2: Provision the IBM Cloud infrastructure for your Red Hat OpenShift cluster 
{: #provision_infrastructure}

Now that you prepared your environment, you can go ahead and provision {{site.data.keyword.Bluemix_notm}} infrastructure resources by using Terraform. 
{: shortdesc}

Before you begin, make sure that you are logged in to the container that you created in the previous lesson. 

1. Retrieve your {{site.data.keyword.Bluemix_notm}} infrastructure user name and API key.
   1. Log in to the [{{site.data.keyword.Bluemix_notm}} infrastructure portal ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://control.bluemix.net/).
   2. From the expanding menu, select **Infrastructure**.
   3. From the menu bar, select **Account** > **Users** > **User List**.
   4. Find the user whose user name and API key you want to retrieve.
   5. Click **Generate** to generate an API key or **View** to view your existing API key. In the pop-up window that opens, copy the infrastructure user name and API key.

2. Create the {{site.data.keyword.Bluemix_notm}} infrastructure components for your Red Hat OpenShift cluster. Enter the infrastructure user name and API key that you retrieved earlier when prompted. 
   ```
   make infrastructure
   ```
   {: pre}
   
   Example output: 
   ```
   ...

   Apply complete! Resources: 47 added, 0 changed, 0 destroyed.
   module.lbass_infra.ibm_lbass.infra_lbass: Still Creating....
   module.lbass_app.ibm_lbass.app_lbass: Still Creating....
   .....
   Apply complete! Resources: 4 added, 0 changed, 0 destroyed.
   ```
   {: screen}
   
   The following resources are created for you. 
   
   **Nodes**: 
   
   <table>
   <thead>
   <th>Resource</th>
   <th>Flavor</th>
   <th>Description</th>
   </thead>
   <tbody>
   <tr>
   <td>Master node</td>
   <td>B1_4X8X100</td>
   <td>3 disks arranged as SAN with a total capacity of 100GB <ul><li>disk 1: 50GB</li><li>disk 2: 25GB</li><li>disk 3: 25GB</li></ul></td>
   </tr>
   <tr>
   <td>Infrastructure node</td>
   <td>B1_2X4X100</td>
   <td>3 disks arranged as SAN with a total capacity of 100GB <ul><li>disk 1: 50GB</li><li>disk 2: 25GB</li><li>disk 3: 25GB</li></ul></td>
   </tr>
   <tr>
   <td>App nodes</td>
   <td>B1_2X4X100</td>
   <td>3 disks arranged as SAN with a total capacity of 100GB <ul><li>disk 1: 50GB</li><li>disk 2: 25GB</li><li>disk 3: 25GB</li></ul></td>
   </tr>
   <tr>
   <td>Bastion node</td>
   <td>B1_2X2X100</td>
   <td>2 disks with the following capacity: <ul><li>disk 1: 100GB</li><li>disk 2: 50GB</li></ul></td>
   </tr>
   </tbody>
   </table>
   
   **Load balancer**:
   
   A fully qualified domain name is assigned to your load balancer service instance that you can use to access your infrastructure and application nodes from the internet. 
   
   <table>
   <thead>
   <th>Resource</th>
   <th>DNS name (Openshift DNS)</th>
   <th>Assigned instances</th>
   </thead>
   <tbody>
   <tr>
   <td>Infrastructure node load balancer <code>infra-llb</code></td>
   <td>openshift-app-XXXXX-XXXXX-<zone>.lb.bluemix.net</td>
   <td>Infrastructure nodes</td>
   </tr>
   <tr>
   <td>Application node load balancer <code>app-llb</code></td>
   <td>openshift-app-XXXXX-XXXXX-<zone>.lb.bluemix.net</td>
   <td>Application nodes</td>
   </tr>
   </tbody>
   </table>
   
   **Security groups**: 
   
   The default security groups that are created assume the following setup: 
   - All outbound traffic from all nodes to the internet is allowed. 
   - The Bastion server is the only node that allows inbound SSH access. 
   - The Bastion server is connected to both the public and the private VLAN.
   - All OpenShift nodes (master, infrastructure, and app nodes) are connected to a private VLAN only. 
   
   <table>
   <thead>
   <th>Group</th>
   <th>VLAN</th>
   <th>Inbound/ outbound</th>
   <th>Port</th>
   <th>From</th>
   <th>To</th>
   </thead>
   <tbody>
   <tr>
   <td><code>ose_bastion_sq</code></td>
   <td>Public</td>
   <td>Inbound</td>
   <td>22/ TCP</td>
   <td>Internet gateway</td>
   <td>-</td>
   </tr>
   <tr>
   <td><code>ose_bastion_sq</code></td>
   <td>Private</td>
   <td>Outbound</td>
   <td>All</td>
   <td>-</td>
   <td>All</td>
   </tr>
   <tr>
   <td><code>ose_master_sg</code></td>
   <td>Private</td>
   <td>Inbound</td>
   <td>443/ TCP</td>
   <td>Internet gateway</td>
   <td>-</td>
   </tr>
   <tr>
   <td><code>ose_master_sg</code></td>
   <td>Private</td>
   <td>Inbound</td>
   <td>80/ TCP</td>
   <td>Internet gateway</td>
   <td>-</td>
   </tr>
   <tr>
   <td><code>ose_master_sg</code></td>
   <td>Private</td>
   <td>Inbound</td>
   <td>22/ TCP</td>
   <td>ose_bastion_sg</td>
   <td>-</td>
   </tr>
   <tr>
   <td><code>ose_master_sg</code></td>
   <td>Private</td>
   <td>Inbound</td>
   <td>443/ TCP</td>
   <td>ose_master_sg & ose_node_sg</td>
   <td>-</td>
   </tr>
   <tr>
   <td><code>ose_master_sg</code></td>
   <td>Private</td>
   <td>Inbound</td>
   <td>8053/ TCP</td>
   <td>ose_node_sg</td>
   <td>-</td>
   </tr>
   <tr>
   <td><code>ose_master_sg</code></td>
   <td>Private</td>
   <td>Inbound</td>
   <td>8053/ UDP</td>
   <td>ose_node_sg</td>
   <td>-</td>
   </tr>
   <tr>
   <td><code>ose_master_sg</code></td>
   <td>Private</td>
   <td>Outbound</td>
   <td>All</td>
   <td>-</td>
   <td>All</td>
   </tr>
   <tr>
   <td><code>ose_master_sg (for etcd)</code></td>
   <td>Private</td>
   <td>Inbound</td>
   <td>2379/ TCP</td>
   <td>ose_master_sg</td>
   <td>-</td>
   </tr>
   <tr>
   <td><code>ose_master_sg</code></td>
   <td>Private</td>
   <td>Inbound</td>
   <td>2380/ TCP</td>
   <td>ose_master_sg</td>
   <td>-</td>
   </tr>
   <tr>
   <td><code>ose_node_sg</code></td>
   <td>Private</td>
   <td>Inbound</td>
   <td>443/ TCP</td>
   <td>ose_bastion_sg</td>
   <td>-</td>
   </tr>
   <tr>
   <td><code>ose_node_sg</code></td>
   <td>Private</td>
   <td>Inbound</td>
   <td>22/ TCP</td>
   <td>ose_bastion_sg</td>
   <td>-</td>
   </tr>
   <tr>
   <td><code>ose_node_sg</code></td>
   <td>Private</td>
   <td>Inbound</td>
   <td>10250/ TCP</td>
   <td>ose_master_sg & ose_node_sg</td>
   <td>-</td>
   </tr>
   <tr>
   <td><code>ose_node_sg</code></td>
   <td>Private</td>
   <td>Inbound</td>
   <td>4789/ TCP</td>
   <td>ose_node_sg</td>
   <td>-</td>
   </tr>
   <tr>
   <td><code>ose_node_sg</code></td>
   <td>Private</td>
   <td>Outbound</td>
   <td>All</td>
   <td>-</td>
   <td>All</td>
   </tr>
   </tbody>
   </table>
   
3. Validate your deployment. 
   ```
   terraform show
   ```
   {: pre}

## Lesson 3: Deploy the Red Hat OpenShift Container Platform on IBM Cloud
{: #deploy_openshift}

Deploy the Red Hat OpenShift Container Platform on top of the {{site.data.keyword.Bluemix_notm}} infrastructure that you created earlier.
{: shortdesc}

During the deployment the following cluster components are set up and configured: 
- 1 OpenShift Container Platform master node
- 3 OpenShift Container Platform infrastructure nodes
- 2 OpenShift Container Platform application nodes
- 1 OpenShift Container Platform Bastion node

For more information about the Red Hat OpenShift Container Platform components, see the [Architecture Overview ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://docs.openshift.com/container-platform/3.9/architecture/index.html)

1. Create the Bastion node and register the node with the Red Hat Network. The Bastion node is connected to the public VLAN and serves as the only SSH entry point for incoming requests to the cluster. To protect your cluster, the Bastion node is configured to securely download all the software images and packages that are required to perform a [disconnected installation ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://docs.openshift.com/container-platform/3.6/install_config/install/disconnected_install.html) of the OpenShift Container Platform on the master, infrastructure, and application nodes during the creation. The software packages are stored in a local repository on the Bastion node that is automatically set up during the Bastion creation. The master, infrastruction, and application nodes can later access this local repository to perform the disconnected OpenShift installation. 
   ```
   make rhn_username=<rhn_username> rhn_password=<rhn_password> bastion
   ```
   {: pre}
   
   When you create the Bastion server, the following software and Red Hat subscriptions are automatically downloaded and installed on the Bastion node for you: 
   
   **Software packages:**
   
   <table>
   <thead>
   <th>Software</th>
   <th>Version</th>
   </thead>
   <tbody>
   <tr>
   <td>Red Hat® Enterprise Linux 7.4 x86_64</td>
   <td>kernel-3.10.0.x</td>
   </tr>
   <tr>
   <td>Atomic-OpenShift (master/clients/node/sdn-ovs/utils)</td>
   <td>3.6.x.x</td>
   </tr>
   <tr>
   <td>Docker</td>
   <td>1.12.x</td>
   </tr>
   <tr>
   <td>Ansible</td>
   <td>2.3.2.x</td>
   </tr>
   </tbody>
   </table>
   
   **Red Hat subscription channels and `rpm` packages:**
   
   <table>
   <thead>
   <th>Channel</th>
   <th>Repository Name</th>
   </thead>
   <tr>
   <td>Red Hat® Enterprise Linux 7 Server (RPMs)</td>
   <td>rhel-7-server-rpms</td>
   </tr>
   <td>Red Hat® OpenShift Enterprise 3.6 (RPMs)</td>
   <td>rhel-7-server-ose-3.6-rpms</td>
   </tr>
   <tr>
   <td>Red Hat® Enterprise Linux 7 Server - Extras (RPMs)</td>
   <td>rhel-7-server-extras-rpms</td>
   </tr>
   <tr>
   <td>Red Hat® Enterprise Linux 7 Server - Fast Datapath (RPMs)</td>
   <td>rhel-7-fast-datapath-rpms</td>
   </tr>
   </tbody>
   </table>
   
2. Prepare the master, infrastructure, and application nodes for the OpenShift installation and perform the disconnected installation of OpenShift on the nodes.   
   ```
   make openshift
   ```
   {: pre}
   
   Example output: 
   ```
   Outputs:

   app_hostname = [
       app-ose-f049f275fb0
   ]
   app_lbass_url = openshift-app-test-1204953-mel01.lb.bluemix.net
   app_private_ip = [
       10.118.138.40
   ]
   bastion_domain = ibm.com
   bastion_hostname = bastion-ose-f049f275fb
   bastion_private_ip = 10.118.138.49
   bastion_public_ip = 168.1.139.71
   infra_hostname = [
       infra-ose-f049f275fb0
   ]
   infra_lbass_url = openshift-infra-1204953-mel01.lb.bluemix.net
   infra_private_ip = [
       10.118.138.55
   ]
   master_hostname = [
       master-ose-f049f275fb
   ]
   master_private_ip = [
       10.118.138.41
   ]
   master_public_ip = [
    168.1.139.87
   ]
   ```
   {: screen}
   
3. Note the **master_hostname** and **master_public_ip** that was assigned to your master node. 
   To show all your resources with the assigned host names and IPs, run `terraform show`. 
   {: tip}
   
4. Add the master node as a host to the `/etc/hosts` file. 
   1. Open the `/etc/hosts` file. 
      ```
      sudo vi /etc/hosts
      ```
      {: pre}
   
   2. Insert the following line to the end of your file. 
      ```
      <master_public_ip> <master_hostname>
      ```
      {: codeblock}

5. Open the OpenShift console.
   ```
   open https://<master_public_ip>:8443/console
   ```
   {: pre}
   
6. Set up users and authentication for your OpenShift cluster. The OpenShift Container Platform master includes a built-in OAuth server. By default, this OAuth server is set up to deny all authentication. To let developers and admins authenticate with the cluster, follow the steps in [Configuring access and authentication ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://docs.openshift.com/container-platform/3.6/install_config/configuring_authentication.html#install-config-configuring-authentication) to set up access for your cluster. 

7. Configure your Docker registry. During the creation of your cluster, an internal, integrated Docker registry is automatically set up for you. You can use the registry to build container images from your source code, deploy them, and manage their lifecycle. For more information, see [Registry Overview ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://docs.openshift.com/container-platform/3.6/install_config/registry/index.html#install-config-registry-overview). 

8. Configure your cluster router to enable incoming non-SSH network traffic for your cluster. For more information, see [Router Overview ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://docs.openshift.com/container-platform/3.6/install_config/router/index.html#install-config-router-overview). 
   
## Lesson 4: Deploy an app in your Red Hat OpenShift cluster
{: #deploy_app}

With your OpenShift cluster up and running, you can now deploy your first app in the cluster. 
{: shortdesc}

1. List all your infrastructure resources with their IP addresses and host names. Note the following values: `bastion_public_ip`, `master_private_ip`, and `app_lbass_url`. 
   ```
   terraform show
   ```
   {: pre}

2. Log in to the master node. 
   ```
   ssh -t -A root@<bastion_public_ip> ssh root@<master_private_ip>
   ```
   {: pre}

3. Log in to the OpenShift client. Enter **admin** as your user name and **test123** as your password, or use any other user name and password that you set up earlier. 
   ```
   c login https://<master_private_ip>:8443
   ```
   {: pre} 

4. Create a new project where you can store all your app files and configurations. 
   ```
   oc new-project <project_name>
   ```
   {: pre}
   
5. Deploy the app. In this example, `nginx` is deployed to your cluster and the `nginx` image is pushed to your internal Docker registry so that your infrastructure and application nodes can securely access the image over the private network. Enter the IP address and the port of your internal Docker registry.   
   ```
   oc new-app --name=nginx --docker-image=<registry_ip>:<port>/<project_name>/nginx
   ```
   {: pre}
   
   To access an image in your private registry after the image is stored, see [Accessing the Registry ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://docs.openshift.com/container-platform/3.9/install_config/registry/accessing_registry.html). 
   {: tip}

5. Create a service for your `nginx` app to expose your app inside the cluster. 
   ```
   oc expose svc/nginx
   ```
   {: pre}
   
6. Edit your service and change the service type to **NodePort**. 
   ```
   oc edit svc/nginx
   ```
   {: pre}
   
7. Get the internal **CLUSTER-IP** and the **PORT** of your `nginx` app. 
   ```
   oc get services
   ```
   
   Example output: 
   ```
   NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)              AGE
   nginx        NodePort    172.30.89.218    <none>        31377/TCP            2h
   ```
   {: screen}
   
8. Expose your app to the public. By default, your `nginx` app is accessible from inside the cluster only. You can expose your cluster to the public by assigning a front-end port to your application node load balancer. 
   1. Log in to the [{{site.data.keyword.Bluemix_notm}} infrastructure portal ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://control.bluemix.net/). 
   2. From the menu, select **Network** > **Load Balancing** > **Local** and find the application node load balancer that you created earlier. 
   3. From the actions menu, click **View details**. 
   4. Select the **Protocols** tab. 
   5. Click **Add Protocol**. 
   6. Enter the following values: 
      - **Front-end protocols**: Select **HTTP**. 
      - **Front-end port:** Enter the port that you want to use to publicly access your `nginx` app. You can choose any port that you want.
      - **Back-end protocol:** Select **HTTP**. 
      - **Back-end port:** Enter the node port of your `nginx` service that you retrieved earlier. 
      - **Method:** Select **Round Robin**. 
   7. Click **Save**. 
   
   After saving your configuration, the fully qualified domain name that is assigned to your application node load balancer and the front-end port that you entered are exposed to the public. 

8. Access your `nginx` app from the internet.  
   ```
   http://<app_lbass_url>:<front-end-port>
   ```
   {: pre}
</br>

**What's next?**</br>
Great! You successfully installed Red Hat OpenShift Container Platform on top of {{site.data.keyword.Bluemix_notm}} infrastructure and deployed your first app to your OpenShift cluster. Now you can try out one of the following:  

- [Explore other features in Red Hat OpenShift Container Platform ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://docs.openshift.com/container-platform/3.9/welcome/index.html). 
- Remove your OpenShift cluster by running the `make destroy` command. 

