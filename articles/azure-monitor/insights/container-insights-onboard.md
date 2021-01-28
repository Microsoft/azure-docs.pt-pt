---
title: Ativar o Monitor Azure para contentores | Microsoft Docs
description: Este artigo descreve como ativar e configurar o Azure Monitor para contentores para que possa entender como o seu recipiente está a funcionar e quais as questões relacionadas com o desempenho que foram identificadas.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: f598b42f1a8d9fcb42f09d17e40850cf3a1282be
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943827"
---
# <a name="enable-azure-monitor-for-containers"></a>Ativar o Monitor Azure para contentores

Este artigo fornece uma visão geral das opções disponíveis para a criação do Azure Monitor para contentores para monitorizar o desempenho das cargas de trabalho que são implantadas nos ambientes de Kubernetes e hospedadas em:

- [Azure Kubernetes Service (AKS)](../../aks/index.yml)  
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) versões 3.x e 4.x  
- [Versão Red Hat OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) 4.x  
- Um [cluster Kubernetes ativado pelo Arco](../../azure-arc/kubernetes/overview.md)

Também pode monitorizar o desempenho das cargas de trabalho que são implantadas em clusters Kubernetes auto-geridos hospedados em:
- Azure, usando o [motor AKS](https://github.com/Azure/aks-engine)
- [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) ou no local, utilizando o motor AKS.

Pode ativar o Azure Monitor para contentores para uma nova implantação ou para uma ou mais implantações existentes de Kubernetes utilizando qualquer um dos seguintes métodos suportados:

- O portal do Azure
- Azure PowerShell
- A CLI do Azure
- [Terraform e AKS](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que cumpriu os seguintes requisitos:

- Tem um espaço de trabalho log analytics.

   O Azure Monitor para contentores suporta um espaço de trabalho Log Analytics nas regiões que estão listadas em [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)

   Pode criar um espaço de trabalho quando ativar a monitorização do seu novo cluster AKS, ou pode deixar que a experiência de embarque crie um espaço de trabalho predefinido no grupo de recursos predefinido da subscrição do cluster AKS. 
   
   Se optar por criar o espaço de trabalho, pode criá-lo através de: 
   - [Azure Resource Manager](../samples/resource-manager-workspace.md)
   - [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)
   - [O portal do Azure](../learn/quick-create-workspace.md) 
   
   Para obter uma lista dos pares de mapeamento suportados para o espaço de trabalho predefinido, consulte [o mapeamento da região para o Monitor Azure para recipientes](container-insights-region-mapping.md).

- É membro do grupo *de contribuintes Log Analytics* para permitir a monitorização do contentor. Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, consulte [Gerir espaços de trabalho.](../platform/manage-access.md)

- Você é membro do grupo [ *Proprietário*](../../role-based-access-control/built-in-roles.md#owner) no recurso de cluster AKS.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

- Para visualizar os dados de monitorização, é necessário ter o papel de [*leitor do Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) no espaço de trabalho Do Log Analytics, configurado com o Azure Monitor para contentores.

- As métricas prometheus não são recolhidas por defeito. Antes [de configurar o agente](container-insights-prometheus-integration.md) para recolher as métricas, é importante rever a [documentação do Prometeu](https://prometheus.io/) para entender que dados podem ser raspados e que métodos são suportados.

## <a name="supported-configurations"></a>Configurações suportadas

O Azure Monitor para contentores suporta oficialmente as seguintes configurações:

- Ambientes: Azure Red Hat OpenShift, Kubernetes no local e o motor AKS em Azure e Azure Stack. Para mais informações, consulte [o motor AKS no Azure Stack.](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)
- As versões de Kubernetes e a política de suporte são as mesmas que são [suportadas no Serviço Azure Kubernetes (AKS)](../../aks/supported-kubernetes-versions.md). 

## <a name="network-firewall-requirements"></a>Requisitos de firewall de rede

A tabela que se segue lista as informações de configuração de procuração e firewall necessárias para que o agente contentorizado comunique com o Azure Monitor para os contentores. Todo o tráfego de rede do agente é de saída para o Monitor Azure.

|Recursos do agente|Porta |
|--------------|------|
| `*.ods.opinsights.azure.com` | 443 |
| `*.oms.opinsights.azure.com` | 443 |
| `dc.services.visualstudio.com` | 443 |
| `*.monitoring.azure.com` | 443 |
| `login.microsoftonline.com` | 443 |

A tabela a seguir lista as informações de configuração proxy e firewall para Azure China 21Vianet:

|Recursos do agente|Porta |Descrição | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.cn` | 443 | Ingestão de dados |
| `*.oms.opinsights.azure.cn` | 443 | OMS a bordo |
| `dc.services.visualstudio.com` | 443 | Para telemetria de agente que usa Azure Public Cloud Application Insights |

A tabela a seguir lista as informações de configuração de procuração e firewall para o Governo dos EUA:

|Recursos do agente|Porta |Descrição | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.us` | 443 | Ingestão de dados |
| `*.oms.opinsights.azure.us` | 443 | OMS a bordo |
| `dc.services.visualstudio.com` | 443 | Para telemetria de agente que usa Azure Public Cloud Application Insights |

## <a name="components"></a>Componentes

A sua capacidade de monitorizar o desempenho baseia-se num agente de Log Analytics contentorizado para o Linux que é especificamente desenvolvido para o Azure Monitor para contentores. Este agente especializado recolhe dados de desempenho e evento de todos os nós do cluster, e o agente é automaticamente implantado e registado com o espaço de trabalho do Log Analytics especificado durante a implementação. 

A versão do agente é microsoft/oms:ciprod04202018 ou posterior, e é representada por uma data no seguinte formato: *mmddyyyy*.

>[!NOTE]
>Com a disponibilidade geral do suporte do Windows Server para AKS, um cluster AKS com nós do Windows Server tem um agente de pré-visualização instalado como um pod de um daemonset em cada nó de servidor do Windows para recolher registos e reencaminhá-lo para o Log Analytics. Para métricas de desempenho, um nó Linux que é automaticamente implantado no cluster como parte da implementação padrão recolhe e encaminha os dados para o Azure Monitor em nome de todos os nós windows no cluster.

Quando uma nova versão do agente é lançada, é automaticamente atualizada nos seus clusters geridos de Kubernetes que são hospedados no Azure Kubernetes Service (AKS). Para saber quais versões são lançadas, consulte [os anúncios de lançamento do agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

> [!NOTE]
> Se já implementou um cluster AKS, ativou a monitorização utilizando o Azure CLI ou um modelo fornecido do Azure Resource Manager, como demonstrado mais tarde neste artigo. Não pode usar `kubectl` para atualizar, eliminar, recolocar ou implantar o agente.
>
> O modelo precisa de ser implantado no mesmo grupo de recursos que o cluster.

Para ativar o Azure Monitor para recipientes, utilize um dos métodos descritos na tabela seguinte:

| Estado de implantação | Método | Descrição |
|------------------|--------|-------------|
| Novo cluster Kubernetes | [Criar um cluster AKS utilizando o CLI Azure](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Pode ativar a monitorização de um novo cluster AKS que cria utilizando o Azure CLI. |
| | [Criar um cluster AKS utilizando o Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Pode ativar a monitorização de um novo cluster AKS que cria utilizando a ferramenta de código aberto Terraform. |
| | [Crie um cluster OpenShift usando um modelo de Gestor de Recursos Azure](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | Pode ativar a monitorização de um novo cluster OpenShift que cria utilizando um modelo pré-configurado do Gestor de Recursos Azure. |
| | [Criar um cluster OpenShift utilizando o CLI Azure](/cli/azure/openshift#az-openshift-create) | Pode ativar a monitorização quando implementar um novo cluster OpenShift utilizando o Azure CLI. |
| Cluster Kubernetes existente | [Permitir a monitorização de um cluster AKS utilizando o CLI Azure](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Pode ativar a monitorização de um cluster AKS que já foi implantado utilizando o Azure CLI. |
| |[Ativar o cluster AKS utilizando o Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Pode ativar a monitorização de um cluster AKS que já foi implantado utilizando a ferramenta de código aberto Terraform. |
| | [Ativar o cluster AKS a partir do Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Pode ativar a monitorização de um ou mais clusters AKS que já estão implantados a partir da página multi-cluster no Azure Monitor. |
| | [Ativar a partir do cluster AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Pode ativar a monitorização diretamente a partir de um cluster AKS no portal Azure. |
| | [Ative o cluster AKS usando um modelo de gestor de recursos Azure](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Pode ativar a monitorização de um cluster AKS utilizando um modelo pré-configurado do Gestor de Recursos Azure. |
| | [Ativar o cluster híbrido Kubernetes](container-insights-hybrid-setup.md) | Você pode ativar a monitorização para o motor AKS que está hospedado em Azure Stack ou para um cluster Kubernetes que está hospedado no local. |
| | [Ativar o cluster Kubernetes ativado pelo Arco.](container-insights-enable-arc-enabled-clusters.md) | Pode ativar a monitorização dos seus clusters Kubernetes que estão alojados fora de Azure e ativados com Azure Arc. |
| | [Ative o cluster OpenShift utilizando um modelo de Gestor de Recursos Azure](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | Pode ativar a monitorização de um cluster OpenShift existente utilizando um modelo pré-configurado do Gestor de Recursos Azure. |
| | [Ativar o cluster OpenShift a partir do Azure Monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Pode ativar a monitorização de um ou mais clusters OpenShift que já estão implantados a partir da página multicluster no Azure Monitor. |

## <a name="next-steps"></a>Próximos passos

Agora que ativou a monitorização, pode começar a analisar o desempenho dos seus clusters Kubernetes que estão hospedados no Azure Kubernetes Service (AKS), Azure Stack ou noutro ambiente. Para aprender a utilizar o Azure Monitor para recipientes, consulte [o desempenho do cluster Kubernetes](container-insights-analyze.md).
