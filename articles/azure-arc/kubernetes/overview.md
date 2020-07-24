---
title: Visão geral de Kubernetes ativados pelo Arco Azure
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Este artigo fornece uma visão geral de Kubernetes ativados por Azure Arc.
keywords: Kubernetes, Arc, Azure, contentores
ms.custom: references_regions
ms.openlocfilehash: 4f07c0e5e01648984514701cd4838f85478d86af
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050053"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>O que é a pré-visualização de Kubernetes ativada pelo Arco Azure?

Pode anexar e configurar aglomerados Kubernetes dentro ou fora de Azure utilizando a pré-visualização de Kubernetes ativada pelo Arco Azure. Quando um cluster Kubernetes estiver ligado ao Arco de Azure, aparecerá no portal Azure. Terá um ID do Azure Resource Manager e uma identidade gerida. Os clusters estão ligados às subscrições padrão do Azure, estão localizados num grupo de recursos, e podem receber tags como qualquer outro recurso Azure. 

Para ligar um cluster Kubernetes ao Azure, o administrador do cluster precisa de enviar agentes. Estes agentes funcionam num espaço de nome Kubernetes chamado `azure-arc` e são implementações padrão de Kubernetes. Os agentes são responsáveis pela conectividade com o Azure, recolhendo registos e métricas do Arco Azure e observando pedidos de configuração. 

A Azure Arc-enabled Kubernetes suporta sSL padrão da indústria para garantir dados em trânsito. Além disso, os dados são armazenados encriptados em repouso numa base de dados DB Azure Cosmos para garantir a confidencialidade dos dados.
 
> [!NOTE]
> Azure Arc habilitado Kubernetes está em pré-visualização. Não recomendamos para cargas de trabalho de produção.

## <a name="supported-scenarios"></a>Cenários suportados 

Azure Arc-enabled Kubernetes suporta estes cenários: 

* Ligue kubernetes correndo fora de Azure para inventário, agrupamento e marcação.

* Implemente aplicações e aplique a configuração utilizando a gestão de configuração baseada em GitOps. 

* Utilize o Monitor Azure para que os recipientes vejam e monitorizem os seus aglomerados. 

* Aplicar políticas utilizando a Política Azure para Kubernetes. 

## <a name="supported-regions"></a>Regiões suportadas 

Azure Arc-enabled Kubernetes é atualmente apoiado nestas regiões: 

* E.U.A. Leste 
* Europa Ocidental 

## <a name="next-steps"></a>Passos seguintes

* [Ligar um cluster](./connect-cluster.md)
