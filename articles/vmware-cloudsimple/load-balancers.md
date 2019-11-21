---
title: Azure VMware Solution by CloudSimple - Choose a load balancing solution for CloudSimple Private Clouds
description: Describes the load balancing options deploying an application in a Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d26eb0160316737c9ad31d98c8cf23bdcad42d32
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74206507"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>Choose a load balancing solution for CloudSimple Private Clouds

When deploying an application in a CloudSimple Private Cloud, you can choose any of several options for load balancing.

You can choose a virtual or software-based load balancer in your CloudSimple private cloud or even use Azure L7 load balancer running in your Azure subscription to front end your web tier VMs running in the CloudSimple Private Cloud. Here, we list a few options:

## <a name="virtual-load-balancers"></a>Virtual load balancers

You can deploy virtual load balancer appliances in your VMware environment through the vCenter interface and configure them to front end your application traffic.

Some popular vendors are: NginX: http://nginx.org/en/docs/http/load_balancing.html F5- BigIP - Traffic Manager: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC: https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Azure L7 load balancer

When you use Azure Application Gateway as a L7 load balancer for your application running in a Private Cloud, you don’t need to manage the load balancer software. The load balancer software is managed by Azure. All the web tier VMs in the Private Cloud use private IP addresses and don’t require additional NAT rules or public IPs addresses to resolve names. Web tier VMs communicate with the Azure Application Gateway over a private, low-latency, high-bandwidth connection.

To learn more about how to configure this solution, refer to the solution guide on Using Azure Application Gateway as a L7 load balancer.

## <a name="azure-internal-load-balancer"></a>Azure internal load balancer

If you choose to run your application in a hybrid deployment where the web front-end tier is running within an Azure vNet in your Azure subscription and the DB tier of the application is running in VMware VMs in CloudSimple Private Cloud, you can use Azure internal load balancer (L4 load balancer) in front of your DB tier VMs for traffic management.

To learn more, see Azure [Internal Load Balancer](../load-balancer/load-balancer-overview.md#internalloadbalancer) documentation.

## <a name="global-server-load-balancer"></a>Global server load balancer

If you are looking for a DNS-based load balancer, then you may either use third party solutions available in Azure Marketplace or go with the native Azure solution.

Azure Traffic Manager is a DNS-based traffic load balancer that enables you to distribute traffic optimally to services across global Azure regions and on-premises, while providing high availability and responsiveness. To learn more, see Azure [Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) documentation.
