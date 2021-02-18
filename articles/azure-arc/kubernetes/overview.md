---
title: Visão geral de Azure Arc habilitado Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Este artigo fornece uma visão geral de Azure Arc habilitado Kubernetes.
keywords: Kubernetes, Arc, Azure, contentores
ms.custom: references_regions
ms.openlocfilehash: 3d96c8c8764db89501da6fb9c498f0a3d20461af
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652535"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>O que é o Kubernetes ativado pelo Azure Arc?

Com o Azure Arc ativado Kubernetes, pode anexar e configurar aglomerados Kubernetes localizados dentro ou fora de Azure. Quando ligar um cluster Kubernetes ao Arco de Azure, será:
* Apareça no portal Azure com um ID do Azure Resource Manager e uma identidade gerida. 
* Seja anexado às assinaturas Azure padrão.
* Ser colocado num grupo de recursos.
* Receba tags como qualquer outro recurso Azure. 

Para ligar um cluster Kubernetes ao Azure, o administrador do cluster precisa de enviar agentes. Estes agentes:
* Corra no espaço de `azure-arc` nomes Kubernetes como implementações padrão de Kubernetes.
* Lidar com a conectividade com o Azure.
* Colete registos e métricas do Arco azul.
* Atenção aos pedidos de configuração. 

A Azure Arc permitiu que a Kubernetes suporta sSL padrão da indústria para proteger dados em trânsito. Estes dados são armazenados encriptados e em repouso numa base de dados DB da Azure Cosmos para garantir a confidencialidade dos dados.
 
## <a name="supported-kubernetes-distributions"></a>Distribuição apoiada de Kubernetes

Azure Arc permitiu que Kubernetes trabalha com qualquer cluster de Kubernetes certificado pela Cloud Native Computing Foundation (CNCF), tais como:
* Motor AKS em Azure
* Motor AKS no Azure Stack Hub
* GKE
* EKS
* VMware vSphere

As funcionalidades de Kubernetes ativadas pelo Azure Arc foram testadas pela equipa arc nas seguintes distribuições:
* RedHat OpenShift 4.3
* Rancheiro RKE 1.0.8
* Kubernetes Encantados Canónicos 1.18
* Motor AKS
* Motor AKS no Azure Stack Hub
* AKS em Azure Stack HCI
* Cluster API Provider Azure

## <a name="supported-scenarios"></a>Cenários suportados 

Azure Arc habilitado Kubernetes suporta os seguintes cenários: 

* Ligue kubernetes correndo fora de Azure para inventário, agrupamento e marcação.

* Implemente aplicações e aplique a configuração utilizando a gestão de configuração baseada em GitOps. 

* Veja e monitorize os seus agrupamentos utilizando o Azure Monitor para obter recipientes. 

* Aplicar políticas usando a política Azure para Kubernetes. 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Regiões suportadas 

Azure Arc habilitado Kubernetes é atualmente apoiado nestas regiões: 

* E.U.A. Leste 
* Europa Ocidental

## <a name="next-steps"></a>Passos seguintes

* [Ligar um cluster](./connect-cluster.md)
