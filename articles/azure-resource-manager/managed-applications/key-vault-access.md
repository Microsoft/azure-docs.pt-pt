---
title: Use o Cofre chave ao implementar uma aplicação gerida
description: Mostra como usar segredos de acesso no Cofre de Chaves Azure ao implementar aplicações geridas
author: tfitzmac
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 5dc219747b9cc74b6c6aac8ab190ebfbe5ae9b32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81458288"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Acesso chave cofre secreto ao implementar aplicações geridas a Azure

Quando precisa de passar um valor seguro (como uma palavra-passe) como parâmetro durante a implementação, pode recuperar o valor de um [Cofre de Chave Azure](../../key-vault/general/overview.md). Para aceder ao Cofre-Chave ao implementar Aplicações Geridas, tem de conceder acesso ao principal do serviço **de fornecedores de recursos** do aparelho. O serviço Aplicações Geridas utiliza esta identidade para executar operações. Para obter com sucesso um valor de um Cofre chave durante a implantação, o principal de serviço deve ser capaz de aceder ao Cofre de Chaves.

Este artigo descreve como configurar o Cofre de Chaves para trabalhar com Aplicações Geridas.

## <a name="enable-template-deployment"></a>Ativar a implementação do modelo

1. No portal, selecione o seu Cofre de Chaves.

1. Selecione **as políticas de acesso**.   

   ![Selecione políticas de acesso](./media/key-vault-access/select-access-policies.png)

1. Selecione **Clicar para mostrar as políticas de acesso avançadas**.

   ![Mostrar políticas avançadas de acesso](./media/key-vault-access/advanced.png)

1. **Selecione Ative o acesso ao Azure Resource Manager para a implementação do modelo**. Em seguida, **selecione Save**.

   ![Ativar a implementação do modelo](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Adicionar serviço como contribuinte

1. Selecione **Controlo de acesso (IAM)** .

   ![Selecione o controlo de acesso](./media/key-vault-access/access-control.png)

1. Selecione **Adicionar atribuição de função**.

   ![Selecionar Adicionar](./media/key-vault-access/add-access-control.png)

1. Selecione **Colaborador** para o papel. Procure o **Fornecedor de Recursos do Aparelho** e selecione-o entre as opções disponíveis.

   ![Pesquisa rumo ao fornecedor](./media/key-vault-access/search-provider.png)

1. Selecione **Guardar**.

## <a name="reference-key-vault-secret"></a>Segredo do cofre da chave de referência

Para passar um segredo de um Cofre-Chave para um modelo na sua Aplicação Gerida, deve utilizar um [modelo ligado ou aninhado](../templates/linked-templates.md) e fazer referência ao Cofre-Chave nos parâmetros para o modelo ligado ou aninhado. Forneça a identificação do recurso do Cofre-Chave e o nome do segredo.

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

Configuraste o cofre para estar acessível durante a implementação de uma Aplicação Gerida.

* Para obter informações sobre a passagem de um valor de um Cofre de Chaves como parâmetro do modelo, consulte [use Azure Key Vault para passar o valor do parâmetro seguro durante a implementação](../templates/key-vault-parameter.md).
* Para exemplos de aplicações geridos, consulte [projetos de amostra para aplicações geridas pela Azure](sample-projects.md).
* Para saber como criar um ficheiro de definição de IU para uma aplicação gerida, veja [Introdução a CreateUiDefinition](create-uidefinition-overview.md).
