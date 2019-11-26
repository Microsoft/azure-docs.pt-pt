---
title: Frequently asked questions about Azure Dev Spaces
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: conceptual
description: Find answers to some of the common questions about Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s '
ms.openlocfilehash: 2baab0812061bec7dcf08d35056804313d873889
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482309"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Frequently asked questions about Azure Dev Spaces

This addresses frequently asked questions about Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Which Azure regions currently provide Azure Dev Spaces?

For a complete list of available regions, see [supported regions and configurations][supported-regions].

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Can I use Azure Dev Spaces without a public IP address?

No, you can't provision Azure Dev Spaces on an AKS Cluster without a public IP. A public IP is [needed by Azure Dev Spaces for routing][dev-spaces-routing].

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Can I use my own ingress with Azure Dev Spaces?

Yes, you can configure your own ingress along side the one Azure Dev Spaces creates. For example, you can use [traefik][ingress-traefik].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Can I use HTTPS with Azure Dev Spaces?

Yes, you can configure your own ingress with HTTPS using [traefik][ingress-https-traefik].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Can I use Azure Dev Spaces on a cluster that uses CNI rather than kubenet? 

Yes, you can use Azure Dev Spaces on an AKS cluster that uses CNI for networking. For example, you can use Azure Dev Spaces on an AKS cluster with [existing Windows containers][windows-containers], which uses CNI for networking.

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Can I use Azure Dev Spaces with Windows Containers?

Currently, Azure Dev Spaces is intended to run on Linux pods and nodes only, but you can run Azure Dev Spaces on an AKS cluster with [existing Windows containers][windows-containers].

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Can I use Azure Dev Spaces on AKS clusters with API server authorized IP address ranges enabled?

Yes, you can use Azure Dev Spaces on AKS clusters with [API server authorized IP address ranges][aks-auth-range] enabled. When [creating][aks-auth-range-create] your cluster, you must [allow additional ranges based on your region][aks-auth-range-ranges]. You can also [update][aks-auth-range-update] an existing cluster to allow those additional ranges.

### <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Can I use Azure Dev Spaces on AKS clusters with restricted egress traffic for cluster nodes?

Yes, you can use Azure Dev Spaces on AKS clusters with [Restricted egress traffic for cluster nodes][aks-restrict-egress-traffic] enabled once the following FQDNs have been allowed:

| FQDN                                    | Porta      | Usar      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | To pull linux alpine and other Azure Dev Spaces images |
| gcr.io | HTTP:443 | To pull helm/tiller images |
| storage.googleapis.com | HTTP:443 | To pull helm/tiller images |
| azds-<guid>.<location>.azds.io | HTTPS:443 | To communicate with Azure Dev Spaces backend services for your controller. The exact FQDN can be found in the "dataplaneFqdn" in %USERPROFILE%\.azds\settings.json |


[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md