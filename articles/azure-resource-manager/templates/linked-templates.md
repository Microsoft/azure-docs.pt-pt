---
title: Modelos de ligação para implementação
description: Descreve como utilizar modelos ligados num modelo Azure Resource Manager para criar uma solução de modelo modular. Mostra como passar valores de parâmetros, especifique um ficheiro de parâmetros e URLs criados dinamicamente.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: e26b795a645ab9128dd738ba6a54b66ac0b7da2a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357375"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Utilizar ligados e aninhados modelos durante a implantação de recursos do Azure

Para implementar soluções complexas, pode quebrar o seu modelo em muitos modelos relacionados e, em seguida, implantá-los juntos através de um modelo principal. Os modelos relacionados podem ser ficheiros separados ou sintaxe de modelo que está incorporado no modelo principal. Este artigo usa o **modelo ligado** ao termo para se referir a um ficheiro de modelo separado que está ligado ao modelo principal. Ele usa o **termo aninhado modelo** para se referir a sintaxe de modelo incorporado dentro do modelo principal.

Para pequenas e médias soluções, é mais fácil de compreender e manter um único modelo. Pode ver todos os recursos e os valores num único arquivo. Para cenários avançados, os modelos ligados permitem-lhe decompor a solução em componentes direcionados. Pode facilmente reutilizar estes modelos para outros cenários.

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

### <a name="scope-for-expressions-in-nested-templates"></a>Âmbito para expressões em modelos aninhados

