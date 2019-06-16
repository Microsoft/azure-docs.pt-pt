---
title: Segredo do Key Vault com o modelo Azure Resource Manager | Documentos da Microsoft
description: Mostra como transmitir um segredo a partir de um cofre de chaves como um parâmetro durante a implementação.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: tomfitz
ms.openlocfilehash: e47a087e27b6a8ade947e36ded762ce2e518ca25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65507998"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Utilizar o Azure Key Vault para transmitir o valor do parâmetro segura durante a implementação

Em vez de colocar um valor seguro (como uma palavra-passe) diretamente no ficheiro de modelo ou do parâmetro, pode recuperar o valor de uma [do Azure Key Vault](../key-vault/key-vault-whatis.md) durante uma implantação. Obter o valor referenciando o Cofre de chaves e o segredo no seu ficheiro de parâmetros. O valor nunca está exposto porque só fazem referência a sua ID de Cofre de chaves. O Cofre de chaves pode existir numa subscrição diferente que o grupo de recursos que está a implementar.

## <a name="deploy-key-vaults-and-secrets"></a>Implementar a cofres de chaves e segredos

Para aceder a um cofre de chaves durante a implementação de modelo, defina `enabledForTemplateDeployment` no Cofre de chaves para `true`.

Os exemplos de CLI do Azure e o Azure PowerShell seguintes mostram como criar o Cofre de chaves e adicionar um segredo.

```azurecli
az group create --name $resourceGroupName --location $location
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $keyVaultName --name "ExamplePassword" --value "hVFkk965BuUv"
```

```azurepowershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName $keyVaultName -Name 'ExamplePassword' -SecretValue $secretvalue
```

Como o proprietário do Cofre de chaves, tem automaticamente acesso a segredos a criar. Se o utilizador a trabalhar com segredos não for o proprietário do Cofre de chaves, conceda acesso com:

```azurecli
az keyvault set-policy \
  --upn $userPrincipalName \
  --name $keyVaultName \
  --secret-permissions set delete get list
```

```azurepowershell
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName $keyVaultName `
  -UserPrincipalName $userPrincipalName `
  -PermissionsToSecrets set,delete,get,list
```

Para obter mais informações sobre como criar cofres de chaves e segredos de adição, consulte:

- [Definir e obter um segredo ao utilizar a CLI](../key-vault/quick-create-cli.md)
- [Definir e obter um segredo com o Powershell](../key-vault/quick-create-powershell.md)
- [Definir e obter um segredo com o portal](../key-vault/quick-create-portal.md)
- [Definir e obter um segredo com o .NET](../key-vault/quick-create-net.md)
- [Definir e obter um segredo ao utilizar o node. js](../key-vault/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Conceder acesso para os segredos

O utilizador que implanta o modelo tem de ter o `Microsoft.KeyVault/vaults/deploy/action` permissão para o âmbito do grupo de recursos e Cofre de chaves. O [proprietário](../role-based-access-control/built-in-roles.md#owner) e [contribuinte](../role-based-access-control/built-in-roles.md#contributor) ambas as funções de concedem o acesso. Se tiver criado o Cofre de chaves, é o proprietário para que tenha a permissão.

O procedimento seguinte mostra como criar uma função com a permissão mínima e como atribuir o utilizador

1. Crie um ficheiro JSON da definição de função personalizada:

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    Substitua "00000000-0000-0000-0000-000000000000" com o ID de subscrição do utilizador que precisa para implementar os modelos.

2. Crie a nova função com o ficheiro JSON:

    ```azurecli
    az role definition create --role-definition "<PathToRoleFile>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee $userPrincipalName \
      --resource-group $resourceGroupName
    ```

    ```azurepowershell
    New-AzRoleDefinition -InputFile "<PathToRoleFile>" 
    New-AzRoleAssignment `
      -ResourceGroupName $resourceGroupName `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName $userPrincipalName
    ```

    Os exemplos de atribuir a função personalizada para o usuário no nível do grupo de recursos.  

Ao utilizar um cofre de chaves com o modelo para um [Managed Application](../managed-applications/overview.md), tem de conceder acesso para o **fornecedor de recursos da aplicação** principal de serviço. Para obter mais informações, consulte [segredo do Cofre de chaves de acesso ao implementar aplicações geridas do Azure](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Segredos de referência com o ID estático

Com esta abordagem, referenciar o Cofre de chaves no ficheiro de parâmetros, não o modelo. A imagem seguinte mostra como o ficheiro de parâmetros referencia o segredo e passará esse valor para o modelo.

![Diagrama de ID estático de integração de Cofre de chaves do Resource Manager](./media/resource-manager-keyvault-parameter/statickeyvault.png)

[Tutorial: Integrar o Azure Key Vault na implementação de modelo do Resource Manager](./resource-manager-tutorial-use-key-vault.md) usa esse método.

O seguinte modelo implementa um servidor SQL que inclua uma palavra-passe de administrador. O parâmetro de palavra-passe é definido como uma cadeia segura. No entanto, o modelo não especifica de onde vêm esse valor.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

Agora, crie um ficheiro de parâmetros para o modelo anterior. No ficheiro de parâmetros, especifique um parâmetro que corresponda ao nome do parâmetro no modelo. Para o valor do parâmetro, referenciar o segredo do key vault. Referenciar o segredo, passando o identificador de recurso do Cofre de chaves e o nome do segredo do:

No seguinte ficheiro de parâmetros, o segredo do Cofre de chaves tem de existir e fornecer um valor estático para o seu ID de recurso.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminLogin": {
            "value": "exampleadmin"
        },
        "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "ExamplePassword"
            }
        },
        "sqlServerName": {
            "value": "<your-server-name>"
        }
    }
}
```

Se precisar de utilizar uma versão do segredo do que não seja a versão atual, utilize o `secretVersion` propriedade.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Implementar o modelo e passar o ficheiro de parâmetros:

Para a CLI do Azure, utilize:

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri <The Template File URI> \
    --parameters <The Parameter File>
```

