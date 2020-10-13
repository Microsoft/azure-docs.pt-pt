---
title: Azure Guide - Crie um cofre de chaves Azure e uma política de acesso ao cofre utilizando o modelo do Gestor de Recursos Azure / Microsoft Docs
description: Mostra como criar cofres chave Azure e políticas de acesso ao cofre usando o modelo do Gestor de Recursos Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/5/2020
ms.author: mbaldwin
ms.openlocfilehash: cf19561005fe2e98b7b5cf6812ff9224fd9474dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91804398"
---
# <a name="how-to-create-azure-key-vault-and-vault-access-policy-using-a-resource-manager-template"></a>Como criar a política de acesso a Azure Key Vault e vault usando um modelo de Gestor de Recursos

[Azure Key Vault](../general/overview.md) é um serviço de nuvem que fornece uma loja segura para segredos, tais como chaves, senhas, certificados e outros segredos. Este guia foca-se no processo de implantação de um modelo de Gestor de Recursos Azure (modelo ARM) para criar um cofre chave.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este artigo:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="create-key-vault-resource-manager-template"></a>Criar modelo de gestor de recursos de cofre de chave

O modelo a seguir mostra uma forma básica de criar o cofre de chaves. Alguns valores são especificados dentro do modelo.

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

Para obter mais informações sobre as definições do modelo do cofre de chaves, consulte [a referência do modelo key Vault ARM](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults).

> [!IMPORTANT]
> Se o modelo for redistribuído, irá sobrepor-se a quaisquer políticas de acesso existentes no cofre de chaves. Recomenda-se povoar a `accessPolicies` propriedade com as políticas de acesso existentes para evitar perder o acesso ao cofre chave. 

## <a name="add-access-policy-to-key-vault-resource-manager-template"></a>Adicione a política de acesso ao modelo do Gestor de Recursos do Cofre chave

Pode implementar políticas de acesso ao cofre de chaves existente sem redistribuir todo o modelo de cofre de chaves. O modelo a seguir mostra uma forma básica de criar políticas de acesso.

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
Para obter mais informações sobre as definições do modelo do cofre de chaves, consulte [a referência do modelo key Vault ARM](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/accesspolicies).

## <a name="other-available-key-vault-resource-manager-templates"></a>Outros modelos disponíveis do Gestor de Recursos do Cofre de Chaves

Existem outros modelos de Gestor de Recursos disponíveis para objetos do Cofre chave:

| Segredos | Chaves | Certificados |
|--|--|--|
|[Início rápido](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-template)<br>[Referência](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/secrets)|N/D|N/D|

Mais modelos de cofre de chaves que pode encontrar aqui: [Referência do Gestor de Recursos do Cofre de Chaves](https://docs.microsoft.com/azure/templates/microsoft.keyvault/allversions)

## <a name="deploy-the-templates"></a>Implementar os modelos

Pode utilizar o portal Azure para implementar modelos acima, utilizando a opção "Construa o seu próprio modelo no editor" no guia abaixo: [Implementar recursos a partir do modelo personalizado](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template)

Também pode guardar modelos acima para ficheiros e usar os seguintes comandos:  [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [az deployment group create](/cli/azure/group/deployment#az-group-deployment-create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se planeia continuar e trabalhar em quickstarts e tutoriais subsequentes, pode deixar estes recursos no lugar. Quando os recursos já não forem necessários, elimine o grupo de recursos, que elimina o cofre-chave e os recursos conexos. Para eliminar o grupo de recursos utilizando o Azure CLI ou o Azure PowerShell, utilize os seguintes passos.

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

- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Saiba mais sobre o [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- Rever [as melhores práticas do Azure Key Vault](../general/best-practices.md)

## <a name="next-steps"></a>Passos seguintes

- [Acesso seguro a um cofre de chaves](secure-your-key-vault.md)
- [Autenticar para um cofre de chaves](authentication.md)
- [Guia do Azure Key Vault Developer](developers-guide.md)
