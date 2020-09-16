---
title: Visão geral de Azure Arc habilitado Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Este artigo fornece uma visão geral de Azure Arc habilitado Kubernetes.
keywords: Kubernetes, Arc, Azure, contentores
ms.custom: references_regions
ms.openlocfilehash: fb8a7b7c2e1e5b3de7d1ccdb4054e44825231458
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604806"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>O que é a pré-visualização de Kubernetes ativada por Azure?

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
