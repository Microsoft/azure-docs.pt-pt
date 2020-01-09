---
title: Vincular modelos para implantação
description: Descreve como utilizar modelos ligados num modelo Azure Resource Manager para criar uma solução de modelo modular. Mostra como passar valores de parâmetros, especifique um ficheiro de parâmetros e URLs criados dinamicamente.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 5d01aa8df6eba9b4e68937434dffae315a445cc8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477450"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Utilizar ligados e aninhados modelos durante a implantação de recursos do Azure

Para implantar soluções complexas, você pode dividir o modelo em muitos modelos relacionados e implantá-los juntos por meio de um modelo principal. Os modelos relacionados podem ser arquivos separados ou a sintaxe de modelo que é inserida no modelo principal. Este artigo usa o termo **vinculado modelo** para se referir a um arquivo de modelo separado que está vinculado a do modelo principal. Ele usa o termo **modelo aninhado** para se referir à sintaxe de modelo incorporado no modelo principal.

Para pequenas e médias soluções, é mais fácil de compreender e manter um único modelo. Pode ver todos os recursos e os valores num único arquivo. Para cenários avançados, os modelos vinculados permitem dividir a solução em componentes de destino. Você pode facilmente reutilizar esses modelos para outros cenários.

Para obter um tutorial, veja [Tutorial: criar modelos do Azure Resource Manager ligados](template-tutorial-create-linked-templates.md).

> [!NOTE]
> Para modelos vinculados ou aninhados, você só pode usar o modo de implantação [incremental](deployment-modes.md) .
>

## <a name="nested-template"></a>Modelo aninhado

Para aninhar um modelo, adicione um [recurso de implantações](/azure/templates/microsoft.resources/deployments) ao seu modelo principal. Na propriedade de **modelo** , especifique a sintaxe do modelo.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate1",
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

O exemplo a seguir implanta uma conta de armazenamento por meio de um modelo aninhado.

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
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate1",
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
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
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

### <a name="scope-for-expressions-in-nested-templates"></a>Escopo para expressões em modelos aninhados

