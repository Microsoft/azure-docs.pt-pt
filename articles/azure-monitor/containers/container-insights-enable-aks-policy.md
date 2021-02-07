---
title: Ativar addon de monitorização AKS usando a política de Azure
description: Descreve como ativar o Addon de monitorização AKS utilizando a Azure Custom Policy.
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 302fdbbbcadf211339952f4b1bd97dcbb4ab1a85
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808417"
---
# <a name="enable-aks-monitoring-addon-using-azure-policy"></a>Ativar o addon de monitorização AKS usando a política de Azure
Este artigo descreve como ativar o Addon de monitorização AKS usando a Azure Custom Policy. A política personalizada de monitorização addon pode ser atribuída no âmbito de subscrição ou grupo de recursos. Se o espaço de trabalho do Azure Log Analytics e o cluster AKS estiverem em diferentes subscrições, então a identidade gerida utilizada pela atribuição de políticas tem de ter as permissões de função necessárias tanto nas subscrições como, menos, no recurso do espaço de trabalho Log Analytics. Da mesma forma, se a política for traçada para o grupo de recursos, então a identidade gerida deve ter as permissões de função necessárias no espaço de trabalho Log Analytics se o espaço de trabalho não estiver no âmbito do grupo de recursos selecionado.

A Monitorização do Addon requer as seguintes funções na identidade gerida utilizada pela Azure Policy:

 - [azure-kubernetes-service-contributor-role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-contributor-role)
 - [log-analytics-contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-contributor)

## <a name="create-and-assign-policy-definition-using-azure-portal"></a>Criar e atribuir definição de política usando o portal Azure

### <a name="create-policy-definition"></a>Criar definição de política

1. Descarregue a definição de Política Personalizada Azure para ativar o Addon de monitorização AKS.
 
    ``` sh
    curl -o azurepolicy.json -L https://aka.ms/aks-enable-monitoring-custom-policy
    ```

3. Navegue https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions e crie definição de política com os seguintes detalhes na definição de política criar caixa de diálogo.
 
    - **Localização de definição**: Escolha a subscrição Azure onde a definição de política deve ser armazenada.
    - **Nome**: *(Preview)AKS-Monitoring-Addon*
    - **Descrição**: *Política Personalizada Azure para permitir o Addon de Monitorização no Cluster Azure Kubernetes em âmbito especificado*
    - **Categoria**: Escolha *a utilização existente* e escolha *Kubernetes* do drop-down.
    - **Regra de Política**: Remova as regras de amostra existentes e copie o conteúdo de *azurepolicy.js* descarregado #1 passo acima.

### <a name="assign-policy-definition-to-specified-scope"></a>Atribuir a definição de política ao âmbito especificado

> [!NOTE]
>  A identidade gerida será criada automaticamente e atribuídas funções especificadas na definição de Política.

1. Selecione a definição de política *(Preview) AKS Monitoring Addon* que acabou de criar.
4. Clique *em Atribuir*** e especifique um **âmbito** de onde a apólice deve ser atribuída. 
5. Clique **em seguida** e forneça o ID de recurso do espaço de trabalho Azure Log Analytics. O ID de recursos deve estar neste `/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>` formato.
6. Crie uma tarefa de reparação no caso de pretender aplicar a política aos clusters AKS existentes no âmbito selecionado.
7. Clique **em Rever + Criar** opção para criar a atribuição de políticas.
   
## <a name="create-and-assign-policy-definition-using-azure-cli"></a>Criar e atribuir definição de política usando O Azure CLI

### <a name="create-policy-definition"></a>Criar definição de política

1. Descarregue as regras de definição de política personalizada Azure e os ficheiros de parâmetros com os seguintes comandos:

    ``` sh
    curl -o azurepolicy.rules.json -L https://aka.ms/aks-enable-monitoring-custom-policy-rules
    curl -o azurepolicy.parameters.json -L https://aka.ms/aks-enable-monitoring-custom-policy-parameters
    ```

2. Criar a definição de política com o seguinte comando:

    ``` sh
    az cloud set -n <AzureCloud | AzureChinaCloud | AzureUSGovernment> # set the Azure cloud
    az login # login to cloud environment 
    az account set -s <subscriptionId>
    az policy definition create --name "(Preview)AKS-Monitoring-Addon" --display-name "(Preview)AKS-Monitoring-Addon" --mode Indexed --metadata version=1.0.0 category=Kubernetes --rules azurepolicy.rules.json --params azurepolicy.parameters.json
    ```

### <a name="assign-policy-definition-to-specified-scope"></a>Atribuir a definição de política ao âmbito especificado

- Criar a atribuição de política com o seguinte comando:

    ``` sh
    az policy assignment create --name aks-monitoring-addon --policy "(Preview)AKS-Monitoring-Addon" --assign-identity --identity-scope /subscriptions/<subscriptionId> --role Contributor --scope /subscriptions/<subscriptionId> --location <locatio> --role Contributor --scope /subscriptions/<subscriptionId> -p "{ \"workspaceResourceId\": { \"value\":  \"/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>\" } }"
    ```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Política Azure.](../../governance/policy/overview.md)
- Saiba como [funciona a segurança da reparação.](../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works)
- Saiba mais sobre [o Azure Monitor para Contentores.](../insights/container-insights-overview.md)
- Instale o [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

