---
title: Modelos de ligação para implantação
description: Descreve como usar modelos ligados num modelo de Gestor de Recursos Azure (modelo ARM) para criar uma solução de modelo modular. Mostra como passar valores de parâmetros, especificar um ficheiro de parâmetros e URLs criados dinamicamente.
ms.topic: conceptual
ms.date: 01/20/2021
ms.openlocfilehash: dd810167e07f1bb23f9563936cb481652953ccd1
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624863"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Utilizar modelos ligados e aninhados ao implementar recursos do Azure

Para implementar soluções complexas, pode quebrar o seu modelo de Gestor de Recursos Azure (modelo ARM) em muitos modelos relacionados e, em seguida, implantá-los juntos através de um modelo principal. Os modelos relacionados podem ser ficheiros separados ou sintaxe de modelo que está incorporado no modelo principal. Este artigo usa o **modelo de ligação** do termo para se referir a um ficheiro de modelo separado que é referenciado através de um link do modelo principal. Ele usa o modelo **aninhado** para se referir à sintaxe do modelo incorporado dentro do modelo principal.

Para obter soluções pequenas a médias, um único modelo é mais fácil de compreender e manter. Pode ver todos os recursos e valores num único ficheiro. Para cenários avançados, os modelos associados permitem-lhe dividir a solução em componentes direcionados. Pode facilmente utilizar estes modelos para outros cenários.

Para um tutorial, consulte [Tutorial: Implemente um modelo ligado](./deployment-tutorial-linked-template.md).

> [!NOTE]
> Para modelos ligados ou aninhados, só pode definir o modo de implementação para [Incremental](deployment-modes.md). No entanto, o modelo principal pode ser implantado em modo completo. Se implementar o modelo principal no modo completo e o modelo ligado ou aninhado atingir o mesmo grupo de recursos, os recursos implantados no modelo ligado ou aninhado estão incluídos na avaliação para implementação completa do modo. A recolha combinada de recursos implantados no modelo principal e modelos ligados ou aninhados é comparada com os recursos existentes no grupo de recursos. Quaisquer recursos não incluídos nesta coleção combinada são eliminados.
>
> Se o modelo ligado ou aninhado tiver como alvo um grupo de recursos diferente, essa implementação utiliza o modo incremental.
>

## <a name="nested-template"></a>Modelo aninhado

Para nidificar um modelo, adicione um [recurso de implementações](/azure/templates/microsoft.resources/deployments) ao seu modelo principal. Na `template` propriedade, especifique a sintaxe do modelo.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "mode": "Incremental",
        "template": {
          <nested-template-syntax>
        }
      }
    }
  ],
  "outputs": {
  }
}
```

O exemplo a seguir implanta uma conta de armazenamento através de um modelo aninhado.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-04-01",
              "name": "[parameters('storageAccountName')]",
              "location": "West US",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
  }
}
```

### <a name="expression-evaluation-scope-in-nested-templates"></a>Âmbito de avaliação de expressão em modelos aninhados

