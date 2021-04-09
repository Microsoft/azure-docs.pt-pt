---
title: Reparar uma conta de auto-nomeação Azure quebrada
description: Se mudou recentemente uma subscrição que contém uma Conta de Autoadministração para um novo inquilino, precisa de a reconfigurar. Neste artigo, vais aprender como.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.openlocfilehash: 4694fa679c7bbff309a0452219ff39bacf2488c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96183707"
---
# <a name="repair-an-automanage-account"></a>Reparar uma Conta De Auto-Produção
A sua [Conta Azure Automanage](./automanage-virtual-machines.md#automanage-account) é o contexto de segurança ou identidade em que ocorrem as operações automatizadas. Se mudou recentemente uma subscrição que contém uma Conta De Autoadministração para um novo inquilino, precisa de reconfigurar a conta. Para reconfigurá-lo, é necessário redefinir o tipo de identidade e atribuir as funções adequadas para a conta.

## <a name="step-1-reset-the-automanage-account-identity-type"></a>Passo 1: Redefinir o tipo de identidade de Conta de Auto-produção
Reinicie o tipo de identidade de conta de gestão automática utilizando o seguinte modelo Azure Resource Manager (ARM). Guarde o ficheiro localmente como armdeploy.jsou um nome semelhante. Note o nome e localização da sua Conta de Auto-produção porque são necessários parâmetros no modelo ARM.

1. Crie uma implementação de Gestor de Recursos utilizando o seguinte modelo. Utilize `identityType = None`.
    * Pode criar a implantação no CLI Azure utilizando `az deployment sub create` . Para obter mais informações, consulte o sub de [implantação az](/cli/azure/deployment/sub).
    * Pode criar a implementação no PowerShell utilizando o `New-AzDeployment` módulo. Para mais informações, consulte [New-AzDeployment](/powershell/module/az.resources/new-azdeployment).

1. Volte a executar o mesmo modelo ARM com `identityType = SystemAssigned` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "identityType": {
            "type": "string",
            "allowedValues": [ "None", "SystemAssigned" ]
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Automanage/accounts",
            "identity": {
                "type": "[parameters('identityType')]"
            }
        }
    ]
}

```

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>Passo 2: Atribuir funções adequadas para a Conta De Autoadministração
A Conta De Gestão Automática requer as funções de Contribuinte e Contribuinte de Política de Recursos na subscrição que contém os VMs que a Automanage está a gerir. Pode atribuir estas funções utilizando o portal Azure, os modelos ARM ou o Azure CLI.

Se estiver a utilizar um modelo ARM ou o Azure CLI, necessitará do ID principal (também conhecido como ID do Objeto) da sua Conta de Auto-Produção. (Não precisa da identificação se estiver a usar o portal Azure.) Pode encontrar este ID utilizando estes métodos:

- [Azure CLI](/cli/azure/ad/sp): Utilize o comando `az ad sp list --display-name <name of your Automanage Account>` .

- Portal Azure: Vá ao **Azure Ative Directory** e procure a sua Conta de Gestão Automática pelo nome. No âmbito **das Aplicações empresariais,** selecione o nome da Conta De Gestão Automática quando aparecer.

### <a name="azure-portal"></a>Portal do Azure
1. Em **Subscrições,** vá para a subscrição que contém os seus VMs geridos automaticamente.
1. Ir ao **controlo de acesso (IAM)**.
1. Selecione **Adicionar atribuições de funções**.
1. Selecione a função **Contribuinte** e insira o nome da sua Conta de Auto-Produção.
1. Selecione **Guardar**.
1. Repita os passos 3 a 5, desta vez com o papel **de Contribuinte de Política de Recursos.**

### <a name="arm-template"></a>Modelo ARM
Executar o seguinte modelo ARM. Vai precisar da identificação principal da sua Conta de Auto-Produção. Os passos para o conseguir estão no início desta secção. Introduza a identificação quando for solicitado.

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
        }
    },
    "variables": {
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Resource Policy Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '36243c78-bf99-498c-9df9-86d9f8d28608')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Resource Policy Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Resource Policy Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

### <a name="azure-cli"></a>CLI do Azure
Execute estes comandos:

```azurecli
az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Contributor" --scope /subscriptions/<your subscription ID>

az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription ID>
```

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre a Azure Automanage](./automanage-virtual-machines.md)