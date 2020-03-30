---
title: Modelos de ligação para implementação
description: Descreve como usar modelos ligados num modelo de Gestor de Recursos Azure para criar uma solução de modelo modular. Mostra como passar os valores dos parâmetros, especificar um ficheiro de parâmetros e URLs criados dinamicamente.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 322797383ee865ceb66c44793387da827aeb8879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131917"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Utilizar modelos ligados e aninhados ao implementar recursos do Azure

Para implementar soluções complexas, pode quebrar o seu modelo em muitos modelos relacionados e, em seguida, implantá-los juntos através de um modelo principal. Os modelos relacionados podem ser ficheiros separados ou sintaxe de modelo que está incorporado no modelo principal. Este artigo usa o **modelo de ligação** termo para se referir a um ficheiro de modelo separado que é referenciado através de um link do modelo principal. Ele usa o **termo aninhado modelo** para se referir a sintaxe de modelo incorporado dentro do modelo principal.

Para soluções pequenas a médias, um único modelo é mais fácil de entender e manter. Pode ver todos os recursos e valores num único ficheiro. Para cenários avançados, os modelos ligados permitem-lhe decompor a solução em componentes direcionados. Pode facilmente reutilizar estes modelos para outros cenários.

Para um tutorial, consulte [Tutorial: crie modelos ligados do Gestor de Recursos Azure.](template-tutorial-create-linked-templates.md)

> [!NOTE]
> Para modelos ligados ou aninhados, só pode utilizar o modo de implantação [Incremental.](deployment-modes.md)
>

## <a name="nested-template"></a>Modelo aninhado

Para nidificar um modelo, adicione um recurso de [implantação](/azure/templates/microsoft.resources/deployments) ao seu modelo principal. Na propriedade do **modelo,** especifique a sintaxe do modelo.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "name": "nestedTemplate1",
      "apiVersion": "2017-05-10",
      "type": "Microsoft.Resources/deployments",
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

