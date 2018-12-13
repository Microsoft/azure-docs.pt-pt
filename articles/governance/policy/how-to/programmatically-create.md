---
title: Criar políticas e ver os dados de conformidade com o Azure Policy através de programação
description: Este artigo descreve a criação e gestão de políticas do Azure Policy a por meio de programação.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 881dc5c66f9b408d28a43fa16cff987a79a896a5
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084911"
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>Criar políticas e ver os dados de conformidade através de programação

Este artigo descreve a criação e a gestão de políticas por meio de programação. Definições de política impõem diferentes regras e efeitos aos recursos. Imposição certifica-se de que recursos mantêm-se em conformidade com os seus padrões empresariais e contratos de nível de serviço.

Para obter informações sobre a conformidade, consulte [obtenção de dados de conformidade](getting-compliance-data.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que são cumpridos os seguintes pré-requisitos:

1. Se ainda não o fez, instale o [ARMClient](https://github.com/projectkudu/ARMClient). É uma ferramenta que envia pedidos de HTTP para APIs baseadas no Azure Resource Manager.

1. Atualize o módulo do AzureRM PowerShell para a versão mais recente. Para obter mais informações sobre a versão mais recente, consulte [do Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

1. Registe o fornecedor de recursos do Policy Insights com o Azure PowerShell para validar que a sua subscrição funciona com o fornecedor de recursos. Para registar um fornecedor de recursos, tem de ter permissão para executar a operação de ação de registo para o fornecedor de recursos. Esta operação está incluída nas funções de Contribuinte e Proprietário. Execute o seguinte comando para registar o fornecedor de recursos:

   ```azurepowershell-interactive
   Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Para obter mais informações sobre como registar e visualizar os fornecedores de recursos, consulte [fornecedores de recursos e os tipos de](../../../azure-resource-manager/resource-manager-supported-services.md).

1. Se ainda não o fez, instale a CLI do Azure. Pode obter a versão mais recente na [instalar a CLI do Azure no Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Criar e atribuir uma definição de política

É o primeiro passo para uma melhor visibilidade dos seus recursos criar e atribuir políticas aos recursos. A próxima etapa é saber como criar e atribuir uma política. O exemplo de política audita contas de armazenamento que estão abertas para todas as redes públicas com o PowerShell, CLI do Azure e pedidos de HTTP.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Criar e atribuir uma definição de política com o PowerShell

1. Utilize o fragmento JSON seguinte para criar um ficheiro JSON com o nome AuditStorageAccounts.json.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Para obter mais informações sobre a criação de uma definição de política, consulte [estrutura de definição de política do Azure](../concepts/definition-structure.md).

1. Execute o seguinte comando para criar uma definição de política a utilizar o ficheiro de AuditStorageAccounts.json.

   ```azurepowershell-interactive
   New-AzureRmPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   O comando cria uma definição de política com o nome _auditoria armazenamento contas aberto a redes públicas_.
   Para obter mais informações sobre outros parâmetros que pode utilizar, consulte [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition).

   Quando chamado sem parâmetros de localização, `New-AzureRmPolicyDefinition` predefinições para guardar a definição de política na subscrição selecionada do contexto de sessões. Para guardar a definição para uma localização diferente, utilize os seguintes parâmetros:

   - **SubscriptionId** -guardar para uma subscrição diferente. Requer uma _GUID_ valor.
   - **ManagementGroupName** -salvar num grupo de gestão. Requer uma _cadeia de caracteres_ valor.

1. Depois de criar a definição de política, pode criar uma atribuição de política ao executar os comandos seguintes:

   ```azurepowershell-interactive
   $rg = Get-AzureRmResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzureRmPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzureRmPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Substitua _ContosoRG_ com o nome do seu grupo de recursos pretendido.

   O **âmbito** parâmetro no `New-AzureRmPolicyAssignment` também funciona com as subscrições e grupos de gestão. O parâmetro usa um caminho de recurso completo, que o **ResourceId** propriedade no `Get-AzureRmResourceGroup` devolve. O padrão para **âmbito** para cada contentor é da seguinte forma.
   Substitua `{rgName}`, `{subId}`, e `{mgName}` com seu recurso agrupar nome, o ID de subscrição e o nome do grupo de gestão, respectivamente.

   - Grupo de recursos- `/subscriptions/{subId}/resourceGroups/{rgName}`
   - Subscrição- `/subscriptions/{subId}/`
   - Grupo de gestão- `/providers/Microsoft.Management/managementGroups/{mgName}`

Para obter mais informações sobre a gestão de políticas de recursos com o módulo PowerShell do Azure Resource Manager, consulte [Azurerm](/powershell/module/azurerm.resources/#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Criar e atribuir uma definição de política com ARMClient

Utilize o procedimento seguinte para criar uma definição de política.

1. Copie o fragmento JSON seguinte para criar um ficheiro JSON. Chamará o ficheiro no próximo passo.

   ```json
   "properties": {
       "displayName": "Audit Storage Accounts Open to Public Networks",
       "policyType": "Custom",
       "mode": "Indexed",
       "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
       "parameters": {},
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Storage/storageAccounts"
                   },
                   {
                       "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                       "equals": "Allow"
                   }
               ]
           },
           "then": {
               "effect": "audit"
           }
       }
   }
   ```

1. Crie a definição de política a utilizar uma das seguintes chamadas:

   ```
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
   ```

   Substitua {subscriptionId} anterior com o ID da sua subscrição ou {managementGroupId} com o ID da sua [grupo de gestão](../../management-groups/overview.md).

   Para obter mais informações sobre a estrutura da consulta, consulte [definições de política – criar ou atualizar](/rest/api/resources/policydefinitions/createorupdate) e [definições de política – criação ou atualização no grupo de gestão](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

Utilize o procedimento seguinte para criar uma atribuição de política e atribuir a definição de política ao nível do grupo de recursos.

1. Copie o fragmento JSON seguinte para criar um ficheiro de atribuição de política JSON. Substituir informações de exemplo na &lt; &gt; símbolos pelos seus próprios valores.

   ```json
   {
       "properties": {
           "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
           "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
           "parameters": {},
           "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
           "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
       }
   }
   ```

1. Crie a atribuição de política através da seguinte chamada:

   ```
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
   ```

   Substituir informações de exemplo na &lt; &gt; símbolos pelos seus próprios valores.

   Para obter mais informações sobre como fazer chamadas HTTP para a API REST, consulte [recursos da API REST do Azure](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Criar e atribuir uma definição de política com a CLI do Azure

Para criar uma definição de política, utilize o seguinte procedimento:

1. Copie o fragmento JSON seguinte para criar um ficheiro de atribuição de política JSON.

  ```json
  {
      "if": {
          "allOf": [{
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
              },
              {
                  "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                  "equals": "Allow"
              }
          ]
      },
      "then": {
          "effect": "audit"
      }
  }
  ```

1. Execute o seguinte comando para criar uma definição de política:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

1. Utilize o seguinte comando para criar uma atribuição de política. Substituir informações de exemplo na &lt; &gt; símbolos pelos seus próprios valores.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

Pode obter o ID de definição de política com o PowerShell com o seguinte comando:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

O ID de definição de política para a definição de política que criou deve assemelhar-se o exemplo seguinte:

```
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Para obter mais informações sobre como pode gerir as políticas de recursos com a CLI do Azure, consulte [políticas de recursos do Azure CLI](/cli/azure/policy?view=azure-cli-latest).

## <a name="next-steps"></a>Passos Seguintes

Reveja os artigos seguintes para obter mais informações sobre os comandos e consultas neste artigo.

- [Recursos da API REST do Azure](/rest/api/resources/)
- [Módulos do Azure RM PowerShell](/powershell/module/azurerm.resources/#policies)
- [Comandos de política CLI do Azure](/cli/azure/policy?view=azure-cli-latest)
- [Fornecedor de recursos de informações de política referência da REST API](/rest/api/policy-insights)
- [Organizar os recursos com grupos de gestão do Azure](../../management-groups/overview.md)