---
title: Segredo do cofre chave com modelo
description: Mostra como passar um segredo de um cofre chave como parâmetro durante a implantação.
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 8983d51d90e07e21ac4c9489c7ed553530278d3c
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672710"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Use o cofre da chave Azure para passar o valor do parâmetro seguro durante a implementação

Em vez de colocar um valor seguro (como uma palavra-passe) diretamente no seu modelo ou ficheiro de parâmetros, pode recuperar o valor de um [Cofre de Chave Azure](../../key-vault/general/overview.md) durante uma implementação. Recupera o valor referindo-se ao cofre de chaves e ao segredo no seu ficheiro de parâmetros. O valor nunca é exposto porque só se refere a identificação do cofre. O cofre-chave pode existir numa subscrição diferente do grupo de recursos para o qual está a implantar.

Este artigo centra-se no cenário de passar um valor sensível como parâmetro de modelo. Não cobre o cenário de definir uma propriedade de máquina virtual para o URL de um certificado em um Cofre chave. Para obter um modelo de arranque rápido desse cenário, consulte [instalar um certificado do Cofre de Chaves Azure numa Máquina Virtual](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows).

## <a name="deploy-key-vaults-and-secrets"></a>Implementar cofres e segredos chave

Para aceder a um cofre de chave durante a colocação do modelo, coloque `enabledForTemplateDeployment` no cofre da chave para `true` .

Se já tiver um Cofre de Chaves, certifique-se de que permite a implementação do modelo.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

Para criar um novo Cofre-Chave e adicionar um segredo, use:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name ExampleGroup --location centralus
az keyvault create \
  --name ExampleVault \
  --resource-group ExampleGroup \
  --location centralus \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name ExampleVault --name "ExamplePassword" --value "hVFkk965BuUv"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name ExampleGroup -Location centralus
New-AzKeyVault `
  -VaultName ExampleVault `
  -resourceGroupName ExampleGroup `
  -Location centralus `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName ExampleVault -Name 'ExamplePassword' -SecretValue $secretvalue
```

---

Como dono do cofre, tens acesso automaticamente à criação de segredos. Se o utilizador que trabalha com segredos não é o proprietário do cofre chave, conceda acesso com:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az keyvault set-policy \
  --upn <user-principal-name> \
  --name ExampleVault \
  --secret-permissions set delete get list
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName ExampleVault `
  -UserPrincipalName <user-principal-name> `
  -PermissionsToSecrets set,delete,get,list
```

---

Para obter mais informações sobre a criação de cofres-chave e a adição de segredos, consulte:

