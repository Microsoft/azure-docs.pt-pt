---
title: Crie um cofre de chaves Azure e uma política de acesso ao cofre usando o modelo ARM
description: Este artigo mostra como criar cofres chave Azure e políticas de acesso ao cofre usando um modelo de Gestor de Recursos Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/5/2020
ms.author: mbaldwin
ms.openlocfilehash: 1ab382ec10d932b94961c73b7d7ac9082125a88c
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704525"
---
# <a name="how-to-create-an-azure-key-vault-and-vault-access-policy-by-using-a-resource-manager-template"></a>Como criar uma política de acesso a cofre e cofre Azure utilizando um modelo de Gestor de Recursos

[Azure Key Vault](../general/overview.md) é um serviço de nuvem que fornece uma loja segura para segredos como chaves, senhas e certificados. Este artigo descreve o processo de implantação de um modelo de Gestor de Recursos Azure (modelo ARM) para criar um cofre chave.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos deste artigo:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="create-a-key-vault-resource-manager-template"></a>Crie um modelo de gestor de recursos de cofre de chave

O modelo a seguir mostra uma forma básica de criar um cofre chave. Alguns valores são especificados no modelo.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    }
   },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "false",
        "enabledForDiskEncryption": "false",
        "enabledForTemplateDeployment": "false",
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    }
  ]
}

```

Para obter mais informações sobre as definições do modelo do cofre de chaves, consulte [a referência do modelo key Vault ARM](/azure/templates/microsoft.keyvault/vaults).

> [!IMPORTANT]
> Se um modelo for redistribuído, quaisquer políticas de acesso existentes no cofre chave serão ultrapassadas. Recomendamos que preencha a `accessPolicies` propriedade com as políticas de acesso existentes para evitar perder o acesso ao cofre chave. 

## <a name="add-an-access-policy-to-a-key-vault-resource-manager-template"></a>Adicione uma política de acesso a um modelo de Gestor de Recursos de Cofre de Chave

Pode implementar políticas de acesso a um cofre de chaves existente sem recolocar todo o modelo do cofre de chaves. O modelo a seguir mostra uma forma básica de criar políticas de acesso:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "certificatePermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to certificates in the vault. Valid values are: all,  create, delete, update, deleteissuers, get, getissuers, import, list, listissuers, managecontacts, manageissuers,  recover, backup, restore, setissuers, and purge."
      }
    },
  "resources": [
     {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]",
              "certificates": [parameters('certificatesPermissions')]
            }
          }
        ]
      }
    }
  ]
}

```
Para obter mais informações sobre as definições do modelo do Cofre de Chaves, consulte a [referência do modelo key Vault ARM](/azure/templates/microsoft.keyvault/vaults/accesspolicies).

## <a name="more-key-vault-resource-manager-templates"></a>Mais modelos de gestor de recursos de cofre de cofre

Existem outros modelos de Gestor de Recursos disponíveis para objetos do Cofre chave:

| Segredos | Chaves | Certificados |
|--|--|--|
|<ul><li>[Início rápido](../secrets/quick-create-template.md)<li>[Referência](/azure/templates/microsoft.keyvault/vaults/secrets)|N/D|N/D|

Pode encontrar mais modelos de Cofre chave aqui: [referência do Gestor de Recursos do Cofre de Chaves](/azure/templates/microsoft.keyvault/allversions).

## <a name="deploy-the-templates"></a>Implementar os modelos

Pode utilizar o portal Azure para implementar os modelos anteriores utilizando o seu próprio modelo na opção **de editor,** conforme descrito aqui: [Implementar recursos a partir de um modelo personalizado](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

Também pode guardar os modelos anteriores para ficheiros e utilizar estes comandos:  [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [grupo de implementação az criar](/cli/azure/deployment/group#az_deployment_group_create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se planeia continuar com os rápidos e tutoriais subsequentes, pode deixar estes recursos no lugar. Quando já não precisar dos recursos, elimine o grupo de recursos. Se eliminar o grupo, o cofre-chave e os recursos conexas também são eliminados. Para eliminar o grupo de recursos utilizando o Azure CLI ou o Azure PowerShell, complete estes passos:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="resources"></a>Recursos

- Leia uma [visão geral do Cofre da Chave Azure](../general/overview.md).
- Saiba mais sobre [o Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Reveja [as melhores práticas do Azure Key Vault](../general/best-practices.md).

## <a name="next-steps"></a>Passos seguintes

- [Acesso seguro a um cofre de chaves](secure-your-key-vault.md)
- [Autenticar para um cofre de chaves](authentication.md)
- [Guia do Programador do Azure Key Vault](developers-guide.md)
