---

copyright:
  years: 2018
lastupdated: "2018-11-28"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:note: .note}
{:download: .download}
{:faq: data-hd-content-type='faq'}

# FAQs
{: #faqs}

## How do I find the flavor and parameters to configure a virtual service instance in {{site.data.keyword.Bluemix_notm}}? 
{: #vsi_config}
{: faq}

The Terraform `ibm_compute_vm_instance` resource includes optional and mandatory parameters to be completed. The compute configuration parameters can be determined using the {{site.data.keyword.Bluemix_notm}} CLI.  

1. Install the [{{site.data.keyword.Bluemix_notm}} CLI](/docs/cli/reference/ibmcloud/download_cli.html#install_use). 

2. List supported configuration options for virtual servers. The listed options include available data centers, machine flavors, cpu, memory, operating systems, local disk and SAN disk sizes, and network interface controllers (nic) in {{site.data.keyword.Bluemix_notm}}. {{site.data.keyword.Bluemix_notm}} offers multiple virtual server offerings that each come with a specific configuration. The configuration of an offering is optimized for a specific workload need, such as high input and output performance, or real-time analytics. For more information see [Public Virtual Servers](/docs/vsi/vsi_public.html#public-virtual-servers). 
   ```
   ibmcloud sl vs options
   ```
   {: pre}


## Resource provisioning times
{: provisioning_times}
{: faq}

Most {{site.data.keyword.Bluemix}} resources are provisioned within seconds. Resources including bare-metal servers, virtual servers and Cloud Load Balancers take longer. The `terraform apply` or `destroy` may be stalled waiting for these resources if there are explicit or implicit dependencies through interpolation.  Virtual servers are created in the range of 3-4 minutes, destroy occurs in seconds. {{site.data.keyword.Bluemix}} Load Balancers are in the range of 6-7 minutes to create, destroy can take between 7 to 30 minutes for the task to complete. Bare-metal provisioning times depend on the configuration requested and will be in the order of hours. 

If the Terraform operation does not complete due to a timeout, wait for the resource state change to complete and retry the operation. 
