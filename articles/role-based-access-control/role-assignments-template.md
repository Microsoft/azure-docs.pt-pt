---
title: Gerir o acesso aos recursos do Azure com modelos RBAC e do Azure Resource Manager | Documentos da Microsoft
description: Saiba como gerir o acesso aos recursos do Azure para utilizadores, grupos e aplicações que utilizam o controlo de acesso baseado em funções (RBAC) e os modelos Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 537ee35e96a41cd02605319e244d39c6567c3bf1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60344605"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>Gerir o acesso aos recursos do Azure com modelos RBAC e do Azure Resource Manager

[Controlo de acesso baseado em funções (RBAC)](overview.md) é a maneira que gerencie o acesso aos recursos do Azure. Além de utilizar o Azure PowerShell ou a CLI do Azure, pode gerir o acesso aos recursos do Azure através do RBAC e [modelos Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Modelos podem ser útil se precisar de implementar recursos de forma consistente e repetida. Este artigo descreve como pode gerir o acesso com RBAC e modelos.

## <a name="example-template-to-create-a-role-assignment"></a>Modelo de exemplo para criar uma atribuição de função

No RBAC, para conceder acesso, crie uma atribuição de função. O modelo seguinte demonstra:
- Como atribuir uma função a um utilizador, grupo ou aplicação no âmbito do grupo de recursos
- Como especificar as funções de proprietário, Contribuidor e leitor como um parâmetro

Para utilizar o modelo, tem de especificar as seguintes entradas:
- O nome de um grupo de recursos
- O identificador exclusivo de um utilizador, grupo ou aplicação para atribuir a função
- A função para atribuir
- Um identificador exclusivo que será utilizado para a atribuição de função

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The principal to assign the role to"
      }
    },
    "builtInRoleType": {
      "type": "string",
      "allowedValues": [
        "Owner",
        "Contributor",
        "Reader"
      ],
      "metadata": {
        "description": "Built-in role to assign"
      }
    },
    "roleNameGuid": {
      "type": "string",
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    }
  },
  "variables": {
    "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
    "scope": "[resourceGroup().id]"
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2017-05-01",
      "name": "[parameters('roleNameGuid')]",
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('scope')]"
      }
    }
  ]
}
```

O código a seguir mostra um exemplo de um leitor de atribuição de função a um utilizador depois de implementar o modelo.

![Atribuição de função com um modelo](./media/role-assignments-template/role-assignment-template.png)

## <a name="deploy-template-using-azure-powershell"></a>Implementar o modelo com o Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Para implementar o modelo anterior com o Azure PowerShell, siga estes passos.

1. Crie um novo ficheiro designado rbac rg.json e copie o modelo anterior.

1. Iniciar sessão no [Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Obter o identificador exclusivo de um utilizador, grupo ou aplicação. Por exemplo, pode utilizar o [Get-AzADUser](/powershell/module/az.resources/get-azaduser) command para listar os utilizadores do Azure AD.

    ```azurepowershell
    Get-AzADUser
    ```

1. Utilize uma ferramenta GUID para gerar um identificador exclusivo que será utilizado para a atribuição de função. O identificador tem o formato: `11111111-1111-1111-1111-111111111111`

1. Crie um grupo de recursos de exemplo.

    ```azurepowershell
    New-AzResourceGroup -Name ExampleGroup -Location "Central US"
    ```

1. Utilize o [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) comando para iniciar a implementação.

    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json
    ```

    É-lhe perguntado para especificar os parâmetros necessários. O código a seguir mostra um exemplo da saída.

    ```Output
    PS /home/user> New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json
    
    cmdlet New-AzResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    principalId: 22222222-2222-2222-2222-222222222222
    builtInRoleType: Reader
    roleNameGuid: 11111111-1111-1111-1111-111111111111
    
    DeploymentName          : rbac-rg
    ResourceGroupName       : ExampleGroup
    ProvisioningState       : Succeeded
    Timestamp               : 7/17/2018 7:46:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              principalId      String                     22222222-2222-2222-2222-222222222222
                              builtInRoleType  String                     Reader
                              roleNameGuid     String                     11111111-1111-1111-1111-111111111111
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="deploy-template-using-the-azure-cli"></a>Implementar modelo com a CLI do Azure

Para implementar o modelo anterior com a CLI do Azure, siga estes passos.

1. Crie um novo ficheiro designado rbac rg.json e copie o modelo anterior.

1. Inicie sessão para o [CLI do Azure](/cli/azure/authenticate-azure-cli).

1. Obter o identificador exclusivo de um utilizador, grupo ou aplicação. Por exemplo, pode utilizar o [lista de utilizadores do ad az](/cli/azure/ad/user#az-ad-user-list) command para listar os utilizadores do Azure AD.

    ```azurecli
    az ad user list
    ```

1. Utilize uma ferramenta GUID para gerar um identificador exclusivo que será utilizado para a atribuição de função. O identificador tem o formato: `11111111-1111-1111-1111-111111111111`

1. Crie um grupo de recursos de exemplo.

    ```azurecli
    az group create --name ExampleGroup --location "Central US"
    ```

1. Utilize o [criar a implementação do grupo az](/cli/azure/group/deployment#az-group-deployment-create) comando para iniciar a implementação.

    ```azurecli
    az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json
    ```

    É-lhe perguntado para especificar os parâmetros necessários. O código a seguir mostra um exemplo da saída.

    ```Output
    C:\Azure\Templates>az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json
    Please provide string value for 'principalId' (? for help): 22222222-2222-2222-2222-222222222222
    Please provide string value for 'builtInRoleType' (? for help):
     [1] Owner
     [2] Contributor
     [3] Reader
    Please enter a choice [1]: 3
    Please provide string value for 'roleNameGuid' (? for help): 11111111-1111-1111-1111-111111111111
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/rbac-rg",
      "name": "rbac-rg",
      "properties": {
        "additionalProperties": {
          "duration": "PT9.5323924S",
          "outputResources": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
              "resourceGroup": "ExampleGroup"
            }
          ],
          "templateHash": "0000000000000000000"
        },
        "correlationId": "33333333-3333-3333-3333-333333333333",
        "debugSetting": null,
        "dependencies": [],
        "mode": "Incremental",
        "outputs": null,
        "parameters": {
          "builtInRoleType": {
            "type": "String",
            "value": "Reader"
          },
          "principalId": {
            "type": "String",
            "value": "22222222-2222-2222-2222-222222222222"
          },
          "roleNameGuid": {
            "type": "String",
            "value": "11111111-1111-1111-1111-111111111111"
          }
        },
        "parametersLink": null,
        "providers": [
          {
            "id": null,
            "namespace": "Microsoft.Authorization",
            "registrationState": null,
            "resourceTypes": [
              {
                "aliases": null,
                "apiVersions": null,
                "locations": [
                  null
                ],
                "properties": null,
                "resourceType": "roleAssignments"
              }
            ]
          }
        ],
        "provisioningState": "Succeeded",
        "template": null,
        "templateLink": null,
        "timestamp": "2018-07-17T19:00:31.830904+00:00"
      },
      "resourceGroup": "ExampleGroup"
    }
    ```
    
## <a name="next-steps"></a>Passos Seguintes

- [Quickstart: Criar e implementar modelos Azure Resource Manager com o portal do Azure](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [Understand the structure and syntax of Azure Resource Manager Templates](../azure-resource-manager/resource-group-authoring-templates.md) (Compreender a estrutura e a sintaxe dos Modelos do Azure Resource Manager)
- [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
