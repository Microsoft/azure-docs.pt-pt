---
title: Perguntas frequentes sobre Azure Dev Spaces
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: conceptual
description: Encontre respostas para algumas das perguntas mais comuns sobre Azure Dev Spaces
keywords: 'Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S '
ms.openlocfilehash: c904ae5809a36859ba6428bf026c9016a1a8f747
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867182"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Perguntas frequentes sobre Azure Dev Spaces

Isso aborda as perguntas frequentes sobre Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Quais regiões do Azure atualmente fornecem Azure Dev Spaces?

Consulte [regiões com suporte][supported-regions] para obter uma lista completa de regiões disponíveis.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Posso usar Azure Dev Spaces sem um endereço IP público?

Não, você não pode provisionar Azure Dev Spaces em um cluster AKS sem um IP público. Um IP público é [necessário pelo Azure dev Spaces para roteamento][dev-spaces-routing].

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Posso usar minha própria entrada com Azure Dev Spaces?

Sim, você pode configurar sua própria entrada ao longo do lado que um Azure Dev Spaces cria. Por exemplo, você pode usar [traefik][ingress-traefik].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Posso usar HTTPS com Azure Dev Spaces?

Sim, você pode configurar sua própria entrada com HTTPS usando [traefik][ingress-https-traefik].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Posso usar Azure Dev Spaces em um cluster que usa CNI em vez de kubenet? 

Sim, você pode usar Azure Dev Spaces em um cluster AKS que usa o CNI para rede. Por exemplo, você pode usar Azure Dev Spaces em um cluster AKS com [contêineres do Windows existentes][windows-containers], que usa o CNI para rede. Mais informações sobre como usar o CNI para rede com o Azure Dev Spaces está disponível [aqui](configure-networking.md#using-azure-container-networking-with-azure-dev-spaces).

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Posso usar Azure Dev Spaces com contêineres do Windows?

Atualmente, o Azure Dev Spaces destina-se a ser executado somente em pods e nós do Linux, mas você pode executar Azure Dev Spaces em um cluster AKS com [contêineres do Windows existentes][windows-containers].

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Posso usar Azure Dev Spaces em clusters AKS com intervalos de endereços IP autorizados do servidor de API habilitado?

Sim, você pode usar Azure Dev Spaces em clusters AKS com [intervalos de endereços IP autorizados do servidor de API][aks-auth-range] habilitados. Mais informações sobre como usar clusters AKS com intervalos de endereços IP autorizados do servidor de API habilitados com o Azure Dev Spaces estão disponíveis [aqui](configure-networking.md#using-api-server-authorized-ip-ranges-with-azure-dev-spaces).

### <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Posso usar Azure Dev Spaces em clusters AKS com tráfego de saída restrito para nós de cluster?

Sim, você pode usar Azure Dev Spaces em clusters AKS com [tráfego de saída restrito para nós de cluster][aks-restrict-egress-traffic] habilitados assim que os FQDNs corretos tiverem sido permitidos. Mais informações sobre como usar clusters AKS com tráfego de saída restrito para nós de cluster habilitados com o Azure Dev Spaces estão disponíveis [aqui](configure-networking.md#ingress-and-egress-network-traffic-requirements).

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[windows-containers]: how-to/run-dev-spaces-windows-containers.md