Para o PowerShell, utilize:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <The Template File URI> `
  -TemplateParameterFile <The Parameter File>
```

## <a name="reference-secrets-with-dynamic-id"></a>Segredos de referência com o ID de dinâmica

A secção anterior mostrou como passar uma ID de recurso estático para o segredo do Cofre de chaves do parâmetro. No entanto, em alguns cenários, precisa fazer referência um segredo do Cofre de chaves que varia com base na implementação atual. Em alternativa, pode querer passar valores de parâmetros para o modelo, em vez de criar um parâmetro de referência no ficheiro de parâmetros. Em ambos os casos, pode gerar dinamicamente o ID de recurso para um segredo do Cofre de chaves utilizando um modelo ligado.

Dinamicamente não é possível gerar o ID de recurso no ficheiro de parâmetros, uma vez que não são permitidas expressões de modelo no ficheiro de parâmetros. 

No modelo principal, adicione o modelo ligado e passar um parâmetro que contém o ID de recurso gerado dinamicamente. A imagem seguinte mostra como um parâmetro no modelo ligado referencia o segredo.

![ID dinâmico](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

O [seguindo o modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-key-vault-use-dynamic-id) dinamicamente cria o ID de Cofre de chaves e passa-o como um parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "The location where the resources will be deployed."
            }
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "The name of the keyvault that contains the secret."
            }
        },
        "secretName": {
            "type": "string",
            "metadata": {
                "description": "The name of the secret."
            }
        },
        "vaultResourceGroupName": {
            "type": "string",
            "metadata": {
                "description": "The name of the resource group that contains the keyvault."
            }
        },
        "vaultSubscription": {
            "type": "string",
            "defaultValue": "[subscription().subscriptionId]",
            "metadata": {
                "description": "The name of the subscription that contains the keyvault."
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located. When the template is deployed using the accompanying scripts, a private location in the subscription will be used and this value will be automatically generated."
            },
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/"
        },
        "_artifactsLocationSasToken": {
            "type": "securestring",
            "metadata": {
                "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated."
            },
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2018-05-01",
            "name": "dynamicSecret",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "contentVersion": "1.0.0.0",
                    "uri": "[uri(parameters('_artifactsLocation'), concat('./nested/sqlserver.json', parameters('_artifactsLocationSasToken')))]"
                },
                "parameters": {
                    "location": {
                        "value": "[parameters('location')]"
                    },
                    "adminLogin": {
                        "value": "ghuser"
                    },
                    "adminPassword": {
                        "reference": {
                            "keyVault": {
                                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                            },
                            "secretName": "[parameters('secretName')]"
                        }
                    }
                }
            }
        }
    ],
    "outputs": {
        "sqlFQDN": {
            "type": "string",
            "value": "[reference('dynamicSecret').outputs.sqlFQDN.value]"
        }
    }
}
```

Implementar o modelo anterior e forneça valores para os parâmetros. Pode utilizar o modelo de exemplo do GitHub, mas tem de fornecer valores de parâmetro para o seu ambiente.

Para a CLI do Azure, utilize:

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=$keyVaultName vaultResourceGroupName=examplegroup secretName=examplesecret
```

Para o PowerShell, utilize:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName $keyVaultName -vaultResourceGroupName $keyVaultResourceGroupName -secretName $secretName
```

## <a name="next-steps"></a>Passos Seguintes

- Para obter informações gerais sobre cofres de chaves, consulte [o que é o Azure Key Vault?](../key-vault/key-vault-overview.md).
- Para obter exemplos completos de segredos da chave de referência, consulte [exemplos de Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
