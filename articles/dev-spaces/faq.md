---
title: Perguntas frequentes sobre Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/25/2019
ms.topic: conceptual
description: Encontre respostas para algumas das perguntas mais comuns sobre Azure Dev Spaces
keywords: 'Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S '
ms.openlocfilehash: 317a942f94f3852cc11b8c0f004ed55097d05bc6
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014211"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Perguntas frequentes sobre Azure Dev Spaces

Isso aborda as perguntas frequentes sobre Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Quais regiões do Azure atualmente fornecem Azure Dev Spaces?

Para obter uma lista completa das regiões disponíveis, consulte [regiões e configurações com suporte][supported-regions].

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Posso usar Azure Dev Spaces sem um endereço IP público?

Não, você não pode provisionar Azure Dev Spaces em um cluster AKS sem um IP público. Um IP público é [necessário pelo Azure dev Spaces para roteamento][dev-spaces-routing].

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Posso usar minha própria entrada com Azure Dev Spaces?

Sim, você pode configurar sua própria entrada ao longo do lado que um Azure Dev Spaces cria. Por exemplo, você pode usar [traefik][ingress-traefik].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Posso usar HTTPS com Azure Dev Spaces?

Sim, você pode configurar sua própria entrada com HTTPS usando [traefik][ingress-https-traefik].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Posso usar Azure Dev Spaces em um cluster que usa CNI em vez de kubenet? 

Sim, você pode usar Azure Dev Spaces em um cluster AKS que usa o CNI para rede. Por exemplo, você pode usar Azure Dev Spaces em um cluster AKS com [contêineres do Windows existentes][windows-containers], que usa o CNI para rede.

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Posso usar Azure Dev Spaces com contêineres do Windows?

Atualmente, o Azure Dev Spaces destina-se a ser executado somente em pods e nós do Linux, mas você pode executar Azure Dev Spaces em um cluster AKS com [contêineres do Windows existentes][windows-containers].

### <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Posso usar Azure Dev Spaces em clusters AKS com intervalos de endereços IP autorizados do servidor de API habilitado?

Sim, você pode usar Azure Dev Spaces em clusters AKS com [intervalos de endereços IP autorizados do servidor de API][aks-auth-range] habilitados. Ao [criar][aks-auth-range-create] o cluster, você deve [permitir intervalos adicionais com base em sua região][aks-auth-range-ranges]. Você também pode [Atualizar][aks-auth-range-update] um cluster existente para permitir esses intervalos adicionais.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md