Ao utilizar um modelo aninhado, pode especificar se as expressões do modelo são avaliadas dentro do âmbito do modelo dos pais ou do modelo aninhado. O âmbito determina como os parâmetros, variáveis e funções como [o grupo de recursos](template-functions-resource.md#resourcegroup) e a [subscrição](template-functions-resource.md#subscription) são resolvidos.

Você define o âmbito através da propriedade `expressionEvaluationOptions`. Por padrão, a propriedade `expressionEvaluationOptions` está definida para `outer`, o que significa que utiliza o âmbito do modelo dos pais. Detete o valor para `inner` para definir expressões para o modelo aninhado.

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

O valor da variável muda com base no âmbito. A tabela que se segue mostra os resultados de ambos os âmbitos.

| Âmbito | Saída |
| ----- | ------ |
| interior | do modelo aninhado |
| exterior (ou padrão) | do modelo dos pais |

O exemplo seguinte implementa um servidor SQL e recupera um segredo de cofre chave para usar para a palavra-passe. O âmbito está definido para `inner` porque cria dinamicamente o ID do cofre chave e passa-o como parâmetro para o modelo aninhado.

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

> [!NOTE]
>
> Quando o âmbito está definido para `outer`, não pode utilizar a função `reference` na secção de saídas de um modelo aninhado para um recurso que implementou no modelo aninhado. Para devolver os valores de um recurso implantado num modelo aninhado, use o âmbito interno ou converta o seu modelo aninhado num modelo ligado.

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

Não é possível especificar um ficheiro local ou um ficheiro que só está disponível na sua rede local. Só pode fornecer um valor URI que inclua **http** ou **https**. O Gestor de Recursos deve ter acesso ao modelo. Uma opção é colocar o seu modelo ligado numa conta de armazenamento e utilizar o URI para esse item.

Você não tem que fornecer a propriedade `contentVersion` para o modelo ou parâmetros. Se não fornecer um valor de versão do conteúdo, a versão atual do modelo é implementada. Se fornecer um valor para a versão do conteúdo, tem de corresponder a versão no modelo ligado; caso contrário, a implementação falhar com um erro.

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

Não é possível utilizar parâmetros inline e uma ligação para um ficheiro de parâmetros. A implementação falha com um erro quando são especificadas `parametersLink` e `parameters`.

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

## <a name="using-variables-to-link-templates"></a>Usando variáveis para associar modelos

Os exemplos anteriores mostraram valores codificados de URL para as ligações de modelo. Essa abordagem possa funcionar para um modelo simples, mas não funciona bem quando trabalhar com um grande conjunto de modelos modulares. Em vez disso, pode criar uma variável estática que armazena um URL de base para o modelo de principal e, em seguida, criar dinamicamente os URLs para os modelos ligados a partir desse URL base. A vantagem dessa abordagem é que pode facilmente transitar ou bifurcar o modelo porque só precisa de alterar a variável estática no modelo principal. O modelo principal passa os URIs correta em todo o modelo decomposto.

O exemplo seguinte mostra como usar um URL base para criar dois URLs para modelos ligados **(sharedTemplateUrl** e **vmTemplate**).

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Também pode usar a [implementação()](template-functions-deployment.md#deployment) para obter o URL base para o modelo de corrente, e usá-lo para obter o URL para outros modelos no mesmo local. Essa abordagem é útil se as alterações de localização do modelo ou que quiser evitar codificarem URLs no ficheiro de modelo. A propriedade templateLink apenas é retornada quando criar uma ligação para um modelo remoto com um URL. Se estiver a utilizar um modelo de local, essa propriedade não está disponível.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>Obter valores do modelo ligado

Para obter um valor de saída a partir de um modelo ligado, recupere o valor da propriedade com a sintaxe como: `"[reference('deploymentName').outputs.propertyName.value]"`.

Ao obter uma propriedade de saída a partir de um modelo ligado, o nome da propriedade não pode incluir um traço.

Os exemplos seguintes demonstram como fazer referência um modelo ligado e obter um valor de saída. O modelo ligado devolve uma mensagem simple.

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

O modelo principal implanta o modelo ligado e obtém o valor retornado. Tenha em atenção que referencia o recurso de implantação por nome, e utiliza o nome da propriedade devolvido pelo modelo de ligado.

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

Como outros tipos de recursos, pode definir as dependências entre o modelo ligado e outros recursos. Quando outros recursos requerem um valor de saída do modelo ligado, certifique-se de que o modelo ligado é implantado antes deles. Ou, quando o modelo ligado depende de outros recursos, certifique-se de que outros recursos são implementados antes do modelo ligado.

O exemplo seguinte mostra um modelo que implementa um endereço IP público e devolve o ID de recurso:

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

Para utilizar o endereço IP público do modelo anterior ao implementar um balanceador de carga, ligar para o modelo e adicionar uma dependência no recurso de implantação. O endereço IP público no balanceador de carga está definido para o valor de saída do modelo ligado.

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

## <a name="deployment-history"></a>Histórico de implementações

Resource Manager processa cada modelo como uma implementação separada no histórico de implementação. Um modelo principal com três modelos ligados ou aninhados aparece na história da implantação como:

![Histórico de implementações](./media/linked-templates/deployment-history.png)

Pode utilizar estas entradas separadas no histórico para recuperar os valores de saída após a implementação. O modelo seguinte cria um endereço IP público e produz o endereço IP:

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

As seguintes ligações de modelo para o modelo anterior. Ele cria três endereços IP públicos.

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

Após a implementação, pode recuperar os valores de saída com o seguinte script do PowerShell:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
  $name = 'linkedTemplate' + $i;
  $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
  Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Em alternativa, script da CLI do Azure numa Bash shell:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
  name="linkedTemplate$i";
  deployment=$(az group deployment show -g examplegroup -n $name);
  ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
  echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Proteger um modelo externo

Embora o modelo ligado tem de estar disponível externamente, ele não precisa estar disponível ao público. É possível adicionar o seu modelo para uma conta de armazenamento privado que esteja acessível para apenas o proprietário da conta de armazenamento. Depois, vai criar um token de assinatura (SAS) de acesso partilhado para ativar o acesso durante a implementação. Adicionar esse token SAS para o URI para o modelo ligado. Mesmo que o token é passado como uma cadeia segura, o URI do modelo ligado, incluindo o token SAS, é registado nas operações de implementação. Para limitar a exposição, defina uma expiração para o token.

O ficheiro de parâmetros pode também ser limitado a acesso por meio de um token SAS.

Atualmente, não é possível ligar a um modelo numa conta de armazenamento que esteja por trás de uma [firewall de Armazenamento Azure](../../storage/common/storage-network-security.md).

O exemplo seguinte mostra como passar um token SAS quando ligar a um modelo:

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

No PowerShell, obter um token para o contentor e implementar modelos com os seguintes comandos. Note que o parâmetro **contentorSasToken** está definido no modelo. Não é um parâmetro no comando **new-AzResourceGroupDeployment.**

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Para a CLI do Azure numa Bash shell, obter um token para o contentor e implementar modelos com o código a seguir:

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
az group deployment create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Modelos de exemplo

Os exemplos seguintes mostram as utilizações comuns dos modelos ligados.

|Modelo principal  |Modelo ligado |Descrição  |
|---------|---------| ---------|
|[Olá Mundo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[modelo ligado](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Devolve a cadeia de modelo ligado. |
|[Balancer de carga com endereço IP público](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[modelo ligado](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Devolve o endereço IP público a partir de modelo ligado e define esse valor no balanceador de carga. |
|[Vários endereços IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [modelo ligado](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Cria vários endereços IP públicos no modelo ligado.  |

## <a name="next-steps"></a>Passos seguintes

* Para passar por um tutorial, consulte [Tutorial: crie modelos ligados do Gestor de Recursos Azure.](template-tutorial-create-linked-templates.md)
* Para conhecer a definição da ordem de implantação dos seus recursos, consulte [dependências de definição nos modelos do Gestor](define-resource-dependency.md)de Recursos Azure .
* Para aprender a definir um recurso mas criar muitos casos do mesmo, consulte [Criar múltiplas instâncias de recursos no Gestor de Recursos Azure.](copy-resources.md)
* Para passos na configuração de um modelo numa conta de armazenamento e na geração de uma ficha SAS, consulte [a implantação de recursos com modelos](deploy-powershell.md) de Gestor de Recursos e o Azure PowerShell ou [desloque recursos com modelos](deploy-cli.md)de Gestor de Recursos e ClI Azure .