Ao utilizar um modelo aninhado, pode especificar se as expressões do modelo são avaliadas no âmbito do modelo principal ou do modelo aninhado. O âmbito determina como os parâmetros, variáveis e funções como [o grupo de recursos](template-functions-resource.md#resourcegroup) e a [subscrição](template-functions-resource.md#subscription) são resolvidos.

Você define o âmbito através da `expressionEvaluationOptions` propriedade. Por padrão, a `expressionEvaluationOptions` propriedade é definida para , o que significa que usa o âmbito do modelo dos `outer` pais. Desa estariremos o valor `inner` para fazer com que as expressões sejam avaliadas no âmbito do modelo aninhado.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2019-10-01",
  "name": "nestedTemplate1",
  "properties": {
  "expressionEvaluationOptions": {
    "scope": "inner"
  },
  ...
```

O modelo a seguir demonstra como as expressões do modelo são resolvidas de acordo com o âmbito. Contém uma variável nomeada `exampleVar` que é definida tanto no modelo dos pais como no modelo aninhado. Devolve o valor da variável.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "exampleVar": "from parent template"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "variables": {
            "exampleVar": "from nested template"
          },
          "resources": [
          ],
          "outputs": {
            "testVar": {
              "type": "string",
              "value": "[variables('exampleVar')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('nestedTemplate1').outputs.testVar.value]"
    }
  }
}
```

O valor das `exampleVar` alterações dependendo do valor do `scope` imóvel em `expressionEvaluationOptions` . A tabela seguinte mostra os resultados de ambos os âmbitos.

| Âmbito de avaliação | Saída |
| ----- | ------ |
| interior | do modelo aninhado |
| exterior (ou padrão) | do modelo dos pais |

O exemplo seguinte implementa um servidor SQL e recupera um segredo de cofre chave para usar para a palavra-passe. O âmbito é definido `inner` porque cria dinamicamente o ID do cofre chave (ver `adminPassword.reference.keyVault` nos modelos externos) `parameters` e passa-o como um parâmetro para o modelo aninhado.

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
      "apiVersion": "2019-10-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
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
        }
      }
    }
  ],
  "outputs": {
  }
}
```

Tenha cuidado ao utilizar valores de parâmetros seguros num modelo aninhado. Se definir o âmbito exterior, os valores de segurança são armazenados como texto simples no histórico de implantação. Um utilizador que visualiza o modelo no histórico de implementação poderia ver os valores de segurança. Em vez disso, use o âmbito interno ou adicione ao modelo principal os recursos que precisam de valores seguros.

O seguinte excerto mostra quais valores são seguros e que não são seguros.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPasswordOrKey": {
      "type": "securestring",
      "metadata": {
        "description": "SSH Key or password for the Virtual Machine. SSH key is recommended."
      }
    }
  },
  ...
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2020-06-01",
      "name": "mainTemplate",
      "properties": {
        ...
        "osProfile": {
          "computerName": "mainTemplate",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPasswordOrKey')]" // Yes, secure because resource is in parent template
        }
      }
    },
    {
      "name": "outer",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "outer"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2020-06-01",
              "name": "outer",
              "properties": {
                ...
                "osProfile": {
                  "computerName": "outer",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPasswordOrKey')]" // No, not secure because resource is in nested template with outer scope
                }
              }
            }
          ]
        }
      }
    },
    {
      "name": "inner",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "parameters": {
          "adminPasswordOrKey": {
              "value": "[parameters('adminPasswordOrKey')]"
          },
          "adminUsername": {
              "value": "[parameters('adminUsername')]"
          }
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": {
              "type": "string",
              "metadata": {
                "description": "Username for the Virtual Machine."
              }
            },
            "adminPasswordOrKey": {
              "type": "securestring",
              "metadata": {
                "description": "SSH Key or password for the Virtual Machine. SSH key is recommended."
              }
            }
          },
          "resources": [
            {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2020-06-01",
              "name": "inner",
              "properties": {
                ...
                "osProfile": {
                  "computerName": "inner",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPasswordOrKey')]" // Yes, secure because resource is in nested template and scope is inner
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

> [!NOTE]
>
> Quando o âmbito está definido `outer` para , não é possível utilizar a `reference` função na secção de saídas de um modelo aninhado para um recurso que implementou no modelo aninhado. Para devolver os valores de um recurso implantado num modelo aninhado, utilize `inner` o âmbito ou converta o seu modelo aninhado num modelo ligado.

## <a name="linked-template"></a>Modelo associado

Para ligar um modelo, adicione um [recurso de implementações](/azure/templates/microsoft.resources/deployments) ao seu modelo principal. Na `templateLink` propriedade, especifique o URI do modelo para incluir. O exemplo a seguir liga-se a um modelo que está numa conta de armazenamento.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

Ao fazer referência a um modelo ligado, o valor de `uri` não pode ser um ficheiro local ou um ficheiro que só está disponível na sua rede local. O Gestor de Recursos Azure deve poder aceder ao modelo. Forneça um valor URI que seja transferível como HTTP ou HTTPS.

Pode fazer referência a modelos utilizando parâmetros que incluam HTTP ou HTTPS. Por exemplo, um padrão comum é usar o `_artifactsLocation` parâmetro. Pode definir o modelo ligado com uma expressão como:

```json
"uri": "[concat(parameters('_artifactsLocation'), '/shared/os-disk-parts-md.json', parameters('_artifactsLocationSasToken'))]"
```

Se estiver a ligar-se a um modelo no GitHub, utilize o URL cru. O link tem o formato: `https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-with-templates/quickstart-template/azuredeploy.json` . Para obter a ligação bruta, selecione **Raw**.

:::image type="content" source="./media/linked-templates/select-raw.png" alt-text="Selecione URL cru":::

### <a name="parameters-for-linked-template"></a>Parâmetros para o modelo ligado

Pode fornecer os parâmetros para o seu modelo ligado, quer num ficheiro externo, quer em linha. Ao fornecer um ficheiro de parâmetro externo, utilize a `parametersLink` propriedade:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2019-10-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

Para passar os valores dos parâmetros em linha, use a `parameters` propriedade.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2019-10-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion": "1.0.0.0"
      },
      "parameters": {
        "storageAccountName": {
          "value": "[parameters('storageAccountName')]"
        }
      }
    }
  }
]
```

