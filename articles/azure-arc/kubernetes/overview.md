---
title: Visão geral de Azure Arc habilitado Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Este artigo fornece uma visão geral de Azure Arc habilitado Kubernetes.
keywords: Kubernetes, Arc, Azure, contentores
ms.custom: references_regions
ms.openlocfilehash: 54b439d73ed5dfb0709d33dea1f588b8bdf4489f
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560273"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>O que é a Pré-visualização do Kubernetes compatível com o Azure Arc?

Pode anexar e configurar aglomerados Kubernetes dentro ou fora de Azure utilizando a pré-visualização de Kubernetes ativada por Azure Arc. Quando um cluster Kubernetes estiver ligado ao Arco de Azure, aparecerá no portal Azure. Terá um ID do Azure Resource Manager e uma identidade gerida. Os clusters estão ligados às subscrições padrão do Azure, estão localizados num grupo de recursos, e podem receber tags como qualquer outro recurso Azure. 

Para ligar um cluster Kubernetes ao Azure, o administrador do cluster precisa de enviar agentes. Estes agentes funcionam num espaço de nome Kubernetes chamado `azure-arc` e são implementações padrão de Kubernetes. Os agentes são responsáveis pela conectividade com o Azure, recolhendo registos e métricas do Arco Azure e observando pedidos de configuração. 

A Azure Arc permitiu que a Kubernetes suporta sSL padrão da indústria para proteger dados em trânsito. Além disso, os dados são armazenados encriptados em repouso numa base de dados DB Azure Cosmos para garantir a confidencialidade dos dados.
 
> [!NOTE]
> Azure Arc habilitado Kubernetes está em pré-visualização. Não recomendamos para cargas de trabalho de produção.

## <a name="supported-kubernetes-distributions"></a>Distribuição apoiada de Kubernetes

Azure Arc permitiu que Kubernetes trabalha com qualquer cluster certificado da Cloud Native Computing Foundation (CNCF), como o motor AKS em Azure, motor AKS no Azure Stack Hub, GKE, EKS e VMware vSphere cluster.

As funcionalidades de Kubernetes ativadas pelo Azure Arc foram testadas pela equipa da Arc nas seguintes distribuições:
* RedHat OpenShift 4.3
* Rancheiro RKE 1.0.8
* Kubernetes Encantados Canónicos 1.18
* Motor AKS
* Motor AKS no Azure Stack Hub
* AKS em Azure Stack HCI
* Cluster API Provider Azure

## <a name="supported-scenarios"></a>Cenários suportados 

Azure Arc habilitado Kubernetes suporta estes cenários: 

* Ligue kubernetes correndo fora de Azure para inventário, agrupamento e marcação.

* Implemente aplicações e aplique a configuração utilizando a gestão de configuração baseada em GitOps. 

* Utilize o Monitor Azure para que os recipientes vejam e monitorizem os seus aglomerados. 

* Aplicar políticas utilizando a Política Azure para Kubernetes. 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Regiões suportadas 

Azure Arc habilitado Kubernetes é atualmente apoiado nestas regiões: 

* E.U.A. Leste 
* Europa Ocidental

## <a name="next-steps"></a>Passos seguintes

* [Ligar um cluster](./connect-cluster.md)
