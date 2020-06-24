---
title: Como ativar o Monitor Azure para contentores Microsoft Docs
description: Este artigo descreve como ativa e configura o Azure Monitor para contentores para que possa compreender como o seu recipiente está a funcionar e quais as questões relacionadas com o desempenho.
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: a765c601682eb594d40ba98b8b4ef1853f35fb37
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84886017"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Como ativar o Monitor Azure para contentores

Este artigo fornece uma visão geral das opções disponíveis para configurar o Azure Monitor para contentores para monitorizar o desempenho das cargas de trabalho que são implantadas em ambientes kubernetes e hospedadas em:

- [Serviço Azure Kubernetes](https://docs.microsoft.com/azure/aks/) (AKS)

- Clusters Kubernetes auto-geridos hospedados em Azure usando [motor AKS](https://github.com/Azure/aks-engine).

- Clusters Kubernetes auto-geridos hospedados em [Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) ou no local usando motor AKS.

- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) versão 3.x e 4.x

- [Versão Red Hat OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) 4.x

- [Arco habilitado aglomerado kubernetes](../../azure-arc/kubernetes/overview.md)

O Monitor Azure para contentores pode ser ativado para novas ou mais implantações existentes de Kubernetes utilizando os seguintes métodos suportados:

- Do portal Azure, Azure PowerShell, ou com Azure CLI

- Usando [Terraform e AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem o seguinte:

- **Uma área de trabalho do Log Analytics.**

   O Azure Monitor para contentores suporta um espaço de trabalho Log Analytics nas regiões listadas nos Produtos Azure [por região.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)

   Pode criar um espaço de trabalho quando ativar a monitorização do seu novo cluster AKS ou deixar que a experiência de embarque crie um espaço de trabalho predefinido no grupo de recursos predefinido da subscrição do cluster AKS. Se optar por criá-lo por si mesmo, pode criá-lo através [do Azure Resource Manager,](../platform/template-workspace-configuration.md)através da [PowerShell,](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)ou no [portal Azure](../learn/quick-create-workspace.md). Para obter uma lista dos pares de mapeamento suportados utilizados para o espaço de trabalho predefinido, consulte [o mapeamento da região para o Monitor Azure para recipientes](container-insights-region-mapping.md).

- É membro da **função de contribuinte Log Analytics** para permitir a monitorização do contentor. Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, consulte [Gerir espaços de trabalho.](../platform/manage-access.md)

- Você é membro do papel **[proprietário](../../role-based-access-control/built-in-roles.md#owner)** no recurso cluster AKS.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

- As métricas prometheus não são recolhidas por defeito. Antes [de configurar o agente](container-insights-prometheus-integration.md) para os recolher, é importante que reveja a [documentação](https://prometheus.io/) de Prometeu para entender o que pode ser raspado e os métodos suportados.

## <a name="supported-configurations"></a>Configurações suportadas

O seguinte é oficialmente apoiado com o Azure Monitor para contentores.

- Ambientes: Azure Red Hat OpenShift, Kubernetes no local e Motor AKS em Azure e Azure Stack. Para mais informações, consulte [o motor AKS em Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- As versões de Kubernetes e a política de suporte são as mesmas que as versões [suportadas pela AKS.](../../aks/supported-kubernetes-versions.md) 

## <a name="network-firewall-requirements"></a>Requisitos de firewall de rede

As informações na tabela que se segue listam as informações de configuração de procuração e firewall necessárias para que o agente contentorizado comunique com o Azure Monitor para os contentores. Todo o tráfego de rede do agente é de saída para o Monitor Azure.

|Recursos do Agente|Portas |
|--------------|------|
| `*.ods.opinsights.azure.com` | 443 |  
| `*.oms.opinsights.azure.com` | 443 |
| `dc.services.visualstudio.com` | 443 |
| `*.monitoring.azure.com` | 443 |
| `login.microsoftonline.com` | 443 |

As informações na tabela seguinte listam as informações de configuração de proxy e firewall para Azure China 21Vianet.

|Recursos do Agente|Portas |Description | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.cn` | 443 | Ingestão de dados |
| `*.oms.opinsights.azure.cn` | 443 | OMS a bordo |
| `dc.services.visualstudio.com` | 443 | Para telemetria de agente usando Azure Public Cloud Application Insights. |

As informações na tabela seguinte listam as informações de configuração de procuração e firewall para o Governo dos EUA do Azure.

|Recursos do Agente|Portas |Description | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.us` | 443 | Ingestão de dados |
| `*.oms.opinsights.azure.us` | 443 | OMS a bordo |
| `dc.services.visualstudio.com` | 443 | Para telemetria de agente usando Azure Public Cloud Application Insights. |

## <a name="components"></a>Componentes

A sua capacidade de monitorizar o desempenho baseia-se num agente de Log Analytics contentorizado para o Linux especificamente desenvolvido para o Azure Monitor para contentores. Este agente especializado recolhe dados de desempenho e evento de todos os nós do cluster, e o agente é automaticamente implantado e registado com o espaço de trabalho do Log Analytics especificado durante a implementação. A versão do agente é microsoft/oms:ciprod04202018 ou posterior, e é representada por uma data no seguinte formato: *mmddyyyy*.

>[!NOTE]
>Com a versão de pré-visualização do suporte do Windows Server para AKS, um cluster AKS com nós do Windows Server não tem um agente instalado para recolher dados e encaminhar para o Azure Monitor. Em vez disso, um nó Linux implantado automaticamente no cluster como parte da implementação padrão recolhe e encaminha os dados para o Azure Monitor em nome de todos os nós windows no cluster.  
>

Quando uma nova versão do agente é lançada, é automaticamente atualizada nos seus clusters geridos de Kubernetes alojados no Serviço Azure Kubernetes (AKS). Para acompanhar as versões lançadas, consulte os [anúncios de lançamento do agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

>[!NOTE]
>Se já implementou um cluster AKS, pode ser monitorizado utilizando o Azure CLI ou um modelo de Gestor de Recursos Azure, como demonstrado mais tarde neste artigo. Não é possível utilizar `kubectl` para atualizar, eliminar, re-implantar ou implantar o agente.
>O modelo precisa de ser implantado no mesmo grupo de recursos que o cluster.

Ativar o Azure Monitor para recipientes utilizando um dos seguintes métodos descritos no quadro seguinte.

| Estado de implantação | Método | Description |
|------------------|--------|-------------|
| Novo cluster Kubernetes | [Criar cluster AKS usando Azure CLI](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Pode ativar a monitorização de um novo cluster AKS que cria com o Azure CLI. |
| | [Criar cluster AKS usando Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Pode ativar a monitorização de um novo cluster AKS que cria utilizando a ferramenta de código aberto Terraform. |
| | [Crie cluster OpenShift usando um modelo de gestor de recursos Azure](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | Pode ativar a monitorização de um novo cluster OpenShift que cria com um modelo pré-configurado do Gestor de Recursos Azure. |
| | [Criar cluster OpenShift usando Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | Pode ativar a monitorização durante a implementação de um novo cluster OpenShift utilizando o Azure CLI. |
| Cluster Kubernetes existente | [Ativar o cluster AKS utilizando o Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Pode ativar a monitorização de um cluster AKS já implantado utilizando o Azure CLI. |
| |[Ativar o cluster AKS utilizando o Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Pode ativar a monitorização de um cluster AKS já implantado utilizando a ferramenta de código aberto Terraform. |
| | [Ativar o cluster AKS a partir do Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Pode ativar a monitorização de um ou mais clusters AKS já implantados a partir da página multi-cluster no Azure Monitor. |
| | [Ativar a partir do cluster AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Pode ativar a monitorização diretamente a partir de um cluster AKS no portal Azure. |
| | [Ative o cluster AKS usando um modelo de gestor de recursos Azure](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Pode ativar a monitorização de um cluster AKS com um modelo pré-configurado do Gestor de Recursos Azure. |
| | [Ativar o cluster híbrido Kubernetes](container-insights-hybrid-setup.md) | Pode ativar a monitorização de um motor AKS hospedado em Azure Stack ou para Kubernetes hospedado no local. |
| | [Ativar o cluster Kubernetes ativado pelo Arco.](container-insights-enable-arc-enabled-clusters.md) | Pode ativar a monitorização dos seus clusters Kubernetes alojados fora do Azure que estão ativados com o Arco Azure. |
| | [Ative o cluster OpenShift utilizando um modelo de Gestor de Recursos Azure](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | Pode ativar a monitorização de um cluster OpenShift existente com um modelo de Gestor de Recursos Azure pré-configurado. |
| | [Ativar o cluster OpenShift a partir do Azure Monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Pode ativar a monitorização de um ou mais clusters OpenShift já implantados a partir da página multi-cluster no Azure Monitor. |

## <a name="next-steps"></a>Passos seguintes

- Com a monitorização ativada, pode começar a analisar o desempenho dos seus clusters Kubernetes hospedados no Serviço Azure Kubernetes (AKS), Azure Stack ou outro ambiente. Para aprender a utilizar o Azure Monitor para recipientes, consulte [o desempenho do cluster Kubernetes](container-insights-analyze.md).