Não é possível utilizar os parâmetros inline e um link para um ficheiro de parâmetros. A implementação falha com um erro quando ambos `parametersLink` e `parameters` são especificados.

## <a name="template-specs"></a>Especificações de modelo

Em vez de manter os seus modelos ligados num ponto final acessível, pode criar uma [especificação de modelo](template-specs.md) que embala o modelo principal e os seus modelos ligados numa única entidade que pode implementar. A especificação do modelo é um recurso na sua subscrição Azure. Torna-se fácil partilhar o modelo de forma segura com os utilizadores da sua organização. Você usa o controlo de acesso baseado em funções Azure (Azure RBAC) para conceder acesso à especificação do modelo. Esta funcionalidade encontra-se atualmente em pré-visualização.

Para obter mais informações, consulte:

- [Tutorial: Criar uma especificação de modelo com modelos ligados.](./template-specs-create-linked.md)
- [Tutorial: Implementar uma especificação de modelo como um modelo ligado](./template-specs-deploy-linked-template.md).

## <a name="dependencies"></a>Dependências

Tal como acontece com outros tipos de recursos, pode definir dependências entre os modelos ligados. Se os recursos de um modelo ligado tão devem ser implementados antes dos recursos num segundo modelo ligado, decida o segundo modelo dependente do primeiro.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/linked-dependency.json" highlight="10,22,24":::

## <a name="contentversion"></a>parte de conteúdoVersão

Você não precisa fornecer a `contentVersion` propriedade para o ou `templateLink` `parametersLink` propriedade. Se não fornecer `contentVersion` um, a versão atual do modelo é implementada. Se fornecer um valor para a versão de conteúdo, deve corresponder à versão no modelo ligado; caso contrário, a implementação falha com um erro.

## <a name="using-variables-to-link-templates"></a>Usando variáveis para ligar modelos

Os exemplos anteriores mostravam valores de URL codificados por código rígido para os links do modelo. Esta abordagem pode funcionar para um modelo simples, mas não funciona bem para um grande conjunto de modelos modulares. Em vez disso, pode criar uma variável estática que armazena um URL de base para o modelo principal e, em seguida, criar urls dinâmicos para os modelos ligados a partir desse URL de base. O benefício desta abordagem é que você pode facilmente mover ou bifurcar o modelo porque você precisa alterar apenas a variável estática no modelo principal. O modelo principal passa os URIs corretos ao longo do modelo decomposto.

