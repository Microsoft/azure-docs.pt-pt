---
title: Visão geral de Kubernetes habilitada a Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: ''
keywords: Kubernetes, Arc, Azure, contentores
ms.custom: references_regions
ms.openlocfilehash: badade7e89d0b03f330bfbe3578d02429220001b
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84194841"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>O que é Kubernetes ativado por Azure Arc (Preview)

Pode anexar e configurar aglomerados Kubernetes dentro ou fora de Azure com Azure Arc ativado Kubernetes (Preview). Quando um cluster Kubernetes estiver ligado ao Arco de Azure, aparecerá no Portal Azure, terá um ID do Gestor de Recursos Azure e uma Identidade Gerida. Os clusters estão ligados às subscrições padrão do Azure, vivem num grupo de recursos e podem receber tags como qualquer outro recurso Azure. 


Ligar um cluster Kubernetes ao Azure requer um administrador de cluster para implantar agentes. Estes agentes funcionam num espaço de nome Kubernetes chamado `azure-arc` e são implementações padrão de Kubernetes. Os agentes são responsáveis pela conectividade com o Azure, recolhendo registos e métricas do Arco Azure e observando pedidos de configuração.  
 
 > [!NOTE]
> Azure Arc habilitado Kubernetes está em pré-visualização e não é recomendado para cargas de trabalho de produção. 


## <a name="supported-scenarios"></a>Cenários Suportados 

Azure Arc habilitado Kubernetes suporta os seguintes cenários: 

* Ligação de Kubernetes fora de Azure para inventário, agrupamento e marcação 

* Implementar aplicações e aplicar configuração usando a gestão de configuração baseada em GitOps 

* Utilize o Monitor Azure para que os recipientes vejam e monitorizem os seus aglomerados 

* Aplicar políticas usando a política Azure para Kubernetes 

 
## <a name="supported-regions"></a>Regiões apoiadas 

Azure Arc habilitado Kubernetes é atualmente apoiado nas seguintes regiões: 

* E.U.A. Leste 
* Europa Ocidental 


## <a name="next-steps"></a>Passos seguintes

* [Ligar um cluster](./connect-cluster.md)
