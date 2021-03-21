---
title: 'Quickstart: Nova atribuição de políticas com Python'
description: Neste quickstart, você usa Python para criar uma atribuição de Política Azure para identificar recursos não conformes.
ms.date: 03/02/2021
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: e600f97dafdd1040c22b6e4d9e333f638334b663
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101742352"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-python"></a>Quickstart: Criar uma atribuição de política para identificar recursos não conformes usando Python

O primeiro passo para compreender a conformidade no Azure consiste em identificar o estado dos seus recursos. Neste arranque rápido, cria-se uma atribuição de políticas para identificar máquinas virtuais que não estão a usar discos geridos. Quando estiver concluído, identificará máquinas virtuais que _não são compatíveis._

A biblioteca Python é usada para gerir os recursos de Azure a partir da linha de comando ou em scripts. Este guia explica como usar a biblioteca Python para criar uma atribuição de políticas.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-library"></a>Adicione a biblioteca política

Para permitir que python trabalhe com a Política Azure, a biblioteca deve ser adicionada. Esta biblioteca funciona onde quer que python possa ser usado, incluindo [bash no Windows 10](/windows/wsl/install-win10) ou instalado localmente.

1. Verifique se o último Python está instalado (pelo menos **3.8**). Se ainda não estiver instalado, descarregue-o em [Python.org](https://www.python.org/downloads/).

1. Verifique se o último CLI do Azure está instalado (pelo menos **2.5.1**). Se ainda não estiver instalado, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

   > [!NOTE]
   > O Azure CLI é necessário para permitir que a Python utilize a **autenticação baseada** em CLI nos seguintes exemplos. Para obter informações sobre outras opções, consulte [Authenticate utilizando as bibliotecas de gestão Azure para Python.](/azure/developer/python/azure-sdk-authenticate)

1. Autenticar através do Azure CLI.

   ```azurecli
   az login
   ```

1. No seu ambiente python de eleição, instale as bibliotecas necessárias para a Política Azure:

   ```bash
   # Add the Python library for Python
   pip install azure-mgmt-policyinsights

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Se o Python estiver instalado para todos os utilizadores, estes comandos devem ser executados a partir de uma consola elevada.

1. Valide que as bibliotecas foram instaladas. `azure-mgmt-policyinsights` deve ser **0,5.0** ou superior, `azure-mgmt-resource` deve ser **9.0.0** ou superior, e `azure-cli-core` deve ser **2,5.0** ou superior.

   ```bash
   # Check each installed library
   pip show azure-mgmt-policyinsights azure-mgmt-resource azure-cli-core
   ```

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste arranque rápido, cria-se uma atribuição de política e atribui os **VMs de auditoria que não utilizam discos geridos** `06a78e20-9358-41c9-923c-fb736d382a4d` () definição. Esta definição de política identifica recursos que não estão em conformidade com as condições definidas na definição de política.

Executar o seguinte código para criar uma nova atribuição de política:

```python
# Import specific methods and models from other libraries
from azure.common.credentials import get_azure_cli_credentials
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient
from azure.mgmt.resource.policy.models import PolicyAssignment

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Create details for the assignment
policyAssignmentDetails = PolicyAssignment(display_name="Audit VMs without managed disks Assignment", policy_definition_id="/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d", scope="{scope}", description="Shows all virtual machines not using managed disks")

# Create new policy assignment
policyAssignment = policyClient.policy_assignments.create("{scope}", "audit-vm-manageddisks", policyAssignmentDetails)

# Show results
print(policyAssignment)
```

Os comandos anteriores utilizam as seguintes informações:

Detalhes da atribuição:
- **display_name** - Mostrar o nome para a atribuição de apólices. Neste caso, está a utilizar _VMs de auditoria sem serviço de discos geridos_.
- **policy_definition_id** – O caminho de definição de política, baseado no qual está a usar para criar a atribuição. Neste caso, é o ID de definição de política _Audit VMs que não usam discos geridos_. Neste exemplo, a definição de política é um incorporado e o caminho não inclui o grupo de gestão ou informações de subscrição.
- **âmbito** - Um âmbito determina quais os recursos ou agrupamento de recursos em que a atribuição de políticas é executada. Pode ir de um grupo de gestão a um recurso individual. Certifique-se de que `{scope}` substitui por um dos seguintes padrões:
  - Grupo de gestão: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Subscrição: `/subscriptions/{subscriptionId}`
  - Grupo de recursos: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Recurso: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`
- **descrição** - Uma explicação mais profunda do que a apólice faz ou por que é atribuída a este âmbito.

Criação de atribuição:

- Âmbito - Este âmbito determina onde a atribuição de política é guardada. O âmbito definido nos dados de atribuição deve existir neste âmbito.
- Nome – O nome real da atribuição. Neste exemplo, foi utilizado _audit-vm-manageddisks_.
- Atribuição de políticas - O objeto de **assinatura da Política** Python criado no passo anterior.

Está agora pronto para identificar recursos não conformes para entender o estado de conformidade do seu ambiente.

## <a name="identify-non-compliant-resources"></a>Identificar recursos que não estão em conformidade

Utilize as seguintes informações para identificar recursos que não estão em conformidade com a atribuição de política que criou. Execute o seguinte código:

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.policyinsights._policy_insights_client import PolicyInsightsClient
from azure.mgmt.policyinsights.models import QueryOptions

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyInsightsClient = get_client_from_cli_profile(PolicyInsightsClient)

# Set the query options
queryOptions = QueryOptions(filter="IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'",apply="groupby((ResourceId))")

# Fetch 'latest' results for the subscription
results = policyInsightsClient.policy_states.list_query_results_for_subscription(policy_states_resource="latest", subscription_id="{subscriptionId}", query_options=queryOptions)

# Show results
print(results)
```

`{subscriptionId}`Substitua-a pela subscrição deseja ver os resultados de conformidade desta atribuição de política. Para obter uma lista de outros âmbitos e formas de resumir os dados, consulte os [métodos do Estado da Política](/python/api/azure-mgmt-policyinsights/azure.mgmt.policyinsights.operations.policystatesoperations#methods).

Os resultados assemelham-se ao seguinte exemplo:

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

Os resultados coincidem com o que vê no **separador** de conformidade de recursos de uma atribuição de políticas na vista do portal Azure.

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover a atribuição criada, utilize o seguinte comando:

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Delete the policy assignment
policyAssignment = policyClient.policy_assignments.delete("{scope}", "audit-vm-manageddisks")

# Show results
print(policyAssignment)
```

`{scope}`Substitua-o pelo mesmo âmbito que usou para criar a atribuição de políticas.

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, atribuiu uma definição de política para identificar recursos incompatíveis no seu ambiente do Azure.

Para saber mais sobre a atribuição de definições políticas para validar que os novos recursos estão em conformidade, continue ao tutorial para:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./tutorials/create-and-manage.md)