O exemplo a seguir mostra como usar um URL de base para criar dois URLs para modelos ligados `sharedTemplateUrl` (e `vmTemplateUrl` ).

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[uri(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[uri(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Também pode usar [a implementação para](template-functions-deployment.md#deployment) obter o URL base para o modelo atual, e usá-lo para obter o URL para outros modelos no mesmo local. Esta abordagem é útil se a localização do seu modelo mudar ou se quiser evitar URLs de codificação dura no ficheiro do modelo. A `templateLink` propriedade só é devolvida quando se liga a um modelo remoto com um URL. Se você está usando um modelo local, essa propriedade não está disponível.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

Em última análise, usaria a variável na `uri` propriedade de uma `templateLink` propriedade.

```json
"templateLink": {
 "uri": "[variables('sharedTemplateUrl')]",
 "contentVersion":"1.0.0.0"
}
```

## <a name="using-copy"></a>Usando a cópia

Para criar múltiplas instâncias de um recurso com um modelo aninhado, adicione o `copy` elemento ao nível do `Microsoft.Resources/deployments` recurso. Ou, se o âmbito `inner` for, pode adicionar a cópia dentro do modelo aninhado.

O modelo de exemplo a seguir mostra como usar `copy` com um modelo aninhado.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2019-10-01",
    "name": "[concat('nestedTemplate', copyIndex())]",
    // yes, copy works here
    "copy": {
      "name": "storagecopy",
      "count": 2
    },
    "properties": {
      "mode": "Incremental",
      "expressionEvaluationOptions": {
        "scope": "inner"
      },
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(variables('storageName'), copyIndex())]",
            "location": "West US",
            "sku": {
              "name": "Standard_LRS"
            },
            "kind": "StorageV2"
            // Copy works here when scope is inner
            // But, when scope is default or outer, you get an error
            //"copy":{
            //  "name": "storagecopy",
            //  "count": 2
            //}
          }
        ]
      }
    }
  }
]
```

## <a name="get-values-from-linked-template"></a>Obter valores do modelo ligado

Para obter um valor de saída a partir de um modelo ligado, recupere o valor da propriedade com sintaxe como: `"[reference('deploymentName').outputs.propertyName.value]"` .

Ao obter uma propriedade de saída a partir de um modelo ligado, o nome da propriedade não deve incluir um traço.

Os exemplos a seguir demonstram como fazer referência a um modelo ligado e recuperar um valor de saída. O modelo ligado devolve uma mensagem simples. Primeiro, o modelo ligado:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/helloworld.json":::

O modelo principal implementa o modelo ligado e obtém o valor devolvido. Note que faz referência ao recurso de implantação pelo nome, e utiliza o nome da propriedade devolvida pelo modelo ligado.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/helloworldparent.json" highlight="10,23":::

O exemplo a seguir mostra um modelo que implanta um endereço IP público e devolve o ID de recurso do recurso Azure para esse IP público:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/public-ip.json" highlight="27":::

Para utilizar o endereço IP público a partir do modelo anterior ao implementar um equilibrador de carga, ligue-o ao modelo e declare uma dependência do `Microsoft.Resources/deployments` recurso. O endereço IP público no balançador de carga é definido para o valor de saída a partir do modelo ligado.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json" highlight="28,41":::

## <a name="deployment-history"></a>Histórico de implantação

O Gestor de Recursos processa cada modelo como uma implementação separada no histórico de implementação. Um modelo principal com três modelos ligados ou aninhados aparece no histórico de implementação como:

![Histórico de implantação](./media/linked-templates/deployment-history.png)

Pode utilizar estas entradas separadas no histórico para recuperar os valores de saída após a implantação. O modelo a seguir cria um endereço IP público e produz o endereço IP:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "publicIPAddresses_name": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2018-11-01",
      "name": "[parameters('publicIPAddresses_name')]",
      "location": "southcentralus",
      "properties": {
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Static",
        "idleTimeoutInMinutes": 4,
        "dnsSettings": {
          "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
        }
      },
      "dependsOn": []
    }
  ],
  "outputs": {
    "returnedIPAddress": {
      "type": "string",
      "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
    }
  }
}
```

