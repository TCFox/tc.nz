---
layout: post
title:  "Enforcing TLS 1.2 with Azure CDN"
date:   2020-01-16 21:14:06 +1300
tags: azure tls cdn
excerpt_separator: "<!--more-->"
---
It's been several years since TLS 1.0 was largely agreed to be deprecated in favour of TLS 1.2. Sadly, a number of products out there still accept TLS 1.0 connections and cannot be configured otherwise. Up until recently, I thought [Azure CDN](https://azure.microsoft.com/en-us/services/cdn/) was one of them, but it turns out there's a sneaky hidden trick buried deep in the [Azure Management REST API](https://docs.microsoft.com/en-us/rest/api/azure/) that allows you to set your custom domain CDN endpoints to enforce TLS 1.2 as a minimum!
<!--more-->
## The problem
So, you've got some static content you want to serve to the world, such as some static CSS/Javascript assets, user-uploaded data or even an entire static website! Azure CDN is absolutely perfect for your usecase, as your data doesn't change much and can tolerate being heavily cached. It sits between your origin (where the data is stored) and your users (such as their web browser) and tries to remember what it sees go past (step 1-4), serving data it remembers in it's cache so your user's requests don't have to go all the way back to your origin servers (step 5-6):

{% asset azure-cdn-tls12-cdnoverview.png alt='Azure CDN Overview' width=500px height=auto %}

Now, instead of going to `https://yourazureblobstorageaccount.blob.core.windows.net/staticfilescontainer/somefile.png` to get your large-but-never-changing files, your users go to a new URL: `https://cdnprofile.azureedge.net` and Azure CDN magic happens!

It's... an improvement, but what you _really_ want is a domain name that's a bit closer to your brand. Fortunately, you can set a custom domain too! Setting this up is easy, and can be done entirely in the Azure Portal!

{% asset azure-cdn-tls12-customdomainportal.png alt='Azure CDN Custom Domain configuration page' width=650px height=auto %}

Once you've set this up, and [configured your HTTPS certificates based on this guide here](https://docs.microsoft.com/en-us/azure/cdn/cdn-custom-ssl?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate), it's time to check out your rating on SSL Labs!

{% asset azure-cdn-tls12-ssllabsbwarning.png alt='SSL Labs - A but with a warning...' width=650px height=auto %}

Sadly, despite the 'A' rating as at the time of writing, there's a big warning there that the best we'll get shortly is a mere 'B', since TLS 1.0 is still configured! But there wasn't any option to choose otherwise in the Azure Portal...

## The solution: introducing the Azure REST API!
Despite the lack of support for CDN Profile TLS configuration in the Azure Portal (or, as far as I know, even in the command line or SDKs for managing Azure resources), these all depend on the underlying Azure REST API to function, and sure enough [deep in the guts of the Azure REST API documentation there exists this page](https://docs.microsoft.com/en-us/rest/api/cdn/customdomains/enablecustomhttps#minimumtlsversion):

{% asset azure-cdn-tls12-minimumtlsversiondocumentation.png alt='Azure REST API documentation' width=500px height=auto %}

Promising! While we can't use a nice and intuitive GUI to get this configured, we can at least poke the API directly to get where we want. All it takes is two HTTP requests!

### List custom domains

Firstly, we need to query Azure to get our list of custom domains for the CDN endpoint we set up earlier. This can be done with the [Azure CDN Custom Domain "List By Endpoint" call](https://docs.microsoft.com/en-us/rest/api/cdn/customdomains/listbyendpoint):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}/customDomains?api-version=2019-04-15
Authorization: Bearer eyJ[...]OWO
```
(for details on how to get the `Authorization: Bearer` token, please refer to the [Getting Started with REST](https://docs.microsoft.com/en-us/rest/api/azure/#create-the-request) documentation, or hit the handy "Try It" button on the docs.microsoft.com page to have Azure do the hard bits for you!)

Fire off the request, and we should get a result similar to the following:

```json
{
  "value": [
    {
      "name": "tc-nz",
      "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}/customdomains/{customDomain}",
      "type": "Microsoft.Cdn/profiles/endpoints/customdomains",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceState": "Active",
        "hostName": "{customDomainHostname}",
        "validationData": null,
        "customHttpsProvisioningState": "Enabled",
        "customHttpsProvisioningSubstate": "CertificateDeployed",
        "customHttpsParameters": {
          "certificateSource": "AzureKeyVault",
          "certificateSourceParameters": {
            "@odata.type": "#Microsoft.Azure.Cdn.Models.KeyVaultCertificateSourceParameters",
            "subscriptionId": "{subscriptionId}",
            "resourceGroupName": "{resourceGroupName}",
            "vaultName": "{azureKeyVaultName}",
            "secretName": "{azureKeyVaultSecretName}",
            "secretVersion": "{azureKeyVaultSecretVersion}",
            "updateRule": "NoAction",
            "deleteRule": "NoAction"
          },
          "minimumTLSVersion": "None",
          "protocolType": "ServerNameIndication"
        }
      }
    }
  ]
}
```

Note the `customHttpsParameters` section, as well as the part we've marked as `{customDomain}`! We'll need that later.

### Set the minimum TLS version to TLS 1.2

We'll first need to *disable* HTTPS, as we're going to be setting it back up again based on the parameters seen in our first request. Go ahead and do that in the Azure Portal, as that's the easiest way to do it:

{% asset azure-cdn-tls12-customdomainhttpsoff.png alt='Disabling HTTPS on Azure CDN custom domains' width=650px height=auto %}

Next, we'll fire a call off to the [Azure CDN Custom Domain "Enable Custom HTTPS" call](https://docs.microsoft.com/en-us/rest/api/cdn/customdomains/enablecustomhttps):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}/customDomains/{customDomain}/enableCustomHttps?api-version=2019-04-15
Authorization: Bearer eyJ[...]OWO
Content-type: application/json

{
    "certificateSource": "AzureKeyVault",
    "certificateSourceParameters": {
        "@odata.type": "#Microsoft.Azure.Cdn.Models.KeyVaultCertificateSourceParameters",
        "subscriptionId": "{subscriptionId}",
        "resourceGroupName": "{resourceGroupName}",
        "vaultName": "{azureKeyVaultName}",
        "secretName": "{azureKeyVaultSecretName}",
        "secretVersion": "{azureKeyVaultSecretVersion}",
        "updateRule": "NoAction",
        "deleteRule": "NoAction"
    },
    "minimumTLSVersion": "TLS12",
    "protocolType": "ServerNameIndication"
}
```

Note the following:

* We set the `minimumTLSVersion` to the exact string `"TLS12"`. Don't put dots here, it looks exactly like this.
* The POST body consists of the contents of the `customHttpsParameters` section from the last call that I mentioned earlier. 
* The `{customDomain}` part in the URL is **not** the same as your domain name! Azure represents this using dashes rather than dots, it's important that this matches the result from the "List By Endpoint" call earlier! This caught me out when I first tried this out.

Once that's fired off, we should get the following `HTTP 202` result:

```json
{
  "provisioningState": "Succeeded",
  "resourceState": "Active",
  "hostName": "{customDomainHostname}",
  "validationData": null,
  "customHttpsProvisioningState": "Enabling",
  "customHttpsProvisioningSubstate": "ImportingUserProvidedCertificate",
  "customHttpsParameters": {
    "certificateSource": "AzureKeyVault",
    "certificateSourceParameters": {
      "@odata.type": "#Microsoft.Azure.Cdn.Models.KeyVaultCertificateSourceParameters",
      "subscriptionId": "{subscriptionId}",
      "resourceGroupName": "{resourceGroupName}",
      "vaultName": "{azureKeyVaultName}",
      "secretName": "{azureKeyVaultSecretName}",
      "secretVersion": "{azureKeyVaultSecretVersion}",
      "updateRule": "NoAction",
      "deleteRule": "NoAction"
    },
    "minimumTLSVersion": "TLS12",
    "protocolType": "ServerNameIndication"
  }
}
```

## The result

Give it a couple of hours, and our SSL Labs result should look like the following:

{% asset azure-cdn-tls12-ssllabsaplus.png alt='SSL Labs - A+!' width=650px height=auto %}

Sweet as!