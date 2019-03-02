---
title: Estrutura de modelo do Azure Resource Manager e a sintaxe | Documentos da Microsoft
description: Descreve a estrutura e propriedades de modelos Azure Resource Manager usando sintaxe declarativa do JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2019
ms.author: tomfitz
ms.openlocfilehash: 7819dc62d766a6b35f5c2efe1179cb0adb0ab933
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243555"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Compreender a estrutura e a sintaxe de modelos Azure Resource Manager

Este artigo descreve a estrutura de um modelo Azure Resource Manager. Ela apresenta as diferentes secções de um modelo e as propriedades que estão disponíveis dessas secções. O modelo é constituído por JSON e expressões que pode utilizar para construir valores para a sua implementação.

Este artigo destina-se a utilizadores que têm um pouco familiarizado com modelos do Resource Manager. Ele fornece informações detalhadas sobre a estrutura e a sintaxe do modelo. Se pretender uma introdução à criação de um modelo, veja [criar o primeiro modelo do Azure Resource Manager](resource-manager-create-first-template.md).

## <a name="template-format"></a>Formato de modelo

Na sua estrutura mais simples, um modelo tem os seguintes elementos:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  },
    "variables": {  },
    "functions": [  ],
    "resources": [  ],
    "outputs": {  }
}
```

| Nome do elemento | Necessário | Descrição |
|:--- |:--- |:--- |
| $schema |Sim |Localização do ficheiro de esquema JSON que descreve a versão da linguagem do modelo.<br><br> Para implementações do grupo de recursos, utilize: `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Para implementações de subscrição, utilize: `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |Sim |Versão do modelo (por exemplo, 1.0.0.0). Pode fornecer qualquer valor para este elemento. Utilize este valor para documentar alterações significativas no seu modelo. Ao implementar recursos com o modelo, este valor pode ser usado para se certificar de que o modelo certo está a ser utilizado. |
| parâmetros |Não |Valores que são fornecidos quando a implementação é executada para personalizar a implementação de recursos. |
| Variáveis |Não |Valores que são utilizados como fragmentos JSON no modelo para simplificar as expressões de linguagem de modelo. |
| functions |Não |Funções definidas pelo utilizador que estão disponíveis dentro do modelo. |
| recursos |Sim |Tipos de recursos que são implementados ou atualizados num grupo de recursos ou subscrição. |
| saídas |Não |Valores que são devolvidos após a implementação. |

Cada elemento tem propriedades que pode definir. O exemplo seguinte mostra a sintaxe completa para um modelo:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  
        "<parameter-name>" : {
            "type" : "<type-of-parameter-value>",
            "defaultValue": "<default-value-of-parameter>",
            "allowedValues": [ "<array-of-allowed-values>" ],
            "minValue": <minimum-value-for-int>,
            "maxValue": <maximum-value-for-int>,
            "minLength": <minimum-length-for-string-or-array>,
            "maxLength": <maximum-length-for-string-or-array-parameters>,
            "metadata": {
                "description": "<description-of-the parameter>" 
            }
        }
    },
    "variables": {
        "<variable-name>": "<variable-value>",
        "<variable-object-name>": {
            <variable-complex-type-value>
        },
        "<variable-object-name>": {
            "copy": [
                {
                    "name": "<name-of-array-property>",
                    "count": <number-of-iterations>,
                    "input": <object-or-value-to-repeat>
                }
            ]
        },
        "copy": [
            {
                "name": "<variable-array-name>",
                "count": <number-of-iterations>,
                "input": <object-or-value-to-repeat>
            }
        ]
    },
    "functions": [
      {
        "namespace": "<namespace-for-your-function>",
        "members": {
          "<function-name>": {
            "parameters": [
              {
                "name": "<parameter-name>",
                "type": "<type-of-parameter-value>"
              }
            ],
            "output": {
              "type": "<type-of-output-value>",
              "value": "<function-expression>"
            }
          }
        }
      }
    ],
    "resources": [
      {
          "condition": "<boolean-value-whether-to-deploy>",
          "apiVersion": "<api-version-of-resource>",
          "type": "<resource-provider-namespace/resource-type-name>",
          "name": "<name-of-the-resource>",
          "location": "<location-of-resource>",
          "tags": {
              "<tag-name1>": "<tag-value1>",
              "<tag-name2>": "<tag-value2>"
          },
          "comments": "<your-reference-notes>",
          "copy": {
              "name": "<name-of-copy-loop>",
              "count": "<number-of-iterations>",
              "mode": "<serial-or-parallel>",
              "batchSize": "<number-to-deploy-serially>"
          },
          "dependsOn": [
              "<array-of-related-resource-names>"
          ],
          "properties": {
              "<settings-for-the-resource>",
              "copy": [
                  {
                      "name": ,
                      "count": ,
                      "input": {}
                  }
              ]
          },
          "resources": [
              "<array-of-child-resources>"
          ]
      }
    ],
    "outputs": {
        "<outputName>" : {
            "condition": "<boolean-value-whether-to-output-value>",
            "type" : "<type-of-output-value>",
            "value": "<output-value-expression>"
        }
    }
}
```

