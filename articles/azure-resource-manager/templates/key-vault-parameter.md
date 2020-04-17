---
title: Segredo do cofre chave com modelo
description: Mostra como passar um segredo de um cofre chave como parâmetro durante a implantação.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: d21a7d727091b427fee59e22db6a77a495a4eab7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458271"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Utilize o Cofre chave Azure para passar o valor seguro do parâmetro durante a implantação

Em vez de colocar um valor seguro (como uma palavra-passe) diretamente no seu modelo ou ficheiro de parâmetro, pode recuperar o valor de um [Cofre de Chave Azure](../../key-vault/general/overview.md) durante uma implementação. Recupera-se o valor referindo-se ao cofre chave e ao segredo no seu ficheiro de parâmetros. O valor nunca é exposto porque só se refere a identificação do cofre chave. O cofre chave pode existir numa subscrição diferente do grupo de recursos para o qual estás a implantar.

Este artigo centra-se no cenário de passar um valor sensível como parâmetro de modelo. Não cobre o cenário de definir uma propriedade virtual para a URL de um certificado num Cofre chave. Para um modelo de arranque rápido desse cenário, consulte [Instalar um certificado do Cofre de Chaves Azure numa máquina virtual](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows).

## <a name="deploy-key-vaults-and-secrets"></a>Implementar cofres e segredos chave

Para aceder a um cofre `enabledForTemplateDeployment` chave durante a `true`implantação do modelo, coloque no cofre da chave para .

Se já tem um Cofre chave, certifique-se de que permite a implementação do modelo.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

Para criar um novo Cofre chave e adicionar um segredo, use:

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

Como dono do cofre, tens automaticamente acesso a criar segredos. Se o utilizador que trabalha com segredos não for o proprietário do cofre chave, conceda acesso com:

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

Para mais informações sobre a criação de cofres chave e a adição de segredos, consulte:

- [Definir e recuperar um segredo usando cli](../../key-vault/secrets/quick-create-cli.md)
- [Definir e recuperar um segredo usando powershell](../../key-vault/secrets/quick-create-powershell.md)
- [Definir e recuperar um segredo usando o portal](../../key-vault/secrets/quick-create-portal.md)
- [Definir e recuperar um segredo usando .NET](../../key-vault/secrets/quick-create-net.md)
- [Definir e recuperar um segredo usando Node.js](../../key-vault/secrets/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Conceder acesso aos segredos

O utilizador que implementa o `Microsoft.KeyVault/vaults/deploy/action` modelo deve ter a permissão para o âmbito do grupo de recursos e do cofre chave. As funções [de Proprietário](../../role-based-access-control/built-in-roles.md#owner) e [Colaborador](../../role-based-access-control/built-in-roles.md#contributor) concedem este acesso. Se criou o cofre, é o dono, por isso tem a permissão.

O procedimento seguinte mostra como criar um papel com a permissão mínima, e como atribuir o utilizador

1. Criar um ficheiro JSON de definição de função personalizada:

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
    Substitua "00000000-0000-0000-0000-00000-000000000000000" pelo ID de subscrição.

2. Criar a nova função utilizando o ficheiro JSON:

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

Ao utilizar um Cofre de Chave com o modelo para uma [Aplicação Gerida,](../managed-applications/overview.md)deve conceder acesso ao diretor de serviço do Fornecedor de Recursos de **Aparelhos.** Para mais informações, consulte o [segredo do Cofre de Chaves de Acesso ao implementar aplicações geridas](../managed-applications/key-vault-access.md)pelo Azure .

## <a name="reference-secrets-with-static-id"></a>Segredos de referência com ID estático

Com esta abordagem, você referencia o cofre chave no arquivo do parâmetro, não o modelo. A imagem que se segue mostra como o ficheiro do parâmetro refere o segredo e passa esse valor para o modelo.

![Conjunto de id id de cofre de gestor de recursos](./media/key-vault-parameter/statickeyvault.png)

[Tutorial: Integrar o cofre de chaves azure na implementação](./template-tutorial-use-key-vault.md) do modelo de gestor de recursos utiliza este método.

O modelo seguinte implementa um servidor SQL que inclui uma palavra-passe do administrador. O parâmetro de senha está definido para uma corda segura. Mas o modelo não especifica de onde vem esse valor.

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

Agora, crie um ficheiro de parâmetro para o modelo anterior. No ficheiro do parâmetro, especifique um parâmetro que corresponda ao nome do parâmetro no modelo. Para o valor do parâmetro, referência ao segredo do cofre chave. Refere o segredo passando o identificador de recursos do cofre chave e o nome do segredo:

No seguinte ficheiro de parâmetros, o segredo do cofre chave já deve existir, e você fornece um valor estático para o seu ID de recursos.

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

Se precisar de utilizar uma versão do segredo que `secretVersion` não seja a versão atual, utilize a propriedade.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Desdobrar o modelo e passar no ficheiro do parâmetro:

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

A secção anterior mostrou como passar uma identificação de recurso estático para o segredo do cofre chave do parâmetro. No entanto, em alguns cenários, é necessário referir um segredo chave do cofre que varia com base na implementação atual. Ou, pode querer passar os valores dos parâmetros para o modelo em vez de criar um parâmetro de referência no ficheiro parâmetro. Em qualquer dos casos, você pode gerar dinamicamente o ID de recursos para um segredo de cofre chave usando um modelo ligado.

Não é possível gerar dinamicamente o ID de recurso no ficheiro de parâmetros porque as expressões do modelo não são permitidas no ficheiro de parâmetros.

No modelo dos pais, adicione o modelo aninhado e passe num parâmetro que contém o ID de recursos gerado dinamicamente. A imagem que se segue mostra como um parâmetro no modelo ligado refere o segredo.

![ID dinâmico](./media/key-vault-parameter/dynamickeyvault.png)

O modelo seguinte cria dinamicamente o ID do cofre chave e passa-o como parâmetro.

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
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

- Para obter informações gerais sobre cofres chave, veja [o que é o Cofre chave Azure?](../../key-vault/general/overview.md)
- Para exemplos completos de referência de segredos-chave, consulte [os exemplos do Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