O modelo a seguir liga-se ao modelo anterior. Cria três endereços IP públicos.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "[concat('linkedTemplate', copyIndex())]",
      "copy": {
        "count": 3,
        "name": "ip-loop"
      },
      "properties": {
        "mode": "Incremental",
        "templateLink": {
        "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
        "contentVersion": "1.0.0.0"
        },
        "parameters":{
          "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
        }
      }
    }
  ]
}
```

Após a implementação, pode recuperar os valores de saída com o seguinte script PowerShell:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
  $name = 'linkedTemplate' + $i;
  $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
  Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Ou, guião do Azure CLI numa concha bash:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
  name="linkedTemplate$i";
  deployment=$(az deployment group show -g examplegroup -n $name);
  ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
  echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Assegurar um modelo externo

Embora o modelo ligado tenha de estar disponível externamente, não precisa de estar geralmente disponível para o público. Pode adicionar o seu modelo a uma conta de armazenamento privada que esteja acessível apenas ao proprietário da conta de armazenamento. Em seguida, cria um token de assinatura de acesso partilhado (SAS) para permitir o acesso durante a implementação. Adicione o símbolo SAS ao URI para o modelo ligado. Mesmo que o token seja passado como uma cadeia segura, o URI do modelo ligado, incluindo o token SAS, é registado nas operações de implantação. Para limitar a exposição, estabeleça uma expiração para o token.

O ficheiro de parâmetros também pode ser limitado ao acesso através de um token SAS.

Atualmente, não é possível ligar-se a um modelo numa conta de armazenamento que está por detrás de uma [firewall de armazenamento Azure](../../storage/common/storage-network-security.md).

> [!IMPORTANT]
> Em vez de garantir o seu modelo ligado com um token SAS, considere criar uma [especificação de modelo](template-specs.md). A especificação do modelo armazena de forma segura o modelo principal e os seus modelos ligados como um recurso na sua subscrição Azure. Você usa O RBAC Azure para conceder acesso aos utilizadores que precisam de implementar o modelo.

O exemplo a seguir mostra como passar um token SAS ao ligar a um modelo:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "securestring" }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

No PowerShell, obtém-se um símbolo para o recipiente e implanta os modelos com os seguintes comandos. Note que o `containerSasToken` parâmetro é definido no modelo. Não é um parâmetro no `New-AzResourceGroupDeployment` comando.

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Para o Azure CLI numa concha bash, obtém-se um símbolo para o recipiente e implanta os modelos com o seguinte código:

```azurecli-interactive
#!/bin/bash

expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
  --resource-group ManageGroup \
  --name storagecontosotemplates \
  --query connectionString)
token=$(az storage container generate-sas \
  --name templates \
  --expiry $expiretime \
  --permissions r \
  --output tsv \
  --connection-string $connection)
url=$(az storage blob url \
  --container-name templates \
  --name parent.json \
  --output tsv \
  --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az deployment group create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Modelos de exemplo

Os exemplos a seguir mostram utilizações comuns de modelos ligados.

|Modelo principal  |Modelo associado |Descrição  |
|---------|---------| ---------|
|[Olá, mundo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[modelo ligado](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Retorna a corda do modelo ligado. |
|[Balanceador de carga com endereço IP público](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[modelo ligado](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Devolve o endereço IP público do modelo ligado e define esse valor no equilibrador de carga. |
|[Vários endereços IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [modelo ligado](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Cria vários endereços IP públicos em modelo ligado.  |

## <a name="next-steps"></a>Passos seguintes

* Para passar por um tutorial, consulte [Tutorial: Implemente um modelo ligado](./deployment-tutorial-linked-template.md).
* Para saber mais sobre a definição da ordem de implantação dos seus recursos, consulte [Definir a ordem para a implantação de recursos em modelos ARM](define-resource-dependency.md).
* Para aprender a definir um recurso, mas criar muitos casos do mesmo, consulte a iteração de [recursos nos modelos ARM](copy-resources.md).
* Para etapas na configuração de um modelo numa conta de armazenamento e na geração de um token SAS, consulte [recursos de implantação com modelos ARM e Azure PowerShell](deploy-powershell.md) ou [Recursos de implantação com modelos ARM e CLI Azure](deploy-cli.md).
