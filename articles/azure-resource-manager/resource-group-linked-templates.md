---
title: Ligar a modelos para a implementação do Azure | Documentos da Microsoft
description: Descreve como utilizar modelos ligados num modelo Azure Resource Manager para criar uma solução de modelo modular. Mostra como passar valores de parâmetros, especifique um ficheiro de parâmetros e URLs criados dinamicamente.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 27d8c4b2-1e24-45fe-88fd-8cf98a6bb2d2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/18/2019
ms.author: tomfitz
ms.openlocfilehash: d4ecccf8787e369b9a3270eab2d01a01ce7ae0c7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58174312"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Utilizar ligados e aninhados modelos durante a implantação de recursos do Azure

Para implantar sua solução, pode utilizar um modelo único ou um modelo principal com muitos modelos relacionados. O modelo relacionado pode ser um arquivo separado que está ligado a partir do modelo de principal ou um modelo que esteja aninhado dentro do modelo principal.

Para pequenas e médias soluções, é mais fácil de compreender e manter um único modelo. Pode ver todos os recursos e os valores num único arquivo. Para cenários avançados, modelos ligados permitem-lhe dividir a solução para os componentes-alvo e reutilizar os modelos.

Ao utilizar modelos ligados, criar um modelo principal que recebe os valores de parâmetro durante a implementação. O modelo principal contém todos os modelos ligados e transmite valores para esses modelos, conforme necessário.

Para obter um tutorial, veja [Tutorial: criar modelos do Azure Resource Manager ligados](./resource-manager-tutorial-create-linked-templates.md).

> [!NOTE]
> Para modelos ligados ou aninhados, só pode utilizar [Incremental](deployment-modes.md) modo de implementação.
>

## <a name="link-or-nest-a-template"></a>Associar ou aninhar um modelo

Para ligar a outro modelo, adicione uma **implementações** recursos ao seu modelo principal.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "linkedTemplate",
    "properties": {
        "mode": "Incremental",
        <nested-template-or-external-template>
    }
  }
]
```

As propriedades que fornecer para o recurso de implantação variam consoante quer esteja a criar uma ligação para um modelo externo ou aninhar um modelo inline no modelo principal.

### <a name="nested-template"></a>Modelo aninhado

Para aninhar o modelo dentro do modelo principal, utilize o **modelo** propriedade e especifique a sintaxe do modelo.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "nestedTemplate",
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-07-01",
            "name": "[variables('storageName')]",
            "location": "West US",
            "properties": {
              "accountType": "Standard_LRS"
            }
          }
        ]
      }
    }
  }
]
```

> [!NOTE]
> Para aninhados modelos, é possível utilizar parâmetros ou variáveis que estão definidas dentro do modelo aninhado. Pode usar parâmetros e variáveis do modelo principal. No exemplo anterior, `[variables('storageName')]` recupera um valor a partir do modelo principal, não o modelo aninhado. Esta restrição não é aplicável a modelos externos.
>
> Dois recursos definida dentro de um modelo de aninhamento e um recurso depende de outro, o valor da dependência é simplesmente o nome do recurso dependentes:
> ```json
> "dependsOn": [
>   "[variables('storageAccountName')]"
> ],
> ```
>
> Não é possível utilizar o `reference` função na secção de saídas de um modelo aninhado. Para devolver os valores para um recurso implementado num modelo aninhado, converta seu modelo aninhado para um modelo ligado.

O modelo aninhado requer o [as mesmas propriedades](resource-group-authoring-templates.md) como um modelo padrão.

### <a name="external-template-and-external-parameters"></a>Modelo externo e parâmetros externos

Para ligar a um modelo externo e o ficheiro de parâmetros, utilize **templateLink** e **parametersLink**. Quando cria uma ligação a um modelo, o serviço de Gestor de recursos tem de ser capaz de aceder ao mesmo. Não é possível especificar um ficheiro local ou um ficheiro que só está disponível na sua rede local. Apenas pode fornecer um valor URI que inclui qualquer um **http** ou **https**. Uma opção é colocar o seu modelo ligado numa conta de armazenamento e utilizar o URI para esse item.

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

Não é necessário fornecer o `contentVersion` propriedade do modelo ou parâmetros. Se não fornecer um valor de versão do conteúdo, a versão atual do modelo é implementada. Se fornecer um valor para a versão do conteúdo, tem de corresponder a versão no modelo ligado; caso contrário, a implementação falhar com um erro.

### <a name="external-template-and-inline-parameters"></a>Parâmetros de modelo e inline externos

Em alternativa, pode fornecer o parâmetro inline. Não é possível utilizar parâmetros inline e uma ligação para um ficheiro de parâmetros. A implementação falhar com um erro quando ambos `parametersLink` e `parameters` são especificados.

Transmita um valor a partir do modelo de principal para o modelo ligado, utilize **parâmetros**.

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

Também pode utilizar [deployment()](resource-group-template-functions-deployment.md#deployment) para obter o URL de base para o modelo atual e usá-lo para obter o URL de outros modelos na mesma localização. Essa abordagem é útil se as alterações de localização do modelo ou que quiser evitar codificarem URLs no ficheiro de modelo. A propriedade templateLink apenas é retornada quando criar uma ligação para um modelo remoto com um URL. Se estiver a utilizar um modelo de local, essa propriedade não está disponível.

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

Como outros tipos de recursos, pode definir as dependências entre o modelo ligado e outros recursos. Por conseguinte, quando outros recursos requerem um valor de saída do modelo ligado, certifique-se do que modelo ligado é implementado antes-los. Ou, quando o modelo ligado depende de outros recursos, certifique-se de que outros recursos são implementados antes do modelo ligado.

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

## <a name="linked-and-nested-templates-in-deployment-history"></a>Modelos ligados e aninhados no histórico de implementação

Resource Manager processa cada modelo como uma implementação separada no histórico de implementação. Por conseguinte, um modelo principal com três modelos ligados ou aninhados é apresentada no histórico de implementação, como:

![Histórico de implementações](./media/resource-group-linked-templates/deployment-history.png)

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

No PowerShell, obter um token para o contentor e implementar modelos com os seguintes comandos. Tenha em atenção que o **containerSasToken** parâmetro é definido no modelo. Não é um parâmetro no **New-AzResourceGroupDeployment** comando.

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

## <a name="next-steps"></a>Passos Seguintes

* Para seguir um tutorial, veja [Tutorial: criar modelos do Azure Resource Manager ligados](./resource-manager-tutorial-create-linked-templates.md).
* Para saber mais sobre o tipo de definir a ordem de implementação para os seus recursos, veja [definir dependências nos modelos Azure Resource Manager](resource-group-define-dependencies.md).
* Para saber como definir um recurso, mas criar várias instâncias do mesmo, consulte [criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md).
* Para obter passos sobre como configurar um modelo numa conta de armazenamento e gerar um token SAS, consulte [implementar recursos com modelos do Resource Manager e o Azure PowerShell](resource-group-template-deploy.md) ou [implementar recursos com modelos do Resource Manager e CLI do Azure](resource-group-template-deploy-cli.md).
