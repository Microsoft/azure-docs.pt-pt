---
title: Reparar uma conta de auto-nomeação Azure quebrada
description: Saiba como corrigir uma conta de auto-condutor quebrada
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.openlocfilehash: ad54b37da8a4945162b507232f33083890ec1fff
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557865"
---
# <a name="repair-a-broken-automanage-account"></a>Reparar uma conta de auto-nomeagem quebrada
A [Conta De Auto-Produção](./automanage-virtual-machines.md#automanage-account) é o contexto de segurança ou a identidade em que ocorrem as operações automatizadas. Se mudou recentemente uma subscrição contendo uma Conta De Automanage para um novo inquilino, terá de reconfigurar a sua Conta de Autoadministração. Para reconfigurar a sua Conta de Autoadministração, terá de redefinir o tipo de identidade e atribuir as funções adequadas para a conta.

## <a name="step-1-reset-automanage-account-identity-type"></a>Passo 1: Redefinir o tipo de identidade da Conta Auto-piloto
Repôs o tipo de identidade de conta de gestão automática com o modelo Azure Resource Manager (ARM) abaixo. Guarde o ficheiro localmente como `armdeploy.json` ou similar. Note o nome e a localização da sua conta de auto-nome, uma vez que são necessários parâmetros no modelo ARM.

1. Crie uma nova implementação ARM com o modelo abaixo, e use `identityType = None`
    * Pode fazê-lo com o Azure CLI a usar `az deployment sub create` . Saiba mais sobre o `az deployment sub` comando [aqui.](https://docs.microsoft.com/cli/azure/deployment/sub)
    * Pode também fazê-lo com o PowerShell utilizando o `New-AzDeployment` módulo. Saiba mais sobre o `New AzDeployment` módulo [aqui.](https://docs.microsoft.com/powershell/module/az.resources/new-azdeployment)

1. Executar o mesmo modelo ARM novamente com `identityType = SystemAssigned`

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
A Conta De Gestão Automática requer as funções de Contribuinte e Contribuinte de Política de Recursos na subscrição que contém os VMs que a Automanage está a gerir. Pode atribuir estas funções utilizando o portal Azure, os modelos ARM ou Azure CLI.

Se estiver a utilizar um modelo ARM ou Azure CLI, necessitará do ID principal (também conhecido como ID do Objeto) da sua Conta de Auto-Produção (isto não é necessário se estiver a utilizar o portal Azure). Pode encontrar o ID principal (ID do objeto) da sua conta de auto-nomeagem utilizando os seguintes métodos:

- [Azure CLI](https://docs.microsoft.com/cli/azure/ad/sp): Utilize o comando `az ad sp list --display-name <name of your Automanage Account>` .

- Portal Azure: Navegue para **O Diretório Ativo Azure** e procure a sua Conta de Gestão Automática pelo nome. No âmbito **das Aplicações empresariais,** selecione o nome da Conta De Gestão Automática quando apresentar.

### <a name="azure-portal"></a>Portal do Azure
1. Em **Subscrições,** navegue para a subscrição que contém os seus VMs autogeridos.
1. Navegar para **o controlo de acesso (IAM)**.
1. Clique **em Adicionar atribuições de funções**.
1. Selecione a função **Contribuinte** e digite o nome da sua conta Automanage.
1. Prima **Guardar**.
1. Repita os passos 3-5, desta vez com o papel **de Contribuinte de Política de Recursos.**

### <a name="arm-template"></a>Modelo ARM
Executar o seguinte modelo ARM. Você precisará da identificação principal da sua Conta de Auto-produção - os passos para obter o ID principal estão acima. Insira-o quando solicitado.

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
Execute os seguintes comandos:

```azurecli
az role assignment create --assignee-object-id <your Automanage Account's object id> --role "Contributor" --scope /subscriptions/<your subscription id>

az role assignment create --assignee-object-id <your Automanage Account's object id> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription id>
```

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a Azure Automanage [aqui.](./automanage-virtual-machines.md)