- [Desabraça e recupere um segredo usando o CLI](../../key-vault/secrets/quick-create-cli.md)
- [Desema estalem e recupere um segredo usando o PowerShell](../../key-vault/secrets/quick-create-powershell.md)
- [Desema estalem e recupere um segredo usando o portal](../../key-vault/secrets/quick-create-portal.md)
- [Desema bem e recupere um segredo utilizando .NET](../../key-vault/secrets/quick-create-net.md)
- [Desemalte e recupere um segredo usando Node.js](../../key-vault/secrets/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Conceder acesso aos segredos

O utilizador que implementar o modelo deve ter a `Microsoft.KeyVault/vaults/deploy/action` permissão para o âmbito do grupo de recursos e do cofre de chaves. As funções [proprietário](../../role-based-access-control/built-in-roles.md#owner) e [contribuinte](../../role-based-access-control/built-in-roles.md#contributor) concedem este acesso. Se criou o cofre, é o dono, por isso tem a permissão.

O procedimento a seguir mostra como criar uma função com a permissão mínima, e como atribuir o utilizador

1. Crie um ficheiro JSON de definição de função personalizada:

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
    Substitua "00000000-0000-0000-0000-0000000000000000000000000000" pelo ID de assinatura.

2. Crie a nova função utilizando o ficheiro JSON:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

    ```azurecli-interactive
    az role definition create --role-definition "<path-to-role-file>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee <user-principal-name> \
      --resource-group ExampleGroup
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell-interactive
    New-AzRoleDefinition -InputFile "<path-to-role-file>"
    New-AzRoleAssignment `
      -ResourceGroupName ExampleGroup `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName <user-principal-name>
    ```

    ---

    As amostras atribuem a função personalizada ao utilizador ao nível do grupo de recursos.

Ao utilizar um Cofre-chave com o modelo para uma [Aplicação Gerida,](../managed-applications/overview.md)deve conceder acesso ao principal do serviço **de fornecedores de recursos** do aparelho. Para obter mais informações, consulte [o segredo do Access Key Vault ao implementar aplicações geridas aZure](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Segredos de referência com ID estático

Com esta abordagem, você referencia o cofre chave no arquivo de parâmetros, não o modelo. A imagem a seguir mostra como o ficheiro do parâmetro faz referência ao segredo e passa esse valor para o modelo.

![Gestor de recursos chave integração de id estático](./media/key-vault-parameter/statickeyvault.png)

[Tutorial: Integrar o Cofre da Chave Azure na implementação do modelo do gestor de recursos](./template-tutorial-use-key-vault.md) utiliza este método.

O modelo a seguir implementa um servidor SQL que inclui uma palavra-passe do administrador. O parâmetro da palavra-passe está definido para uma cadeia segura. Mas o modelo não especifica de onde vem esse valor.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('sqlServerName')]",
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

Agora, crie um ficheiro de parâmetro para o modelo anterior. No ficheiro de parâmetros, especifique um parâmetro que corresponda ao nome do parâmetro no modelo. Para o valor do parâmetro, refira o segredo do cofre da chave. Você refere o segredo passando o identificador de recursos do cofre chave e o nome do segredo:

No seguinte ficheiro de parâmetros, o segredo do cofre-chave já deve existir, e você fornece um valor estático para o seu ID de recurso.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

Se precisar de usar uma versão do segredo que não seja a versão atual, use a `secretVersion` propriedade.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Implemente o modelo e passe no ficheiro do parâmetro:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name SqlGroup --location westus2
az deployment group create \
  --resource-group SqlGroup \
  --template-uri <template-file-URI> \
  --parameters <parameter-file>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <template-file-URI> `
  -TemplateParameterFile <parameter-file>
```

---

## <a name="reference-secrets-with-dynamic-id"></a>Segredos de referência com ID dinâmico

A secção anterior mostrou como passar uma identificação de recurso estática para o segredo do cofre chave a partir do parâmetro. No entanto, em alguns cenários, é necessário fazer referência a um segredo chave do cofre que varia com base na implementação atual. Ou, pode querer passar os valores dos parâmetros para o modelo em vez de criar um parâmetro de referência no ficheiro do parâmetro. Em qualquer dos casos, pode gerar dinamicamente o ID de recurso para um segredo de cofre chave usando um modelo ligado.

Não é possível gerar dinamicamente o ID de recursos no ficheiro de parâmetros porque as expressões do modelo não são permitidas no ficheiro de parâmetros.

No modelo dos seus pais, adiciona-se o modelo aninhado e passa-se num parâmetro que contém o ID de recursos gerado dinamicamente. A imagem a seguir mostra como um parâmetro no modelo ligado refere o segredo.

![ID dinâmico](./media/key-vault-parameter/dynamickeyvault.png)

O modelo a seguir cria dinamicamente o ID do cofre da chave e passa-o como um parâmetro.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
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
  }
}
```

## <a name="next-steps"></a>Passos seguintes

- Para obter informações gerais sobre cofres chave, veja [o que é Azure Key Vault?](../../key-vault/general/overview.md)
- Para obter exemplos completos de referência de segredos chave, consulte [exemplos do Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- Para um módulo Microsoft Learn que cubra a passagem de um valor seguro a partir de um cofre de chaves, consulte [Gerir implementações complexas de nuvem utilizando funcionalidades avançadas do ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
