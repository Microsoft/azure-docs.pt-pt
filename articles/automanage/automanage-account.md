---
title: Conta Azure Automanage
description: Saiba como funciona uma Conta De Auto-Produção e como criar uma.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: alsin
ms.openlocfilehash: b79e061ae00c42ed2ec2ac39f5653a868f09a15f
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368742"
---
# <a name="automanage-accounts"></a>Contas de auto-produção

A conta Automanage é a identidade que é utilizada pelo serviço Automanage para realizar as suas operações automatizadas.

Na experiência do portal Azure, quando está a permitir a auto-managem nos seus VMs, existe uma queda avançada na lâmina **de boas práticas enable Azure VM** que lhe permite atribuir ou criar manualmente a Conta de Auto-managem.

A Conta de Gestão Automática receberá **as** funções de Contribuinte e **Dedução de Política de Recursos** para a(s) subscrição(s) que contém a(s) máquina(s) que você está a bordo para a Automanage. Pode utilizar a mesma Conta de Auto-gestão em máquinas através de várias subscrições, o que dará permissãos de **Contribuinte** de Conta de Auto-Gestão e **Contribuidor de Política de Recursos** em todas as subscrições.

Se o seu VM estiver ligado a um espaço de trabalho log Analytics noutra subscrição, a Conta De Gestão Automática também receberá **contribuidor** como **colaborador de política de recursos** nessa outra subscrição.

Se estiver a habilitar a autoadministração com uma nova Conta de Autoadministração, necessita das seguintes permissões na sua subscrição: Função **de proprietário** ou **colaborador,** juntamente com as funções **de Administrador de Acesso ao Utilizador.**

Se estiver a ativar a Automanage com uma Conta de Auto-gestão existente, tem de ter a função **de Contribuinte** no grupo de recursos que contém os seus VMs.

> [!NOTE]
> Quando desativar as melhores práticas de auto-managem, as permissões da Conta De Auto-Manage em quaisquer subscrições associadas permanecerão. Remova manualmente as permissões indo para a página IAM da subscrição ou eliminando a Conta Desmanvação Automática. A Conta de Gestão Automática não pode ser eliminada se ainda estiver a gerir máquinas.

## <a name="create-an-automanage-account"></a>Criar uma conta de auto-nomeagem
Pode criar uma Conta de Auto-Produção utilizando o portal ou utilizando um modelo ARM.

### <a name="portal"></a>Portal
1. Navegue até à lâmina **de auto-managem** no portal
1. Clique **em Ativar na máquina existente**
1. Em **Avançado**, clique em "Criar uma nova conta"
1. Preencha os campos necessários e clique em **Criar**

### <a name="arm-template"></a>Modelo ARM
A criação de uma Conta de Auto-produção utilizando um modelo ARM requer 2 passos:
1. Criar a Conta De Auto-Produção
1. Conceda permissões suficientes à conta para permitir a realização de operações para si
    1. Necessitará do ID do Objeto da conta que criou para este passo.
        1. Estão disponíveis [aqui](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-view-managed-identity-service-principal-portal#view-the-service-principal)passos para encontrar detalhes do principal serviço da sua conta (incluindo o ID do Objeto).
    1. Assim que encontrar o seu principal de serviço, copie o **ID do objeto.** Guarde isto como necessitará para delegar permissões abaixo.

#### <a name="1-create-automanage-account-does-not-grant-permissions-to-it"></a>1. Criar Conta de Auto-Produção (não lhe concede permissões)
Para criar uma Conta de Auto-produção, guarde o seguinte modelo ARM como `azuredeploy.json` e execute o comando Azure CLI abaixo. Uma vez feito, passe para o segundo modelo abaixo para delegar permissões suficientes na conta.

```azurecli-interactive
az deployment group create --resource-group <resource group name> --template-file azuredeploy.json
```

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "automanageAccountName": {
            "type": "String"
        },
        "location": {
            "type": "String"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "type": "Microsoft.Automanage/accounts",
            "name": "[parameters('automanageAccountName')]",
            "location": "[parameters('location')]",
            "identity": {
                "type": "SystemAssigned"
            }
        }
    ]
}
```
#### <a name="2-grant-permissions-to-the-automanage-account"></a>2. Conceder permissões à Conta De Auto-Produção
Para conceder permissões suficientes à Conta de Auto-Produção, terá de fazer o seguinte:
1. Guarde o seguinte modelo ARM como `azuredeploy2.json` e executar o comando Azure CLI abaixo.
1. Quando solicitado, introduza o ID do Objeto da Conta de Auto-Produção que criou e guardou para baixo.

```azurecli-interactive
az deployment group create --resource-group <resource group name> --template-file azuredeploy.json
```
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
        "contributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
        "resourcePolicyContributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/36243c78-bf99-498c-9df9-86d9f8d28608"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(variables('contributorRoleDefinitionID'))]",
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(variables('resourcePolicyContributorRoleDefinitionID'))]",
            "properties": {
                "roleDefinitionId": "[variables('resourcePolicyContributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre serviços de auto-produção para [Linux](./automanage-linux.md) e [Windows](./automanage-windows-server.md)