O exemplo seguinte implementa uma conta de armazenamento através de um modelo aninhado.

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
      "name": "nestedTemplate1",
      "apiVersion": "2017-05-10",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Ao utilizar um modelo aninhado, pode especificar se as expressões do modelo são avaliadas dentro do âmbito do modelo dos pais ou do modelo aninhado. O âmbito determina como os parâmetros, variáveis e funções como [o grupo de recursos](template-functions-resource.md#resourcegroup) e a [subscrição](template-functions-resource.md#subscription) são resolvidos.

Você define o `expressionEvaluationOptions` âmbito através da propriedade. Por padrão, `expressionEvaluationOptions` a propriedade `outer`está definida para, o que significa que utiliza o âmbito do modelo dos pais. Desdefinir `inner` o valor para fazer com que as expressões sejam avaliadas no âmbito do modelo aninhado.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2017-05-10",
  "name": "nestedTemplate1",
  "properties": {
  "expressionEvaluationOptions": {
    "scope": "inner"
  },
  ...
```

O seguinte modelo demonstra como as expressões do modelo são resolvidas de acordo com o âmbito. Contém uma variável chamada `exampleVar` que é definida tanto no modelo dos pais como no modelo aninhado. Devolve o valor da variável.

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
      "apiVersion": "2017-05-10",
      "name": "nestedTemplate1",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

O valor `exampleVar` das variações dependendo `scope` do `expressionEvaluationOptions`valor do imóvel em . A tabela que se segue mostra os resultados de ambos os âmbitos.

| `expressionEvaluationOptions` `scope` | Saída |
| ----- | ------ |
| interior | do modelo aninhado |
| exterior (ou padrão) | do modelo dos pais |

O exemplo seguinte implementa um servidor SQL e recupera um segredo de cofre chave para usar para a palavra-passe. O âmbito é `inner` definido porque cria dinamicamente `adminPassword.reference.keyVault` o ID `parameters`do cofre chave (ver nos modelos externos) e passa-o como parâmetro para o modelo aninhado.

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
        }
      }
    }
  ],
  "outputs": {
  }
}
```

> [!NOTE]
>
> Quando o âmbito `outer`está definido, não `reference` pode utilizar a função na secção de saídas de um modelo aninhado para um recurso que implementou no modelo aninhado. Para devolver os valores de um recurso implantado `inner` num modelo aninhado, use o âmbito ou converta o seu modelo aninhado num modelo ligado.

## <a name="linked-template"></a>Modelo ligado

Para ligar um modelo, adicione um recurso de [implementação](/azure/templates/microsoft.resources/deployments) ao seu modelo principal. Na propriedade **templateLink,** especifique o URI do modelo para incluir. O exemplo seguinte liga-se a um modelo que implementa uma nova conta de armazenamento.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
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

Ao referir um modelo ligado, o valor não `uri` deve ser um ficheiro local ou um ficheiro que só esteja disponível na sua rede local. Você deve fornecer um valor URI que descarregável como **http** ou **https**. 

> [!NOTE]
>
> Pode referir modelos utilizando parâmetros que, em última análise, resolvem `_artifactsLocation` algo que utiliza **http** ou **https,** por exemplo, utilizando o parâmetro como o seguinte:`"uri": "[concat(parameters('_artifactsLocation'), '/shared/os-disk-parts-md.json', parameters('_artifactsLocationSasToken'))]",`



O Gestor de Recursos deve ter acesso ao modelo. Uma opção é colocar o seu modelo ligado numa conta de armazenamento e usar o URI para esse item.

### <a name="parameters-for-linked-template"></a>Parâmetros para modelo ligado

Pode fornecer os parâmetros para o seu modelo ligado, seja num ficheiro externo ou inline. Ao fornecer um ficheiro de parâmetro externo, utilize a propriedade **parâmetrosLink:**

```json
"resources": [
  {
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2018-05-01",
  "name": "linkedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
      "contentVersion":"1.0.0.0"
    },
    "parametersLink": {
      "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
      "contentVersion":"1.0.0.0"
    }
  }
  }
]
```

Para passar os valores dos parâmetros inline, utilize a propriedade dos **parâmetros.**

```json
"resources": [
  {
   "type": "Microsoft.Resources/deployments",
   "apiVersion": "2018-05-01",
   "name": "linkedTemplate",
   "properties": {
     "mode": "Incremental",
     "templateLink": {
      "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
      "contentVersion":"1.0.0.0"
     },
     "parameters": {
      "StorageAccountName":{"value": "[parameters('StorageAccountName')]"}
    }
   }
  }
]
```

Não pode usar os parâmetros inline e uma ligação a um ficheiro de parâmetros. A implantação falha com `parametersLink` um `parameters` erro quando ambos e são especificados.

## `contentVersion`

Você não tem que `contentVersion` fornecer a `templateLink` `parametersLink` propriedade para a propriedade ou propriedade. Se não fornecer um, `contentVersion`a versão atual do modelo é implementada. Se fornecer um valor para a versão de conteúdo, deve coincidir com a versão no modelo ligado; caso contrário, a implantação falha com um erro.

## <a name="using-variables-to-link-templates"></a>Usando variáveis para ligar modelos

Os exemplos anteriores mostraram valores de URL codificados para as ligações do modelo. Esta abordagem pode funcionar para um modelo simples, mas não funciona bem para um grande conjunto de modelos modulares. Em vez disso, pode criar uma variável estática que armazena um URL base para o modelo principal e, em seguida, criar dinamicamente URLs para os modelos ligados a partir desse URL base. O benefício desta abordagem é que você pode facilmente mover ou bifurcar o modelo porque você precisa alterar apenas a variável estática no modelo principal. O modelo principal passa as URIs corretas em todo o modelo decomposto.

O exemplo seguinte mostra como usar um URL base para criar dois URLs para modelos ligados **(sharedTemplateUrl** e **vmTemplate**).

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[uri(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[uri(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Também pode usar a [implementação()](template-functions-deployment.md#deployment) para obter o URL base para o modelo de corrente, e usá-lo para obter o URL para outros modelos no mesmo local. Esta abordagem é útil se a sua localização do modelo mudar ou se pretender evitar URLs de codificação dura no ficheiro do modelo. A propriedade modeloLink só é devolvida quando se liga a um modelo remoto com um URL. Se você está usando um modelo local, essa propriedade não está disponível.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

Em última análise, você usaria `uri` a `templateLink` variável na propriedade de uma propriedade.

```json
"templateLink": {
 "uri": "[variables('sharedTemplateUrl')]",
 "contentVersion":"1.0.0.0"
}
```

## <a name="using-copy"></a>Usando cópia

Para criar várias instâncias de um recurso com um modelo aninhado, adicione o elemento de cópia ao nível do recurso **Microsoft.Resources/deployments.** Ou, se o âmbito for interior, pode adicionar a cópia dentro do modelo aninhado.

O modelo de exemplo seguinte mostra como usar a cópia com um modelo aninhado.

```json
"resources": [
  {
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2018-05-01",
  "name": "[concat('nestedTemplate', copyIndex())]",
  // yes, copy works here
  "copy":{
    "name": "storagecopy",
    "count": 2
  },
  "properties": {
    "mode": "Incremental",
    "expressionEvaluationOptions": {
    "scope": "inner"
    },
    "template": {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Para obter um valor de saída a partir de um `"[reference('deploymentName').outputs.propertyName.value]"`modelo ligado, recupere o valor da propriedade com a sintaxe como: .

Ao obter uma propriedade de saída a partir de um modelo ligado, o nome da propriedade não deve incluir um traço.

Os exemplos seguintes demonstram como referenciar um modelo ligado e recuperar um valor de saída. O modelo ligado devolve uma mensagem simples.  Primeiro, o modelo ligado:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {
    "greetingMessage": {
      "value": "Hello World",
      "type" : "string"
    }
  }
}
```

O modelo principal implanta o modelo ligado e obtém o valor devolvido. Note que faz referência ao recurso de implantação pelo nome, e utiliza o nome da propriedade devolvida pelo modelo ligado.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[uri(deployment().properties.templateLink.uri, 'helloworld.json')]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('linkedTemplate').outputs.greetingMessage.value]"
    }
  }
}
```

Tal como acontece com outros tipos de recursos, pode definir dependências entre o modelo ligado e outros recursos. Quando outros recursos requerem um valor de saída do modelo ligado, certifique-se de que o modelo ligado é implantado antes deles. Ou, quando o modelo ligado depender de outros recursos, certifique-se de que outros recursos são implantados antes do modelo ligado.

O exemplo seguinte mostra um modelo que implementa um endereço IP público e devolve o ID de recursos do recurso Azure para esse IP público:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
      "location": "eastus",
      "properties": {
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 4
      },
      "dependsOn": []
    }
  ],
  "outputs": {
    "resourceID": {
      "type": "string",
      "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
    }
  }
}
```

Para utilizar o endereço IP público do modelo anterior ao implantar um equilibrador de `Microsoft.Resources/deployments` carga, ligue-se ao modelo e declare uma dependência do recurso. O endereço IP público no equilibrista de carga é definido para o valor de saída do modelo ligado.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "loadBalancers_name": {
      "defaultValue": "mylb",
      "type": "string"
    },
    "publicIPAddresses_name": {
      "defaultValue": "myip",
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/loadBalancers",
      "apiVersion": "2018-11-01",
      "name": "[parameters('loadBalancers_name')]",
      "location": "eastus",
      "properties": {
        "frontendIPConfigurations": [
          {
            "name": "LoadBalancerFrontEnd",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                // this is where the output value from linkedTemplate is used
                "id": "[reference('linkedTemplate').outputs.resourceID.value]"
              }
            }
          }
        ],
        "backendAddressPools": [],
        "loadBalancingRules": [],
        "probes": [],
        "inboundNatRules": [],
        "outboundNatRules": [],
        "inboundNatPools": []
      },
      // This is where the dependency is declared
      "dependsOn": [
        "linkedTemplate"
      ]
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[uri(deployment().properties.templateLink.uri, 'publicip.json')]",
          "contentVersion": "1.0.0.0"
        },
        "parameters":{
          "publicIPAddresses_name":{"value": "[parameters('publicIPAddresses_name')]"}
        }
      }
    }
  ]
}
```

## <a name="deployment-history"></a>Histórico de implantação

O Gestor de Recursos processa cada modelo como uma implementação separada no histórico de implementação. Um modelo principal com três modelos ligados ou aninhados aparece na história da implantação como:

![Histórico de implantação](./media/linked-templates/deployment-history.png)

Pode utilizar estas entradas separadas na história para recuperar valores de saída após a implantação. O seguinte modelo cria um endereço IP público e saídas do endereço IP:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

O modelo seguinte liga-se ao modelo anterior. Cria três endereços IP públicos.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
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

Ou, guião Azure CLI numa concha bash:

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

Embora o modelo ligado tenha de estar disponível externamente, não precisa de estar geralmente disponível para o público. Pode adicionar o seu modelo a uma conta de armazenamento privada acessível apenas ao proprietário da conta de armazenamento. Em seguida, cria um símbolo de assinatura de acesso partilhado (SAS) para permitir o acesso durante a implementação. Adicione o token SAS ao URI para o modelo ligado. Embora o símbolo seja passado como uma corda segura, o URI do modelo ligado, incluindo o token SAS, está registado nas operações de implantação. Para limitar a exposição, estabeleça uma expiração para o símbolo.

O ficheiro parâmetro também pode ser limitado ao acesso através de um token SAS.

Atualmente, não é possível ligar a um modelo numa conta de armazenamento que esteja por trás de uma [firewall de Armazenamento Azure](../../storage/common/storage-network-security.md).

O exemplo que se segue mostra como passar um símbolo SAS ao ligar-se a um modelo:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "containerSasToken": { "type": "securestring" }
  },
  "resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
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

No PowerShell, obtém-se um símbolo para o recipiente e desdobra os modelos com os seguintes comandos. Note que o parâmetro **contentorSasToken** está definido no modelo. Não é um parâmetro no comando **new-AzResourceGroupDeployment.**

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Para o Azure CLI numa concha bash, obtém-se um símbolo para o recipiente e desdobra os modelos com o seguinte código:

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

Os exemplos seguintes mostram usos comuns de modelos ligados.

|Modelo principal  |Modelo ligado |Descrição  |
|---------|---------| ---------|
|[Olá Mundo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[modelo ligado](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Retorna a corda do modelo ligado. |
|[Balancer de carga com endereço IP público](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[modelo ligado](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Devolve o endereço IP público do modelo ligado e define esse valor no equilibrador de carga. |
|[Vários endereços IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [modelo ligado](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Cria vários endereços IP públicos no modelo ligado.  |

## <a name="next-steps"></a>Passos seguintes

* Para passar por um tutorial, consulte [Tutorial: crie modelos ligados do Gestor de Recursos Azure.](template-tutorial-create-linked-templates.md)
* Para conhecer a definição da ordem de implantação dos seus recursos, consulte [dependências de definição nos modelos do Gestor](define-resource-dependency.md)de Recursos Azure .
* Para aprender a definir um recurso mas criar muitos casos do mesmo, consulte [Criar múltiplas instâncias de recursos no Gestor de Recursos Azure.](copy-resources.md)
* Para passos na configuração de um modelo numa conta de armazenamento e na geração de uma ficha SAS, consulte [a implantação de recursos com modelos](deploy-powershell.md) de Gestor de Recursos e o Azure PowerShell ou [desloque recursos com modelos](deploy-cli.md)de Gestor de Recursos e ClI Azure .