Este artigo descreve as seções do modelo em mais detalhes.

## <a name="syntax"></a>Sintaxe

A sintaxe básica do modelo é um JSON. No entanto, expressões e funções estendem os valores JSON disponíveis dentro do modelo.  Expressões são escritas dentro de literais de cadeia de caracteres do JSON cujo primeiro e último carateres são os colchetes: `[` e `]`, respectivamente. O valor da expressão é avaliado quando o modelo é implementado. Embora escrito como um literal de cadeia, o resultado da avaliação da expressão pode ser de um tipo diferente do JSON, como uma matriz ou um número inteiro, consoante a expressão real.  Ter uma cadeia literal, comece com um colchete `[`, mas não o tiver interpretada como uma expressão, adicione um parêntesis extra para iniciar a cadeia de caracteres com `[[`.

Normalmente, usar expressões com as funções para executar operações para configurar a implantação. Da mesma forma que no JavaScript, chamadas de função são formatadas como `functionName(arg1,arg2,arg3)`. Referenciar propriedades utilizando os operadores de pontos e [Índice].

O exemplo seguinte mostra como utilizar várias funções ao construir um valor:

```json
"variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
}
```

Para obter a lista completa de funções de modelo, consulte [funções de modelo do Azure Resource Manager](resource-group-template-functions.md). 

## <a name="parameters"></a>Parâmetros

Na secção de parâmetros do modelo, especifique os valores que pode inserir ao implementar os recursos. Estes valores de parâmetros permitem-lhe personalizar a implementação, fornecendo valores que são adaptadas para um ambiente específico (por exemplo, desenvolvimento, teste e produção). Não é necessário fornecer os parâmetros no seu modelo, mas sem parâmetros seu modelo implementaria sempre os mesmos recursos com os mesmos nomes, locais e propriedades.

O exemplo seguinte mostra uma definição de parâmetro simples:

```json
"parameters": {
  "siteNamePrefix": {
    "type": "string",
    "metadata": {
      "description": "The name prefix of the web app that you wish to create."
    }
  },
},
```

Para obter informações sobre como definir parâmetros, consulte [secção de parâmetros de modelos Azure Resource Manager](resource-manager-templates-parameters.md).

## <a name="variables"></a>Variáveis

Na secção de variáveis, é possível construir valores que podem ser utilizados em todo o seu modelo. Não precisa de definir variáveis, mas elas, muitas vezes, simplificam seu modelo, reduzindo as expressões complexas.

### <a name="available-definitions"></a>Definições disponíveis

O exemplo seguinte mostra as opções disponíveis para definir uma variável:

```json
"variables": {
    "<variable-name>": "<variable-value>",
    "<variable-name>": { 
        <variable-complex-type-value> 
    },
    "<variable-object-name>": {
        "copy": [
            {
                "name": "<name-of-array-property>",
                "count": <number-of-iterations>,
                "input": <object-or-value-to-repeat>
            }
        ]
    },
    "copy": [
        {
            "name": "<variable-array-name>",
            "count": <number-of-iterations>,
            "input": <object-or-value-to-repeat>
        }
    ]
}
```

