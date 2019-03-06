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
ms.date: 03/04/2019
ms.author: tomfitz
ms.openlocfilehash: f67741417c6d31c4adf1d063aac3bd3ccc310fde
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57440255"
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
  "apiProfile": "",
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
| apiProfile |Não | Uma versão de API que funciona como uma coleção de versões de API para tipos de recursos. Utilize este valor para evitar ter de especificar as versões de API para cada recurso no modelo. Quando especificar uma versão de perfil de API e não especificar uma versão de API para o tipo de recurso, o Resource Manager utiliza a versão de API do perfil para esse tipo de recurso. Para obter mais informações, consulte [controlar versões através de perfis de API](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parameters](#parameters) |Não |Valores que são fornecidos quando a implementação é executada para personalizar a implementação de recursos. |
| [Variáveis](#variables) |Não |Valores que são utilizados como fragmentos JSON no modelo para simplificar as expressões de linguagem de modelo. |
| [functions](#functions) |Não |Funções definidas pelo utilizador que estão disponíveis dentro do modelo. |
| [resources](#resources) |Sim |Tipos de recursos que são implementados ou atualizados num grupo de recursos ou subscrição. |
| [outputs](#outputs) |Não |Valores que são devolvidos após a implementação. |

Cada elemento tem propriedades que pode definir. Este artigo descreve as seções do modelo em mais detalhes.

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

Está limitado a 256 parâmetros num modelo. Pode reduzir o número de parâmetros com objetos que contêm várias propriedades, conforme mostrado neste artigo.

### <a name="available-properties"></a>Propriedades disponíveis

As propriedades disponíveis para um parâmetro são:

```json
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
}
```

| Nome do elemento | Necessário | Descrição |
|:--- |:--- |:--- |
| parameterName |Sim |Nome do parâmetro. Tem de ser um identificador de JavaScript válido. |
| tipo |Sim |Tipo do valor de parâmetro. Os tipos permitidos e os valores são **cadeia de caracteres**, **securestring**, **int**, **bool**, **objeto**, **secureObject**, e **matriz**. |
| defaultValue |Não |Valor predefinido para o parâmetro, se não for fornecido nenhum valor para o parâmetro. |
| allowedValues |Não |Matriz de valores permitidos para o parâmetro para se certificar de que o valor correto é fornecido. |
| minValue |Não |O valor mínimo para os parâmetros de tipo int, este valor é inclusivo. |
| maxValue |Não |O valor máximo para os parâmetros de tipo int, este valor é inclusivo. |
| minLength |Não |O comprimento mínimo para a cadeia, cadeia segura e parâmetros de tipo de matriz, este valor é inclusivo. |
| maxLength |Não |O comprimento máximo para a cadeia, cadeia segura e parâmetros de tipo de matriz, este valor é inclusivo. |
| descrição |Não |Descrição do parâmetro que é apresentado aos utilizadores através do portal. Para obter mais informações, consulte [comentários em modelos](#comments). |

### <a name="define-and-use-a-parameter"></a>Definir e utilizar um parâmetro

O exemplo seguinte mostra uma definição de parâmetro simples. Ele define o nome do parâmetro e especifica que demora um valor de cadeia de caracteres. O parâmetro só aceita valores que façam sentido para seu uso pretendido. Especifica um valor predefinido quando é fornecido nenhum valor durante a implementação. Por fim, o parâmetro inclui uma descrição de seu uso.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }   
}
```

No modelo, referenciar o valor para o parâmetro com a seguinte sintaxe:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

### <a name="template-functions-with-parameters"></a>Funções de modelo com parâmetros

Ao especificar o valor predefinido para um parâmetro, pode utilizar a maioria das funções de modelo. Pode utilizar outro valor de parâmetro para criar um valor predefinido. O modelo seguinte demonstra o uso de funções no valor predefinido:

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

Não é possível utilizar o `reference` função na secção de parâmetros. Parâmetros são avaliados antes da implantação até a `reference` função não é possível obter o estado de tempo de execução de um recurso. 

### <a name="objects-as-parameters"></a>Objetos como parâmetros

Pode ser mais fácil organizar os valores relacionados, passando-os na como um objeto. Essa abordagem também reduz o número de parâmetros no modelo.

Defina o parâmetro no seu modelo e especifique um objeto JSON em vez de um único valor durante a implementação. 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

Em seguida, referenciar subproperties do parâmetro ao utilizar o operador de ponto.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

### <a name="parameter-example-templates"></a>Modelos de exemplo de parâmetro

Estes modelos de exemplo demonstram alguns cenários de utilização de parâmetros. Implementá-las para testar como os parâmetros são tratados em cenários diferentes.

|Modelo  |Descrição  |
|---------|---------|
|[parâmetros com funções para os valores predefinidos](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Demonstra como utilizar funções de modelo ao definir os valores predefinidos de parâmetros. O modelo implementa todos os recursos. Ele constrói os valores de parâmetros e retorna esses valores. |
|[Objeto de parâmetro](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Demonstra como utilizar um objeto para um parâmetro. O modelo implementa todos os recursos. Ele constrói os valores de parâmetros e retorna esses valores. |

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

### <a name="variable-example-templates"></a>Modelos de exemplo de variável

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
A seção de recursos, vai definir os recursos que são implementados ou atualizados.

### <a name="available-properties"></a>Propriedades disponíveis

Define os recursos com a seguinte estrutura:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
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
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
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
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Nome do elemento | Necessário | Descrição |
|:--- |:--- |:--- |
| condition | Não | Valor booleano que indica se o recurso será provisionado durante esta implementação. Quando `true`, o recurso é criado durante a implementação. Quando `false`, o recurso é ignorado para esta implementação. |
| apiVersion |Sim |Versão da API REST para utilizar para criar o recurso. Para determinar os valores disponíveis, veja [referência de modelo](/azure/templates/). |
| tipo |Sim |Tipo de recurso. Este valor é uma combinação do espaço de nomes do fornecedor de recursos e o tipo de recurso (por exemplo, **Storage/storageaccounts**). Para determinar os valores disponíveis, veja [referência de modelo](/azure/templates/). |
| nome |Sim |Nome do recurso. O nome tem de seguir restrições de componente URI definidas na RFC3986. Além disso, os serviços do Azure que expõem o nome do recurso fora partes validar o nome para garantir que ela não é uma tentativa para falsificar a identidade de outra. |
| localização |Varia |Georreplicação-localizações suportadas do recurso fornecido. Pode selecionar qualquer uma das localizações disponíveis, mas geralmente faz sentido escolher um que está perto dos seus utilizadores. Normalmente, também faz sentido colocar recursos que interagem entre si na mesma região. A maioria dos tipos de recursos exigem uma localização, mas alguns tipos (por exemplo, uma atribuição de função) não precisam de uma localização. |
| etiquetas |Não |Etiquetas de associado ao recurso. Aplica etiquetas para organizar logicamente os recursos na sua subscrição. |
| comentários |Não |Suas anotações para documentar os recursos no seu modelo. Para obter mais informações, consulte [comentários em modelos](resource-group-authoring-templates.md#comments). |
| copiar |Não |Se for necessário mais de uma instância, o número de recursos para criar. O modo predefinido é paralelo. Especifique o modo serial quando não quiser que todos os ou os recursos necessários para implementar ao mesmo tempo. Para obter mais informações, consulte [criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Não |Recursos que devem ser implementados para que este recurso está implementado. Resource Manager avalia as dependências entre os recursos e implementa-as na ordem correta. Quando os recursos não são dependentes entre si, serem implementadas em paralelo. O valor pode ser uma lista separada por vírgulas de um recurso nomes ou identificadores exclusivos de recursos. Lista apenas os recursos que são implementados neste modelo. Recursos que não sejam definidos neste modelo tem de existir. Evite a adição de dependências desnecessárias como podem atrasar a implantação e criar dependências circulares. Para obter orientações sobre as dependências de definição, consulte [definir dependências nos modelos Azure Resource Manager](resource-group-define-dependencies.md). |
| propriedades |Não |Definições de configuração de recursos específicos. Os valores para as propriedades são os mesmos que os valores que fornecer no corpo do pedido para a operação de REST API (método PUT) criar o recurso. Também pode especificar uma matriz de cópia para criar várias instâncias de uma propriedade. Para determinar os valores disponíveis, veja [referência de modelo](/azure/templates/). |
| sku | Não | Alguns recursos permitem que os valores que definem o SKU para implementar. Por exemplo, pode especificar o tipo de redundância para uma conta de armazenamento. |
| tipo | Não | Alguns recursos permitem que um valor que define o tipo de recurso que implementa. Por exemplo, pode especificar o tipo do Cosmos DB para criar. |
| plano | Não | Alguns recursos permitem que os valores que definem o plano de implementar. Por exemplo, pode especificar a imagem do marketplace para uma máquina virtual. | 
| recursos |Não |Recursos de subordinados que dependem do recurso que está a ser definido. Fornece apenas os tipos de recursos que são permitidos pelo esquema do recurso principal. O tipo totalmente qualificado do recurso subordinado inclui o tipo de recurso principal, como **Microsoft.Web/sites/extensions**. Dependência do recurso principal não está implícita. Tem de definir explicitamente essa dependência. |

### <a name="condition"></a>Condição

Quando tem de decidir durante a implementação se deve ou não criar um recurso, utilize o `condition` elemento. O valor para este elemento é resolvido para true ou false. Quando o valor for VERDADEIRO, o recurso é criado. Quando o valor for FALSO, o recurso não é criado. O valor só pode ser aplicado a todo o recurso.

Normalmente, utiliza este valor quando pretender criar um novo recurso ou utilize um já existente. Por exemplo, para especificar se é implementada uma nova conta de armazenamento ou uma conta de armazenamento existente, utilize:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Para um modelo de exemplo completo que utilize o `condition` elemento, consulte [VM com uma rede Virtual nova ou existente, o armazenamento e o IP público](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

### <a name="resource-names"></a>Nomes de recursos

Em geral, trabalha com três tipos de nomes de recursos no Gestor de recursos:

* Nomes de recursos tem de ser exclusivos.
* Os nomes de recursos que não são necessárias para que seja exclusivo, mas optar por fornecer um nome que pode ajudá-lo a identificar o recurso.
* Nomes de recursos que podem ser genéricos.

Fornecer um **nome de recurso exclusiva** para qualquer tipo de recurso que tem um ponto de final de acesso de dados. Alguns tipos de recursos comuns que necessitam de um nome exclusivo incluem:

* O armazenamento do Azure<sup>1</sup> 
* Funcionalidade de Aplicações Web no Serviço de Aplicações do Azure
* SQL Server
* Azure Key Vault
* Cache do Azure para Redis
* Azure Batch
* Traffic Manager do Azure
* Azure Search
* Azure HDInsight

<sup>1</sup> nomes de conta de armazenamento também tem de estar em minúsculas, 24 carateres ou menos, e não tem qualquer hífenes.

Ao definir o nome, pode manualmente criar um nome exclusivo ou utilizar o [uniqueString()](resource-group-template-functions-string.md#uniquestring) função para gerar um nome. Pode também querer adicionar um prefixo ou sufixo para a **uniqueString** resultado. Modificar o nome exclusivo pode ajudá-lo a mais fácil identificar o tipo de recurso do nome. Por exemplo, pode gerar um nome exclusivo para uma conta de armazenamento ao utilizar a seguinte variável:

```json
"variables": {
  "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

Para alguns tipos de recursos, pode querer fornecer uma **nome para a identificação**, mas o nome não tem de ser exclusivo. Para estes tipos de recursos, forneça um nome que a descreve ou características de utilização.

```json
"parameters": {
  "vmName": { 
    "type": "string",
    "defaultValue": "demoLinuxVM",
    "metadata": {
      "description": "The name of the VM to create."
    }
  }
}
```

Para tipos de recursos que principalmente acesso por meio de um recurso diferente, pode utilizar um **nome genérico** que está hard-coded no modelo. Por exemplo, pode definir um nome padrão, genérico para regras de firewall num SQL server:

```json
{
  "type": "firewallrules",
  "name": "AllowAllWindowsAzureIps",
  ...
}
```

### <a name="resource-location"></a>Localização do recurso

Quando implementar um modelo, tem de fornecer uma localização de cada recurso. Diferentes tipos de recursos são suportados em localizações diferentes. Para obter as localizações suportadas para um tipo de recurso, consulte [fornecedores de recursos do Azure e tipos de](resource-manager-supported-services.md).

Utilizar um parâmetro para especificar a localização para os recursos e defina o valor predefinido `resourceGroup().location`.

O exemplo seguinte mostra uma conta de armazenamento que é implementada para uma localização especificada como um parâmetro:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

### <a name="child-resources"></a>Recursos subordinados

Dentro de alguns tipos de recursos, também pode definir uma matriz de recursos subordinados. Recursos subordinados são recursos que existem apenas dentro do contexto de outro recurso. Por exemplo, uma base de dados SQL não pode existir sem um SQL server para a base de dados é um filho do servidor. Pode definir a base de dados dentro da definição do servidor.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

Quando forem aninhadas, o tipo está definido como `databases` mas o tipo de recurso completo é `Microsoft.Sql/servers/databases`. Não fornecer `Microsoft.Sql/servers/` porque é suposto o pai do tipo de recurso. O nome do recurso subordinado está definido como `exampledatabase` , mas o nome completo inclui o nome do principal. Não fornecer `exampleserver` porque é suposto do recurso principal.

O formato do tipo de recurso subordinado é: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

O formato do nome do recurso subordinado é: `{parent-resource-name}/{child-resource-name}`

No entanto, não precisa de definir a base de dados no servidor. Pode definir os recursos filho no nível superior. Pode usar essa abordagem se o recurso de principal não está implementado no mesmo modelo, ou se pretende utilizar `copy` para criar mais do que um recurso de subordinados. Com esta abordagem, tem de fornecer o tipo de recurso completo e incluir o nome do recurso principal no nome do recurso subordinado.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

Ao construir uma referência completamente qualificada para um recurso, a ordem para combinar os segmentos do tipo e o nome não é simplesmente uma concatenação das duas. Em vez disso, depois do espaço de nomes, utilize uma seqüência de *nome do tipo* pares de menos específicos para a mais específica:

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Por exemplo:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` está correto `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` não está correto

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

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>Passos Seguintes
* Para ver modelos completos para vários tipos de soluções, veja os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).
* Para obter detalhes sobre as funções que pode utilizar a partir de dentro de um modelo, consulte [funções de modelo do Azure Resource Manager](resource-group-template-functions.md).
* Para combinar vários modelos durante a implementação, consulte [utilizar modelos ligados com o Azure Resource Manager](resource-group-linked-templates.md).
* Para obter recomendações sobre a criação de modelos, veja [práticas recomendadas do modelo do Azure Resource Manager](template-best-practices.md).
* Para obter recomendações sobre como criar modelos do Resource Manager que pode ser usado em todos os ambientes do Azure e Azure Stack, veja [modelos de desenvolver o Azure Resource Manager para manter a consistência na cloud](templates-cloud-consistency.md).
