---
title: Localizações personalizadas - Azure Arc habilitado Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Este artigo fornece uma visão geral conceptual da capacidade de localizações personalizadas de Azure Arc habilitado Kubernetes
ms.openlocfilehash: 1235c6adfe97a943ef77584a6a0c8683d691be91
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451129"
---
# <a name="custom-locations-on-top-of-azure-arc-enabled-kubernetes"></a>Localizações personalizadas no topo do Arco Azure habilitados Kubernetes

Como uma extensão da construção da localização Azure, *as Localizações Personalizadas* fornecem uma maneira para os administradores de inquilinos usarem os seus clusters Azure Arc habilitados Kubernetes como locais-alvo para implantar instâncias de serviços Azure. Exemplos de recursos Azure incluem Azure Arc enabled INSTANCE e Azure Arc habilitado a hiperescala pósgresQL.

Semelhante às localizações do Azure, os utilizadores finais dentro do inquilino com acesso a Localizações Personalizadas podem aí utilizar recursos utilizando o computação privada da sua empresa.

[![Camadas de plataforma arc ](./media/conceptual-arc-platform-layers.png)](./media/conceptual-arc-platform-layers.png#lightbox)

Você pode visualizar localizações personalizadas como uma camada de abstração em cima do Arco Azure ativado cluster Kubernetes, cluster connect e extensões de cluster. As Localizações Personalizadas criam as [RoleBindings granulares e ClusterRoleBindings necessárias](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) para que outros serviços Azure acedam ao cluster. Estes outros serviços Azure requerem acesso ao cluster para gerir recursos que o cliente quer implementar nos seus clusters.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Arquitetura

Quando o administrador permite a funcionalidade de Localizações Personalizadas no cluster, é criado um ClusterRoleBinding no cluster, autorizando a aplicação AZure AD utilizada pelo Fornecedor de Recursos de Localizações Personalizadas (RP). Uma vez autorizados, as localizações personalizadas RP podem criar ClusterRoleBindings ou RoleBindings necessários por outros RPs Azure para criar recursos personalizados neste cluster. As extensões de cluster instaladas no cluster determinam a lista de RPs para autorizar.

[![Use locais personalizados ](./media/conceptual-custom-locations-usage.png)](./media/conceptual-custom-locations-usage.png#lightbox)

Quando o utilizador cria uma instância de serviço de dados no cluster: 
1. O pedido PUT é enviado ao Gestor de Recursos Azure.
1. O pedido PUT é reencaminhado para o Azure Arc habilitado serviços de dados RP. 
1. O RP recolhe o `kubeconfig` ficheiro associado ao aglomerado de Kubernetes ativado pelo Arco Azure, no qual existe a Localização Personalizada. 
   * A Localização Personalizada é referenciada como `extendedLocation` no pedido PUT original. 
1. Azure Arc habilitado a Serviços de Dados RP usa o `kubeconfig` para comunicar com o cluster para criar um recurso personalizado do tipo de Serviços de Dados habilitado para o Arco Azure no espaço de nome mapeado para a Localização Personalizada. 
   * O operador Azure Arc habilitado para serviços de dados foi implantado através da criação de extensão de cluster antes da existência da Localização Personalizada. 
1. O operador de Serviços de Dados Azure Arc permite que o novo recurso personalizado criado no cluster e crie o controlador de dados, traduzindo-se na realização do estado desejado no cluster. 

A sequência de passos para criar o exemplo gerido pelo SQL e a instância PostgreSQL são idênticas à sequência de passos acima descritos.

## <a name="next-steps"></a>Passos seguintes

* Use o nosso quickstart para [ligar um cluster Kubernetes ao Arco Azure.](./quickstart-connect-cluster.md)
* [Crie uma localização personalizada](./custom-locations.md) no seu cluster Kubernetes ativado pelo Azure Arc.