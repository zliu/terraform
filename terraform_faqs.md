---

copyright:
  years: 2018
lastupdated: "2018-11-28"

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:table: .aria-labeledby="caption"}


# FAQs: Terraform  

## How do I find the flavor and parameters to configure a VSI? 
{: vsi_parms}

The Terraform `ibm_compute_vm_instance resource` includes optional and mandatory parameters to be completed. The compute configuration parameters can be determined using the IBM Cloud CLI.  

Follow the instructions to install the [IBM Cloud Infrastructure CLI](https://console.bluemix.net/docs/cli/reference/ibmcloud/download_cli.html#install_use) and [infrastructure-service plugin](https://console.bluemix.net/docs/cli/reference/ibmcloud/extend_cli.html#plug-ins). 

Run the [IBM Cloud CLI command](https://console.bluemix.net/docs/cli/reference/ibmcloud/cli_virtual_server.html#sl_vs_options) `vs options` to list all available VSI configuration parameters:
```
ibmcloud sl vs options
```
{: pre}

If there is a message indicating that the **Token** has expired, relogin to the IBM Cloud CLI first and retry.
 
```
ibmcloud sl init
ibmcloud sl vs options
```
{: pre}

The listed options include:  datacenter, flavors, cpu, memory, os, local disk, san disk, nic. The documentation for [Public Virtual Servers]( https://console.bluemix.net/docs/vsi/vsi_public.html#public-virtual-servers) details the flavors and types, Compute, Balanced and Memory. 

Example: BL2_1X2X100 

 - Type: 		Balanced (SSD)
 - Cores:		1
 - Memory:	2GB
 - Disk:		100GB


## Resource provisioning times
{: provisioning_times}

Most {{site.data.keyword.Bluemix}} resources are provisioned within seconds. Resources including bare-metal servers, virtual servers and Cloud Load Balancers take longer. The `terraform apply` or `destroy` may be stalled waiting for these resources if there are explicit or implicit dependencies through interpolation.  Virtual servers are created in the range of 3-4 minutes, destroy occurs in seconds. {{site.data.keyword.Bluemix}} Load Balancers are in the range of 6-7 minutes to create, destroy can take between 7 to 30 minutes for the task to complete. Bare-metal provisioning times depend on the configuration requested and will be in the order of hours. 

If the Terraform operation does not complete due to a timeout, wait for the resource state change to complete and retry the operation. 