Para obter informações sobre como utilizar `copy` para criar vários valores para uma variável, consulte [iteração variável](resource-group-create-multiple.md#variable-iteration).

### <a name="define-and-use-a-variable"></a>Definir e usar uma variável

O exemplo seguinte mostra uma definição de variável. Ele cria um valor de cadeia de caracteres para um nome de conta de armazenamento. Ele utiliza várias funções de modelo para obter um valor de parâmetro e concatena-lo a uma cadeia exclusiva.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Utilize a variável quando definir o recurso.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

### <a name="configuration-variables"></a>Variáveis de configuração

Pode utilizar tipos complexos de JSON para definir valores relacionados para um ambiente.

```json
"variables": {
    "environmentSettings": {
        "test": {
            "instanceSize": "Small",
            "instanceCount": 1
        },
        "prod": {
            "instanceSize": "Large",
            "instanceCount": 4
        }
    }
},
```

Parâmetros, vai criar um valor que indica que configuração valores devem ser usados.

```json
"parameters": {
    "environmentName": {
        "type": "string",
        "allowedValues": [
          "test",
          "prod"
        ]
    }
},
```

Obter as definições atuais com:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

### <a name="variables-example-templates"></a>Modelos de exemplo de variáveis

Estes modelos de exemplo demonstram alguns cenários de utilização de variáveis. Implementá-las para testar como as variáveis são tratadas em cenários diferentes. 

|Modelo  |Descrição  |
|---------|---------|
| [definições de variável](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Demonstra os diferentes tipos de variáveis. O modelo implementa todos os recursos. Ele constrói os valores das variáveis e retorna esses valores. |
| [variável de configuração](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Demonstra o uso de uma variável que define os valores de configuração. O modelo implementa todos os recursos. Ele constrói os valores das variáveis e retorna esses valores. |
| [regras de segurança de rede](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) e [ficheiro de parâmetros](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Constrói uma matriz no formato correto para a atribuição de regras de segurança a um grupo de segurança de rede. |


## <a name="functions"></a>Funções

No seu modelo, pode criar suas próprias funções. Estas funções estão disponíveis para utilização no seu modelo. Normalmente, é possível definir a expressão complicada que não pretende repetir em todo o seu modelo. Criar as funções definidas pelo utilizador a partir de expressões e [funções](resource-group-template-functions.md) que são suportadas nos modelos.

Ao definir uma função de utilizador, existem algumas restrições:

* A função não é possível aceder a variáveis.
* A função só pode utilizar os parâmetros definidos na função. Quando utiliza a [função de parâmetros](resource-group-template-functions-deployment.md#parameters) dentro de uma função definida pelo usuário, está restrito para os parâmetros para essa função.
* A função não é possível chamar outras funções definidas pelo utilizador.
* A função não é possível utilizar o [fazem referência função](resource-group-template-functions-resource.md#reference).
* Parâmetros para a função não podem ter valores predefinidos.

As suas funções exigem um valor de espaço de nomes para evitar conflitos com as funções do modelo de nomenclatura. O exemplo seguinte mostra uma função que retorna um nome de conta de armazenamento:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

Chamar a função com:

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="resources"></a>Recursos
A seção de recursos, vai definir os recursos que são implementados ou atualizados. Esta secção pode são complicada, uma vez que deve compreender os tipos que estiver a implementar para fornecer os valores de certos.

```json
"resources": [
  {
    "apiVersion": "2016-08-01",
    "name": "[variables('webSiteName')]",
    "type": "Microsoft.Web/sites",
    "location": "[resourceGroup().location]",
    "properties": {
      "serverFarmId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Web/serverFarms/<plan-name>"
    }
  }
],
```

Para condicionalmente incluir ou excluir um recurso durante a implementação, utilize o [elemento Condition](resource-manager-templates-resources.md#condition). Para obter mais informações sobre a seção de recursos, consulte [seção de recursos de modelos Azure Resource Manager](resource-manager-templates-resources.md).

## <a name="outputs"></a>Saídas

Na secção de saídas, especifique os valores que são devolvidos da implementação. Normalmente, retornar valores de recursos que foram implementados.

### <a name="available-properties"></a>Propriedades disponíveis

O exemplo seguinte mostra a estrutura de uma definição de saída:

```json
"outputs": {
    "<outputName>" : {
        "condition": "<boolean-value-whether-to-output-value>",
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Nome do elemento | Necessário | Descrição |
|:--- |:--- |:--- |
| outputName |Sim |Nome do valor de saída. Tem de ser um identificador de JavaScript válido. |
| condition |Não | É devolvido o valor booleano que indica se este valor de saída. Quando `true`, o valor está incluído na saída para a implementação. Quando `false`, o valor de saída é ignorado para esta implementação. Quando não especificado, o valor predefinido é `true`. |
| tipo |Sim |Tipo do valor de saída. Valores de saída suportam os mesmos tipos de parâmetros de entrada de modelo. |
| valor |Sim |Expressão de linguagem de modelo que é avaliada e devolvida como valor de saída. |

### <a name="define-and-use-output-values"></a>Definir e utilizar valores de saída

O exemplo seguinte mostra como devolver o ID de recurso para um endereço IP público:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

O exemplo seguinte mostra como devolver condicionalmente o ID de recurso para um endereço IP público com base em se um foi implementado uma nova:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Para obter um exemplo simple de saída condicional, veja [modelo de saída condicional](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

Após a implementação, pode recuperar o valor com o script. Para o PowerShell, utilize:

```powershell
(Get-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Para a CLI do Azure, utilize:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Pode obter o valor de saída a partir de um modelo ligado utilizando a [referência](resource-group-template-functions-resource.md#reference) função. Para obter um valor de saída a partir de um modelo ligado, obter o valor da propriedade com a sintaxe, como: `"[reference('deploymentName').outputs.propertyName.value]"`.

Ao obter uma propriedade de saída a partir de um modelo ligado, o nome da propriedade não pode incluir um traço.

O exemplo seguinte mostra como definir o endereço IP num Balanceador de carga ao obter um valor a partir de um modelo ligado.

```json
"publicIPAddress": {
    "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Não é possível utilizar o `reference` função na secção de saídas de um [modelo aninhado](resource-group-linked-templates.md#link-or-nest-a-template). Para devolver os valores para um recurso implementado num modelo aninhado, converta seu modelo aninhado para um modelo ligado.

### <a name="output-example-templates"></a>Modelos de exemplo de saída

|Modelo  |Descrição  |
|---------|---------|
|[Copie as variáveis](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Cria variáveis complexo e produz esses valores. Não implemente todos os recursos. |
|[Endereço IP público](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Cria um endereço IP público e devolve o ID de recurso. |
|[Balanceador de carga](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Ligações para o modelo anterior. Utiliza o ID de recurso na saída, ao criar o Balanceador de carga. |


<a id="comments" />

## <a name="comments-and-metadata"></a>Comentários e metadados

Tem algumas opções para adicionar comentários e os metadados ao seu modelo.

Pode adicionar um `metadata` objeto praticamente qualquer lugar no seu modelo. Gestor de recursos ignora o objeto, mas o seu editor de JSON pode avisá-lo que a propriedade não é válida. O objeto, defina as propriedades que precisa.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "comments": "This template was developed for demonstration purposes.",
        "author": "Example Name"
    },
```

Para **parâmetros**, adicione um `metadata` objeto com um `description` propriedade.

```json
"parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "User name for the Virtual Machine."
      }
    },
```

Ao implementar o modelo através do portal, o texto que fornecer na descrição do é automaticamente utilizado como uma dica para esse parâmetro.

![Mostrar a sugestão de parâmetro](./media/resource-group-authoring-templates/show-parameter-tip.png)

Para **recursos**, adicione um `comments` elemento ou um objeto de metadados. O exemplo seguinte mostra um elemento de comentários e um objeto de metadados.

```json
"resources": [
  {
    "comments": "Storage account used to store VM disks",
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

Para **produz**, adicionar um objeto de metadados para o valor de saída.

```json
"outputs": {
    "hostname": {
      "type": "string",
      "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
      "metadata": {
        "comments": "Return the fully qualified domain name"
      }
    },
```

Não é possível adicionar um objeto de metadados para as funções definidas pelo utilizador.

Para comentários embutidos, pode usar `//` mas essa sintaxe não funciona com todas as ferramentas. Não é possível utilizar a CLI do Azure para implementar o modelo com comentários embutidos. E, não é possível utilizar o editor de modelos de portal para trabalhar em modelos com comentários embutidos. Se adicionar esse estilo de comentário, certifique-se as ferramentas que utiliza o suporte inline JSON comentários.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  "dependsOn": [ // storage account and network interface must be deployed first
      "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
      "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

No VS Code, pode definir o modo de idioma para JSON com comentários. Os comentários de inline já não estão marcados como inválidos. Para alterar o modo:

1. Abra a seleção do modo de idioma (Ctrl + K M)

1. Selecione **JSON com comentários**.

   ![Selecione o modo de idioma](./media/resource-group-authoring-templates/select-json-comments.png)

## <a name="template-limits"></a>Limites do modelo

Limite o tamanho do seu modelo para 1 MB e cada ficheiro de parâmetros a 64 KB. O limite de 1 MB aplica-se para o estado final do modelo depois foi expandido com definições de recursos iterativo e valores de variáveis e parâmetros. 

Também está limitado a:

* 256 parâmetros
* 256 variáveis
* 800 recursos (incluindo o número de cópias)
* 64 valores de saída
* 24,576 caracteres numa expressão de modelo

Pode exceder alguns limites de modelo ao utilizar um modelo aninhado. Para obter mais informações, consulte [utilizar modelos ligados durante a implantação de recursos do Azure](resource-group-linked-templates.md). Para reduzir o número de parâmetros, variáveis ou saídas, pode combinar diversos valores num objeto. Para obter mais informações, consulte [objetos como parâmetros](resource-manager-objects-as-parameters.md).

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>Passos Seguintes
* Para ver modelos completos para vários tipos de soluções, veja os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).
* Para obter detalhes sobre as funções que pode utilizar a partir de dentro de um modelo, consulte [funções de modelo do Azure Resource Manager](resource-group-template-functions.md).
* Para combinar vários modelos durante a implementação, consulte [utilizar modelos ligados com o Azure Resource Manager](resource-group-linked-templates.md).
* Para obter recomendações sobre a criação de modelos, veja [práticas recomendadas do modelo do Azure Resource Manager](template-best-practices.md).
* Para obter recomendações sobre como criar modelos do Resource Manager que pode ser usado em todos os ambientes do Azure e Azure Stack, veja [modelos de desenvolver o Azure Resource Manager para manter a consistência na cloud](templates-cloud-consistency.md).
