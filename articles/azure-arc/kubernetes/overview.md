---
title: Visão geral de Kubernetes ativadas por Arco Azure
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: ''
keywords: Kubernetes, Arc, Azure, contentores
ms.openlocfilehash: 3b3b184ff7b18e05dfeb1052c0d6b4aa406a8787
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83666259"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>O que é Kubernetes ativado por Arco Azure (Pré-visualização)

Pode anexar e configurar aglomerados Kubernetes dentro ou fora de Azure com Kubernetes ativados por Azure Arc (Pré-visualização). Quando um cluster Kubernetes está ligado ao Azure Arc, aparecerá no Portal Azure, terá um ID do Gestor de Recursos Azure e uma Identidade Gerida. Os clusters estão ligados a subscrições padrão do Azure, vivem num grupo de recursos, e podem receber tags como qualquer outro recurso Azure. 


Ligar um cluster Kubernetes ao Azure requer um administrador de cluster para implementar agentes. Estes agentes funcionam num espaço de nome kubernetes chamado `azure-arc` e são destacamentos kubernetes padrão. Os agentes são responsáveis pela conectividade com o Azure, recolhendo registos e métricas do Azure Arc e vigiando os pedidos de configuração.  
 
 > [!NOTE]
> A Kubernetes ativada pelo Azure Arc está em pré-visualização e não é recomendada para cargas de trabalho de produção. 


## <a name="supported-scenarios"></a>Cenários Suportados 

O Azure Arc habilitado pela Kubernetes suporta os seguintes cenários: 

* Ligando Kubernetes correndo fora de Azure para inventário, agrupamento e marcação 

* Implementar aplicações e aplicar a configuração utilizando a gestão de configuração baseada em GitOps 

* Utilize o Monitor Azure para que os recipientes vejam e monitorizem os seus clusters 

* Aplicar políticas utilizando a política azure para kubernetes 

 
## <a name="supported-regions"></a>Regiões Apoiadas 

A Azure Arc ativada Kubernetes é atualmente apoiada nas seguintes regiões: 

* E.U.A. Leste 
* Europa ocidental 


## <a name="next-steps"></a>Próximos passos

* [Ligar um cluster](./connect-cluster.md)
