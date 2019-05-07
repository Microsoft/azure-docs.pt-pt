---
title: Como integrar o Azure Monitor para contentores | Documentos da Microsoft
description: Este artigo descreve como sua integração e configure o Azure Monitor para contentores, para que possa compreender o desempenho do seu contentor e quais problemas relacionados ao desempenho foram identificados.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: 10b80a9749c5698195ac5d3493ac3b07fd6e24e1
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073314"
---
# <a name="how-to-onboard-azure-monitor-for-containers"></a>Como integrar o Azure Monitor para contentores  

Este artigo fornece uma descrição geral das opções disponíveis para configurar o Azure Monitor para contentores para monitorizar o desempenho de cargas de trabalho que são implementadas nos ambientes do Kubernetes e alojada num [do Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/).

Monitor do Azure para contentores pode ser ativado para a nova ou suportada de um ou mais implementações existentes do AKS com os seguintes métodos:

* No portal do Azure, Azure PowerShell, ou com a CLI do Azure
* Usando [Terraform e AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos 
Antes de começar, certifique-se de que tem o seguinte:

- **Uma área de trabalho do Log Analytics.** Pode criá-la quando ativar a monitorização do seu novo cluster do AKS ou permitir que a experiência de integração, criar uma área de trabalho padrão no grupo de recursos predefinido da subscrição de cluster do AKS. Se optar por criá-lo, pode criá-lo através de [do Azure Resource Manager](../platform/template-workspace-configuration.md), da funcionalidade [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), ou no [portal do Azure](../learn/quick-create-workspace.md).
- For um membro do **função de Contribuidor do Log Analytics** para ativar a monitorização do contentor. Para obter mais informações sobre como controlar o acesso a uma área de trabalho do Log Analytics, consulte [gerir áreas de trabalho](../platform/manage-access.md).
- For um membro do **[proprietário](../../role-based-access-control/built-in-roles.md#owner)** função no recurso de cluster do AKS. 

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>Componentes 

Sua capacidade de monitorizar o desempenho se baseia num agente de Log Analytics em contentores de Linux desenvolvido especificamente para o Azure Monitor para contentores. Este agente especializada recolhe dados de eventos de desempenho e de todos os nós do cluster e o agente automaticamente implementar e registar com a área de trabalho do Log Analytics especificada durante a implementação. A versão do agente é microsoft / oms:ciprod04202018 ou posterior e é representado por uma data no seguinte formato: *mmddyyyy*. 

>[!NOTE]
>Com a versão de pré-visualização do suporte do Windows Server para o AKS, um cluster do AKS com os nós do Windows Server não tem um agente instalado para recolher dados e reencaminhar para o Azure Monitor. Em vez disso, um nó de Linux automaticamente implementado no cluster como parte da implementação padrão recolhe e reencaminha-os dados para o Azure Monitor em nome de todos os nós do Windows no cluster.  
>

Quando for lançada uma nova versão do agente, é atualizada automaticamente nos seus clusters do Kubernetes geridos alojados no Azure Kubernetes Service (AKS). Para seguir as versões lançadas, consulte [anúncios de lançamentos de agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod). 

>[!NOTE] 
>Se já tiver implementado um cluster do AKS, ativa a monitorização com o CLI do Azure ou um modelo Azure Resource Manager fornecido, conforme demonstrado neste artigo. Não é possível utilizar `kubectl` para atualizar, eliminar, implemente novamente ou implementar o agente. O modelo precisa ser implantado no mesmo grupo de recursos do cluster.

Ativar o Azure Monitor para contentores com um dos seguintes métodos descritos na tabela seguinte.

| Estado de implementação | Método | Descrição | 
|------------------|--------|-------------| 
| Novo cluster do AKS | [Criar cluster com a CLI do Azure](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Pode ativar a monitorização de um novo cluster do AKS que criar com a CLI do Azure. | 
| | [Criar cluster com o Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Pode ativar a monitorização de um novo cluster do AKS que crie com a ferramenta de código-fonte aberto Terraform. | 
| Cluster do AKS existente | [Ativar a CLI do Azure](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Pode ativar a monitorização de um cluster do AKS já implementado com a CLI do Azure. | 
| |[Ativar com o Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Pode ativar a monitorização de um cluster do AKS já implementado com a ferramenta de código-fonte aberto Terraform. | 
| | [Ativar a partir do Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Pode ativar a monitorização de um ou mais clusters do AKS já implementadas a partir da página de cluster multi AKS no Azure Monitor. | 
| | [Ativar a partir de um cluster do AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Pode ativar a monitorização diretamente a partir de um cluster do AKS no portal do Azure. | 
| | [Ativar através de um modelo Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Pode ativar a monitorização de um cluster do AKS com um modelo Azure Resource Manager previamente configurada. | 

## <a name="next-steps"></a>Passos Seguintes

* Com a monitorização ativada para capturar métricas de estado de funcionamento para os nós de cluster do AKS e os pods, estas métricas de estado de funcionamento estão disponíveis no portal do Azure. Para saber como utilizar o Azure Monitor para contentores, veja [estado de funcionamento do serviço de Kubernetes do Azure de modo de exibição](container-insights-analyze.md).
