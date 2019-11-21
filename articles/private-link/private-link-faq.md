---
title: Azure Private Link frequently asked questions (FAQ)
description: Learn about Azure Private Link.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 53cb9b91d62c65cefb33451c716e677599306e9c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229338"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Azure Private Link frequently asked questions (FAQ)

## <a name="private-link"></a>Ligação Privada

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>What is Azure Private Endpoint and Azure Private Link Service?

- **[Azure Private Endpoint](private-endpoint-overview.md)** : Azure Private Endpoint is a network interface that connects you privately and securely to a service powered by Azure Private Link. You can use Private Endpoints to connect to an Azure PaaS service that supports Private Link or to your own Private Link Service.
- **[Azure Private Link Service](private-link-service-overview.md)** : Azure Private Link service is a service created by a service provider. Currently, a Private Link service can be attached to the frontend IP configuration of a Standard Load Balancer. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>How is traffic being sent when using Private Link?
Traffic is sent privately using Microsoft backbone. It doesn’t traverse the internet.  
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>What is the difference between a Service Endpoints and a Private Endpoints?
- When using Private Endpoints, network access is granted to specific resources behind a given service providing granular segmentation, also traffic can reach the service resource from on premises without using public endpoints.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>What is the relationship between Private Link service and Private Endpoint?
Private Endpoint provides access to multiple private link resource types, including Azure PaaS services and your own Private Link Service. It is a one-to-many relationship. One Private Link service can receive connections from multiple private endpoints. On the other hand, one private endpoint can only connect to one Private Link service.    

## <a name="private-endpoint"></a>Ponto Final Privado 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Can I create multiple Private Endpoints in same VNet? Can they connect to different Services? 
Sim. You can have multiple private endpoints in same VNet or subnet. They can connect to different services.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Do I require a dedicated subnet for private endpoints? 
Não. You don't require a dedicated subnet for private endpoints. You can choose a private endpoint IP from any subnet from the VNet where your service is deployed.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>Can Private Endpoint connect to Private Link service across Azure Active Directory Tenants? 
Sim. Private endpoints can connect to Private Link services or Azure PaaS across AD tenants.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>Can private endpoint connect to Azure PaaS resources across Azure regions?
Sim. Private endpoints can connect to Azure PaaS resources across Azure regions.

## <a name="private-link-service"></a>Serviço Ligação Privada
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>What are the pre-requisites for creating a Private Link service? 
Your service backends should be in a Virtual network and behind a Standard Load Balancer.
 
### <a name="how-can-i-scale-my-private-link-service"></a>How can I scale my Private Link service? 
You can scale your Private Link service in a few different ways: 
- Add Backend VMs to the pool behind your Standard Load Balancer 
- Add an IP to the Private Link service. We allow up to 8 IPs per Private Link service.  
- Add new Private Link service to Standard Load Balancer. We allow up to eight Private Link services per load balancer.   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Can I connect my service to multiple Private Endpoints?
Sim. One Private Link service can receive connections from multiple Private Endpoints. However one Private Endpoint can only connect to one Private Link service.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>How should I control the exposure of my Private Link service?
You can control the exposure using the visibility configuration on Private Link service. Visibility supports three settings:

- **None** - Only subscriptions with RBAC access can locate the service. 
- **Restrictive** - Only subscriptions that are whitelisted and with RBAC access can locate the service. 
- **All** - Everyone can locate the service. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Can I create a Private Link service with Basic Load Balancer? 
Não. Private Link service over a Basic Load Balancer is not supported.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Is a dedicated subnet required for Private Link service? 
Não. Private Link service doesn’t require a dedicated subnet. You can choose any subnet in your VNet where your service is deployed.   

### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>I am a service provider using Azure Private Link. Do I need to make sure all my customers have unique IP space and don’t overlap with my IP space? 
Não. Azure Private Link provides this functionality for you. Hence, you are not required to have non-overlapping address space with your customer's address space. 

##  <a name="next-steps"></a>Passos seguintes

- Learn about [Azure Private Link](private-link-overview.md)
