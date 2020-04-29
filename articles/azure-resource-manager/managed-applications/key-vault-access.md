---
title: Use o Cofre chave ao implementar a aplicação gerida
description: Mostra como usar segredos de acesso no Cofre chave Azure ao implementar aplicações geridas
author: tfitzmac
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 5dc219747b9cc74b6c6aac8ab190ebfbe5ae9b32
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81458288"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Aceder ao segredo do cofre chave ao implementar aplicações geridas pelo Azure

Quando precisa passar um valor seguro (como uma palavra-passe) como parâmetro durante a implantação, pode recuperar o valor de um [Cofre de Chave Azure](../../key-vault/general/overview.md). Para aceder ao Cofre chave ao implementar aplicações geridas, deve conceder acesso ao principal serviço do Fornecedor de Recursos de **Eletrodomésticos.** O serviço De Aplicações Geridas utiliza esta identidade para executar operações. Para obter com sucesso um valor de um Cofre chave durante a implantação, o diretor de serviço deve ser capaz de aceder ao Cofre chave.

Este artigo descreve como configurar o Cofre chave para trabalhar com aplicações geridas.

## <a name="enable-template-deployment"></a>Ativar a implementação do modelo

1. No portal, selecione o seu Cofre chave.

1. Selecione **Políticas de acesso**.   

   ![Selecione políticas de acesso](./media/key-vault-access/select-access-policies.png)

1. Selecione **Clicar para mostrar as políticas de acesso avançadas**.

   ![Mostrar políticas avançadas de acesso](./media/key-vault-access/advanced.png)

1. Selecione **Ativar o acesso ao Gestor de Recursos Azure para a implementação do modelo**. Em seguida, selecione **Guardar**.

   ![Ativar a implementação do modelo](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Adicionar serviço como contribuinte

1. Selecione **o controlo de acesso (IAM)**.

   ![Selecione o controlo de acesso](./media/key-vault-access/access-control.png)

1. Selecione **Adicionar atribuição de função**.

   ![Selecionar Adicionar](./media/key-vault-access/add-access-control.png)

1. Selecione **Colaborador** para o papel. Procure o Fornecedor de **Recursos de Aparelhos** e selecione-o a partir das opções disponíveis.

   ![Pesquisa por fornecedor](./media/key-vault-access/search-provider.png)

1. Selecione **Guardar**.

## <a name="reference-key-vault-secret"></a>Segredo do cofre de chaves de referência

Para passar um segredo de um Cofre chave para um modelo na sua Aplicação Gerida, deve utilizar um [modelo ligado ou aninhado](../templates/linked-templates.md) e fazer referência ao Cofre chave nos parâmetros para o modelo ligado ou aninhado. Forneça a identificação do recurso do Cofre chave e o nome do segredo.

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

Configurou o seu Cofre chave para estar acessível durante a implementação de uma Aplicação Gerida.

* Para obter informações sobre a passagem de um valor de um cofre chave como parâmetro de modelo, consulte [Use Azure Key Vault para passar o valor do parâmetro seguro durante](../templates/key-vault-parameter.md)a implementação .
* Para exemplos de aplicações geridas, consulte [projetos de amostra para aplicações geridas pelo Azure.](sample-projects.md)
* Para saber como criar um ficheiro de definição de IU para uma aplicação gerida, veja [Introdução a CreateUiDefinition](create-uidefinition-overview.md).
