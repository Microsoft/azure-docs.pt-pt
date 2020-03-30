---
title: Como ativar o Monitor Azure para contentores [ Microsoft Docs
description: Este artigo descreve como ativa e configura o Monitor Azure para contentores para que possa compreender como está a funcionar o seu contentor e quais os problemas relacionados com o desempenho.
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: 7aad7e7dd5ec2569377f9276c2e4793c7afd631a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275311"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Como ativar o Monitor Azure para contentores

Este artigo fornece uma visão geral das opções disponíveis para configurar o Azure Monitor para os contentores para monitorizar o desempenho das cargas de trabalho que são implantadas em ambientes kubernetes e alojadas em:

- [Serviço Azure Kubernetes](https://docs.microsoft.com/azure/aks/) (AKS)

- Aglomerados Kubernetes autogeridos hospedados em Azure usando [o aks engine](https://github.com/Azure/aks-engine).

- Aglomerados Kubernetes autogeridos hospedados em [Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) ou no local usando o AKS Engine.

- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

O Monitor Azure para contentores pode ser ativado para novas ou mais implantações existentes de Kubernetes utilizando os seguintes métodos suportados:

- Do portal Azure, Azure PowerShell, ou com Azure CLI

- Utilização de [Terrafora e AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem o seguinte:

- **Uma área de trabalho do Log Analytics.**

    O Azure Monitor para contentores suporta um espaço de trabalho log Analytics nas regiões listadas em Produtos Azure [por região.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)

    Pode criar um espaço de trabalho quando ativa a monitorização do seu novo cluster AKS ou deixar que a experiência de embarque crie um espaço de trabalho padrão no grupo de recursos padrão da subscrição do cluster AKS. Se você mesmo escolheu criá-lo, você pode criá-lo através do [Azure Resource Manager](../platform/template-workspace-configuration.md), através [do PowerShell,](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)ou no [portal Azure](../learn/quick-create-workspace.md). Para obter uma lista dos pares de mapeamento suportados utilizados para o espaço de trabalho predefinido, consulte o mapeamento da [Região para o Monitor Azure para contentores](container-insights-region-mapping.md).

- É membro da **função** de colaborador do Log Analytics para permitir a monitorização dos contentores. Para obter mais informações sobre como controlar o acesso a um espaço de trabalho de Log Analytics, consulte [Gerir espaços](../platform/manage-access.md)de trabalho .

- É membro do papel de **[Proprietário](../../role-based-access-control/built-in-roles.md#owner)** no recurso do cluster AKS.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* As métricas de Prometeu não são recolhidas por defeito. Antes de [configurar o agente](container-insights-prometheus-integration.md) para os recolher, é importante que reveja a [documentação](https://prometheus.io/) prometheus para entender o que pode ser raspado e os métodos suportados.

## <a name="supported-configurations"></a>Configurações suportadas

O seguinte é oficialmente suportado com o Monitor Azure para contentores.

- Ambientes: Azure Red Hat OpenShift, Kubernetes on-premis e AKS Engine on Azure e Azure Stack. Para mais informações, consulte o [AkS Engine em Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- As versões de Kubernetes e política de suporte são as mesmas que as versões de [AKS suportadas.](../../aks/supported-kubernetes-versions.md) 

## <a name="network-firewall-requirements"></a>Requisitos de firewall de rede

As informações na tabela seguinte listam as informações de configuração de procuração e firewall necessárias para que o agente contentorizado comunique com o Monitor Azure para os contentores. Todo o tráfego de rede do agente está de saída para o Monitor Azure.

|Recursos do Agente|Portas |
|--------------|------|
| *.ods.opinsights.azure.com | 443 |  
| *.oms.opinsights.azure.com | 443 | 
| *.blob.core.windows.net | 443 |
| dc.services.visualstudio.com | 443 |
| *.microsoftonline.com | 443 |
| *.monitoring.azure.com | 443 |
| login.microsoftonline.com | 443 |

As informações na tabela seguinte listam as informações de configuração proxy e firewall para a Azure China.

|Recursos do Agente|Portas |Descrição | 
|--------------|------|-------------|
| *.ods.opinsights.azure.cn | 443 | Ingestão de dados |
| *.oms.opinsights.azure.cn | 443 | Onboarding OMS |
| *.blob.core.windows.net | 443 | Usado para monitorizar a conectividade de saída. |
| microsoft.com | 80 | Usado para conectividade de rede. Isto só é necessário se a versão de imagem do agente for ciprod09262019 ou mais cedo. |
| dc.services.visualstudio.com | 443 | Para telemetria de agente usando Insights de aplicação de nuvem pública Azure. |

As informações na tabela seguinte listam as informações de configuração de procuração e firewall para o Governo dos EUA.

|Recursos do Agente|Portas |Descrição | 
|--------------|------|-------------|
| *.ods.opinsights.azure.us | 443 | Ingestão de dados |
| *.oms.opinsights.azure.us | 443 | Onboarding OMS |
| *.blob.core.windows.net | 443 | Usado para monitorizar a conectividade de saída. |
| microsoft.com | 80 | Usado para conectividade de rede. Isto só é necessário se a versão de imagem do agente for ciprod09262019 ou mais cedo. |
| dc.services.visualstudio.com | 443 | Para telemetria de agente usando Insights de aplicação de nuvem pública Azure. |

## <a name="components"></a>Componentes

A sua capacidade de monitorizar o desempenho baseia-se num agente de Log Analytics contentorizado para o Linux desenvolvido especificamente para o Monitor Azure para contentores. Este agente especializado recolhe dados de desempenho e evento de todos os nós do cluster, e o agente é automaticamente implantado e registado com o espaço de trabalho especificado log Analytics durante a implementação. A versão do agente é microsoft/oms:ciprod04202018 ou posterior, e é representada por uma data no seguinte formato: *mmddyyyy*.

>[!NOTE]
>Com a versão de pré-visualização do suporte do Windows Server para AKS, um cluster AKS com nós do Windows Server não tem um agente instalado para recolher dados e reencaminhar para o Monitor Azure. Em vez disso, um nó Linux implantado automaticamente no cluster como parte da implementação padrão recolhe e envia os dados para o Monitor Azure em nome de todos os nós do Windows no cluster.  
>

Quando uma nova versão do agente é lançada, é automaticamente atualizada nos seus clusters Kubernetes geridos hospedados no Serviço Azure Kubernetes (AKS). Para acompanhar as versões lançadas, consulte os [anúncios](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)de lançamento do agente .

>[!NOTE]
>Se já implementou um cluster AKS, permite a monitorização utilizando o Azure CLI ou um modelo de Gestor de Recursos Azure fornecido, como demonstrado mais tarde neste artigo. Não é `kubectl` possível utilizar para atualizar, eliminar, reutilizar ou implementar o agente.
>O modelo precisa de ser implantado no mesmo grupo de recursos que o cluster.

Ativa o Monitor Azure para os contentores utilizando um dos seguintes métodos descritos na tabela a seguir.

| Estado de Implantação | Método | Descrição |
|------------------|--------|-------------|
| Novo cluster AKS Kubernetes | [Criar cluster AKS usando o Azure CLI](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Pode ativar a monitorização de um novo cluster AKS que cria com o Azure CLI. |
| | [Criar cluster AKS usando terraforma](container-insights-enable-new-cluster.md#enable-using-terraform)| Pode ativar a monitorização de um novo cluster AKS que cria utilizando a ferramenta terrafora de código aberto. |
| | [Crie cluster OpenShift usando um modelo de Gestor de Recursos Azure](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | Pode ativar a monitorização de um novo cluster OpenShift que cria com um modelo de Gestor de Recursos Azure pré-configurado. |
| | [Criar cluster OpenShift usando o Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | Pode ativar a monitorização enquanto implementa um novo cluster OpenShift utilizando o Azure CLI. |
| Aglomerado aks kubernetes existente | [Ativar para cluster AKS utilizando o Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Pode ativar a monitorização de um cluster AKS já implantado utilizando o Azure CLI. |
| |[Ativar para o cluster AKS usando terrafora](container-insights-enable-existing-clusters.md#enable-using-terraform) | Pode ativar a monitorização de um cluster AKS já implantado utilizando a ferramenta terrafora de código aberto. |
| | [Ativar o cluster AKS do Monitor Azure](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Pode ativar a monitorização de um ou mais clusters AKS já implantados a partir da página multi-cluster no Monitor Azure. |
| | [Ativar a partir do cluster AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Pode ativar a monitorização diretamente de um cluster AKS no portal Azure. |
| | [Ativar para cluster AKS usando um modelo de Gestor de Recursos Azure](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Pode ativar a monitorização de um cluster AKS com um modelo de Gestor de Recursos Azure pré-configurado. |
| | [Ativar o cluster kubernetes híbrido](container-insights-hybrid-setup.md) | Pode ativar a monitorização de um motor AKS hospedado em Azure Stack ou para Kubernetes hospedados no local. |
| | [Ativar para cluster OpenShift usando um modelo de Gestor de Recursos Azure](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | Pode ativar a monitorização de um cluster OpenShift existente com um modelo de Gestor de Recursos Azure pré-configurado. |
| | [Ativar o cluster OpenShift do Monitor Azure](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Pode ativar a monitorização de um ou mais clusters OpenShift já implantados a partir da página multi-cluster no Monitor Azure. |

## <a name="next-steps"></a>Passos seguintes

- Com a monitorização ativada, pode começar a analisar o desempenho dos seus clusters Kubernetes hospedados no Serviço Azure Kubernetes (AKS), Azure Stack ou outro ambiente. Para aprender a utilizar o Monitor Azure para contentores, consulte o desempenho do [cluster View Kubernetes](container-insights-analyze.md).
