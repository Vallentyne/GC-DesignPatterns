# APIM and Networks

## Design

Overall, this is a simple environment.  This is a brief overview of the main components.

![APIM Design](./APIM-network.png)


## Attribution
Much of this information is from the product doc pages, but a lot of the detail comes from the following pages, excellent information on this topic.

[API Management - Networking FAQs (Demystifying Series I)](https://techcommunity.microsoft.com/t5/azure-paas-blog/api-management-networking-faqs-demystifying-series-i/ba-p/1500996#NSG6)

[API Management - Networking FAQs (Demystifying Series II)](https://techcommunity.microsoft.com/t5/azure-paas-blog/api-management-networking-faqs-demystifying-series-ii/ba-p/1502056)

# Deployment

For the "internal only" VNet model which is the model that we expect most security sensitive departments to choose, there are many [advantages](https://docs.microsoft.com/en-us/azure/api-management/api-management-using-with-internal-vnet).

The requirements for this kind of deployment include allowing some network traffic to flow in ways that does not align with the hub and spoke model.  The specifics are well covered in the [documentation](https://docs.microsoft.com/en-us/azure/api-management/api-management-using-with-internal-vnet#--routing), but specifically:

## Subnet Service Endpoints

For the subnet that you are installing APIM to, enable service endpoints to:

    - Azure SQL
    - Azure Storage
    - Azure Eventhub
    - Azure Servicebus

This will reduce the other routing and firewall rule requirements, and keep this traffic to the Azure backbone.

## Subnet Route Table

The API subnet route table needs to have a direct route to the API service control IPs.  **This is the traffic pattern that runs against the usual hub and spoke flows.**  The APIM service needs a direct path to the control plane addresses, as shown below.  The actual addresses used here will vary, use the regional addresses as necessary, [found here](https://docs.microsoft.com/en-us/azure/api-management/api-management-using-with-vnet#--control-plane-ip-addresses).

![APIM Route Table](./routetable.png)

## Firewall Rules

Since some of the traffic does respect the hub and spoke networking, we need to allow the necessary flows for monitoring, and other related services.  The following rules were enabled for outbound flows from the API subnet, and are listed here for clarity.

### Application Rule


![Firewall App Rule](./FW-apprules.png)

The target FQDNs in this list are:
- *.windows.net
- *.azure.com
- *.microsoft.com
- *.visualstudio.com

### Network Rules

![Network Rule for Monitoring](./FW-netrules.png)

![Network Rule for SMTP](./FW-netrules2.png)


