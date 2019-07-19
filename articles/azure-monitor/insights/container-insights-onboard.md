---
title: Como habilitar o Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como habilitar e configurar Azure Monitor para contêineres para que você possa entender como o contêiner está sendo executado e quais problemas relacionados ao desempenho foram identificados.
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
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: 25be8f166fec8a311fdc2ed1fa3fca6339185e94
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67867520"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Como habilitar o Azure Monitor para contêineres

Este artigo fornece uma visão geral das opções disponíveis para configurar Azure Monitor para contêineres para monitorar o desempenho de cargas de trabalho implantadas em ambientes kubernetes e hospedadas no [serviço kubernetes do Azure](https://docs.microsoft.com/azure/aks/).

Monitor do Azure para contentores pode ser ativado para a nova ou suportada de um ou mais implementações existentes do AKS com os seguintes métodos:

* Na portal do Azure, Azure PowerShell ou com CLI do Azure
* Usando [Terraform e AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de que tem o seguinte:

* **Um espaço de trabalho Log Analytics.**

    Azure Monitor para contêineres dá suporte a um espaço de trabalho Log Analytics nas regiões listadas em produtos do Azure [por região](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor), excluindo a região **US gov-Virgínia**.

    Você pode criar um espaço de trabalho ao habilitar o monitoramento do novo cluster AKS ou permitir que a experiência de integração crie um espaço de trabalho padrão no grupo de recursos padrão da assinatura do cluster AKS. Se optar por criá-lo, pode criá-lo através de [do Azure Resource Manager](../platform/template-workspace-configuration.md), da funcionalidade [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), ou no [portal do Azure](../learn/quick-create-workspace.md). Para obter uma lista dos pares de mapeamento com suporte usados para o espaço de trabalho padrão, consulte [mapeamento de região para Azure monitor para contêineres](container-insights-region-mapping.md).

* Você é membro da **função colaborador de log Analytics** para habilitar o monitoramento de contêiner. Para obter mais informações sobre como controlar o acesso a uma área de trabalho do Log Analytics, consulte [gerir áreas de trabalho](../platform/manage-access.md).

* Você é membro da função **[proprietário](../../role-based-access-control/built-in-roles.md#owner)** no recurso de cluster AKs.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* As métricas de Prometheus não são coletadas por padrão. Antes de [Configurar o agente](container-insights-agent-config.md) para coletá-los, é importante examinar a [documentação](https://prometheus.io/) do Prometheus para entender o que você pode definir.

## <a name="components"></a>Componentes

Sua capacidade de monitorizar o desempenho se baseia num agente de Log Analytics em contentores de Linux desenvolvido especificamente para o Azure Monitor para contentores. Este agente especializada recolhe dados de eventos de desempenho e de todos os nós do cluster e o agente automaticamente implementar e registar com a área de trabalho do Log Analytics especificada durante a implementação. A versão do agente é microsoft / oms:ciprod04202018 ou posterior e é representado por uma data no seguinte formato: *mmddyyyy*.

>[!NOTE]
>Com a versão de visualização do suporte do Windows Server para AKS, um cluster AKS com nós do Windows Server não tem um agente instalado para coletar dados e encaminhá-los para Azure Monitor. Em vez disso, um nó do Linux implantado automaticamente no cluster como parte da implantação padrão coleta e encaminha os dados para Azure Monitor em nome de todos os nós do Windows no cluster.  
>

Quando for lançada uma nova versão do agente, é atualizada automaticamente nos seus clusters do Kubernetes geridos alojados no Azure Kubernetes Service (AKS). Para seguir as versões lançadas, consulte [anúncios de lançamentos de agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

>[!NOTE]
>Se já tiver implementado um cluster do AKS, ativa a monitorização com o CLI do Azure ou um modelo Azure Resource Manager fornecido, conforme demonstrado neste artigo. Não é possível utilizar `kubectl` para atualizar, eliminar, implemente novamente ou implementar o agente.
>O modelo precisa ser implantado no mesmo grupo de recursos do cluster.

Habilite Azure Monitor para contêineres usando um dos seguintes métodos descritos na tabela a seguir.

| Estado da implantação | Método | Descrição |
|------------------|--------|-------------|
| Novo cluster AKS | [Criar cluster usando CLI do Azure](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Você pode habilitar o monitoramento de um novo cluster AKS criado com CLI do Azure. |
| | [Criar cluster usando Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Você pode habilitar o monitoramento de um novo cluster AKS que você cria usando a ferramenta de código-fonte aberto Terraform. |
| Cluster AKS existente | [Habilitar usando CLI do Azure](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Você pode habilitar o monitoramento de um cluster AKS já implantado usando CLI do Azure. |
| |[Habilitar usando Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Você pode habilitar o monitoramento de um cluster AKS já implantado usando a ferramenta de código-fonte aberto Terraform. |
| | [Habilitar do Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Você pode habilitar o monitoramento de um ou mais clusters AKS já implantados na página de vários clusters do AKS no Azure Monitor. |
| | [Habilitar do cluster AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Você pode habilitar o monitoramento diretamente de um cluster AKS no portal do Azure. |
| | [Habilitar usando um modelo de Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Você pode habilitar o monitoramento de um cluster AKS com um modelo de Azure Resource Manager pré-configurado. |

## <a name="next-steps"></a>Passos Seguintes

* Com a monitorização ativada para capturar métricas de estado de funcionamento para os nós de cluster do AKS e os pods, estas métricas de estado de funcionamento estão disponíveis no portal do Azure. Para saber como utilizar o Azure Monitor para contentores, veja [estado de funcionamento do serviço de Kubernetes do Azure de modo de exibição](container-insights-analyze.md).