Ao usar um modelo aninhado, você pode especificar se as expressões de modelo são avaliadas dentro do escopo do modelo pai ou do modelo aninhado. O escopo determina como os parâmetros, as variáveis e as funções, como o [resourcegroup](template-functions-resource.md#resourcegroup) e a [assinatura](template-functions-resource.md#subscription) , são resolvidos.

Você define o escopo por meio da propriedade `expressionEvaluationOptions`. Por padrão, a propriedade `expressionEvaluationOptions` é definida como `outer`, o que significa que ele usa o escopo do modelo pai. Defina o valor como `inner` para as expressões de escopo para o modelo aninhado.

```json
{
  "apiVersion": "2017-05-10",
  "name": "nestedTemplate1",
  "type": "Microsoft.Resources/deployments",
  "properties": {
    "expressionEvaluationOptions": {
      "scope": "inner"
    },
    ...
```

O modelo a seguir demonstra como as expressões de modelo são resolvidas de acordo com o escopo. Ele contém uma variável chamada `exampleVar` que é definida no modelo pai e no modelo aninhado. Ele retorna o valor da variável.

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
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate1",
            "type": "Microsoft.Resources/deployments",
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

O valor da variável é alterado com base no escopo. A tabela a seguir mostra os resultados para ambos os escopos.

| Âmbito | Saída |
| ----- | ------ |
| Inner | do modelo aninhado |
| externo (ou padrão) | do modelo pai |

O exemplo a seguir implanta um SQL Server e recupera um segredo do Key Vault a ser usado para a senha. O escopo é definido como `inner` porque cria dinamicamente a ID do cofre de chaves e o passa como um parâmetro para o modelo aninhado.

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
            "apiVersion": "2018-05-01",
            "name": "dynamicSecret",
            "type": "Microsoft.Resources/deployments",
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
                            "name": "[variables('sqlServerName')]",
                            "type": "Microsoft.Sql/servers",
                            "apiVersion": "2018-06-01-preview",
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
> Quando o escopo é definido como `outer`, não é possível usar a função `reference` na seção de saídas de um modelo aninhado para um recurso que você implantou no modelo aninhado. Para retornar os valores de um recurso implantado em um modelo aninhado, use o escopo interno ou converta o modelo aninhado em um modelo vinculado.

## <a name="linked-template"></a>Modelo ligado

Para vincular um modelo, adicione um [recurso de implantações](/azure/templates/microsoft.resources/deployments) ao seu modelo principal. Na propriedade **templateLink** , ESPECIFIQUE o URI do modelo a ser incluído. O exemplo a seguir contém links para um modelo que implanta uma nova conta de armazenamento.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
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

Não é possível especificar um ficheiro local ou um ficheiro que só está disponível na sua rede local. Apenas pode fornecer um valor URI que inclui qualquer um **http** ou **https**. O Gerenciador de recursos deve ser capaz de acessar o modelo. Uma opção é colocar o seu modelo ligado numa conta de armazenamento e utilizar o URI para esse item.

Não é necessário fornecer o `contentVersion` propriedade do modelo ou parâmetros. Se não fornecer um valor de versão do conteúdo, a versão atual do modelo é implementada. Se fornecer um valor para a versão do conteúdo, tem de corresponder a versão no modelo ligado; caso contrário, a implementação falhar com um erro.

### <a name="parameters-for-linked-template"></a>Parâmetros para o modelo vinculado

Você pode fornecer os parâmetros para o modelo vinculado, seja em um arquivo externo ou embutido. Ao fornecer um arquivo de parâmetro externo, use a propriedade **parametersLink** :

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

Para passar valores de parâmetro embutidos, use a propriedade **Parameters** .

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

Não é possível utilizar parâmetros inline e uma ligação para um ficheiro de parâmetros. A implementação falhar com um erro quando ambos `parametersLink` e `parameters` são especificados.

## <a name="using-copy"></a>Usando a cópia

Para criar várias instâncias de um recurso com um modelo aninhado, adicione o elemento Copy no nível do recurso **Microsoft. Resources/Implantations** . Ou, se o escopo for interno, você poderá adicionar a cópia dentro do modelo aninhado.

O modelo de exemplo a seguir mostra como usar Copy com um modelo aninhado.

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
            "kind": "StorageV2",
            "sku": {
              "name": "Standard_LRS"
            }
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

O exemplo seguinte mostra como utilizar um URL de base para criar dois URLs para modelos ligados (**sharedTemplateUrl** e **vmTemplate**).

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Também pode utilizar [deployment()](template-functions-deployment.md#deployment) para obter o URL de base para o modelo atual e usá-lo para obter o URL de outros modelos na mesma localização. Essa abordagem é útil se as alterações de localização do modelo ou que quiser evitar codificarem URLs no ficheiro de modelo. A propriedade templateLink apenas é retornada quando criar uma ligação para um modelo remoto com um URL. Se estiver a utilizar um modelo de local, essa propriedade não está disponível.

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>Obter valores do modelo ligado

Para obter um valor de saída a partir de um modelo ligado, obter o valor da propriedade com a sintaxe, como: `"[reference('deploymentName').outputs.propertyName.value]"`.

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

Como outros tipos de recursos, pode definir as dependências entre o modelo ligado e outros recursos. Quando outros recursos exigirem um valor de saída do modelo vinculado, verifique se o modelo vinculado está implantado antes deles. Ou, quando o modelo ligado depende de outros recursos, certifique-se de que outros recursos são implementados antes do modelo ligado.

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

Resource Manager processa cada modelo como uma implementação separada no histórico de implementação. Um modelo principal com três modelos vinculados ou aninhados aparece no histórico de implantação como:

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

No momento, não é possível vincular a um modelo em uma conta de armazenamento que está atrás de um [Firewall de armazenamento do Azure](../../storage/common/storage-network-security.md).

O exemplo seguinte mostra como passar um token SAS quando ligar a um modelo:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "string" }
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

No PowerShell, obter um token para o contentor e implementar modelos com os seguintes comandos. Tenha em atenção que o **containerSasToken** parâmetro é definido no modelo. Não é um parâmetro no comando **New-AzResourceGroupDeployment** .

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
|[Olá Mundo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[Modelo ligado](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Devolve a cadeia de modelo ligado. |
|[Balanceador de carga com o endereço IP público](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[Modelo ligado](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Devolve o endereço IP público a partir de modelo ligado e define esse valor no balanceador de carga. |
|[Vários endereços IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [Modelo ligado](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Cria vários endereços IP públicos no modelo ligado.  |

## <a name="next-steps"></a>Passos seguintes

* Para seguir um tutorial, veja [Tutorial: criar modelos do Azure Resource Manager ligados](template-tutorial-create-linked-templates.md).
* Para saber mais sobre o tipo de definir a ordem de implementação para os seus recursos, veja [definir dependências nos modelos Azure Resource Manager](define-resource-dependency.md).
* Para saber como definir um recurso, mas criar várias instâncias do mesmo, consulte [criar várias instâncias de recursos no Azure Resource Manager](create-multiple-instances.md).
* Para obter passos sobre como configurar um modelo numa conta de armazenamento e gerar um token SAS, consulte [implementar recursos com modelos do Resource Manager e o Azure PowerShell](deploy-powershell.md) ou [implementar recursos com modelos do Resource Manager e CLI do Azure](deploy-cli.md).
