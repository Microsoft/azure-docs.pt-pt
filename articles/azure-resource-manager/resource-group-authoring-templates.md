---
title: Estrutura e sintaxe do modelo de Azure Resource Manager | Microsoft Docs
description: Descreve a estrutura e as propriedades de modelos de Azure Resource Manager usando a sintaxe JSON declarativa.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: tomfitz
ms.openlocfilehash: 53b2f9783b33c859ca2c5de5f35353b8482ea5c7
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70275126"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Entender a estrutura e a sintaxe de modelos de Azure Resource Manager

Este artigo descreve a estrutura de um modelo de Azure Resource Manager. Ele apresenta as diferentes seções de um modelo e as propriedades que estão disponíveis nessas seções. O modelo consiste em JSON e expressões que você pode usar para construir valores para sua implantação.

Este artigo destina-se a usuários que têm alguma familiaridade com modelos do Resource Manager. Ele fornece informações detalhadas sobre a estrutura e a sintaxe do modelo. Se você quiser uma introdução à criação de um modelo, consulte [criar seu primeiro modelo de Azure Resource Manager](resource-manager-create-first-template.md).

## <a name="template-format"></a>Formato do modelo

Em sua estrutura mais simples, um modelo tem os seguintes elementos:

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
| $schema |Sim |Local do arquivo de esquema JSON que descreve a versão do idioma do modelo.<br><br> Para implantações de grupo de recursos, use:`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Para implantações de assinatura, use:`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |Sim |Versão do modelo (como 1.0.0.0). Você pode fornecer qualquer valor para este elemento. Use esse valor para documentar alterações significativas em seu modelo. Ao implantar recursos usando o modelo, esse valor pode ser usado para garantir que o modelo correto esteja sendo usado. |
| apiProfile |Não | Uma versão de API que serve como uma coleção de versões de API para tipos de recursos. Use esse valor para evitar a especificação de versões de API para cada recurso no modelo. Quando você especifica uma versão de perfil de API e não especifica uma versão de API para o tipo de recurso, o Resource Manager usa a versão de API para esse tipo de recurso que é definido no perfil.<br><br>A propriedade de perfil de API é especialmente útil ao implantar um modelo em ambientes diferentes, como Azure Stack e o Azure global. Use a versão do perfil de API para certificar-se de que o modelo usa automaticamente as versões com suporte em ambos os ambientes. Para obter uma lista das versões de perfil de API atuais e as versões de API de recursos definidas no perfil, consulte [perfil de API](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Para obter mais informações, consulte [rastrear versões usando perfis de API](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parameters](#parameters) |Não |Valores que são fornecidos quando a implantação é executada para personalizar a implantação de recursos. |
| [variables](#variables) |Não |Valores que são usados como fragmentos JSON no modelo para simplificar as expressões de linguagem do modelo. |
| [funções](#functions) |Não |Funções definidas pelo usuário que estão disponíveis no modelo. |
| [os](#resources) |Sim |Tipos de recursos que são implantados ou atualizados em um grupo de recursos ou assinatura. |
| [produz](#outputs) |Não |Valores retornados após a implantação. |

Cada elemento tem propriedades que você pode definir. Este artigo descreve as seções do modelo com mais detalhes.

## <a name="parameters"></a>Parâmetros

Na seção de parâmetros do modelo, você especifica quais valores você pode inserir ao implantar os recursos. Esses valores de parâmetro permitem que você personalize a implantação fornecendo valores que são personalizados para um determinado ambiente (como desenvolvimento, teste e produção). Você não precisa fornecer parâmetros em seu modelo, mas sem parâmetros seu modelo sempre implantaria os mesmos recursos com os mesmos nomes, locais e propriedades.

Você está limitado a 256 parâmetros em um modelo. Você pode reduzir o número de parâmetros usando objetos que contêm várias propriedades, conforme mostrado neste artigo.

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
| type |Sim |Tipo do valor do parâmetro. Os tipos e valores permitidos são **String**, **SecureString**, **int**, **bool**, **Object**, **secureobject**e **array**. |
| defaultValue |Não |Valor padrão para o parâmetro, se nenhum valor for fornecido para o parâmetro. |
| allowedValues |Não |Matriz de valores permitidos para o parâmetro para garantir que o valor correto seja fornecido. |
| minValue |Não |O valor mínimo para parâmetros de tipo int, esse valor é inclusivo. |
| maxValue |Não |O valor máximo para parâmetros de tipo int, esse valor é inclusivo. |
| minLength |Não |O comprimento mínimo para parâmetros de cadeia de caracteres, Cadeia de caracteres segura e tipo de matriz, esse valor é inclusivo. |
| maxLength |Não |O comprimento máximo para parâmetros de cadeia de caracteres, Cadeia de caracteres segura e tipo de matriz, esse valor é inclusivo. |
| description |Não |Descrição do parâmetro que é exibido aos usuários por meio do Portal. Para obter mais informações, consulte [comentários em modelos](#comments). |

### <a name="define-and-use-a-parameter"></a>Definir e usar um parâmetro

O exemplo a seguir mostra uma definição de parâmetro simples. Ele define o nome do parâmetro e especifica que ele usa um valor de cadeia de caracteres. O parâmetro aceita somente valores que fazem sentido para seu uso pretendido. Ele especifica um valor padrão quando nenhum valor é fornecido durante a implantação. Por fim, o parâmetro inclui uma descrição de seu uso.

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

No modelo, você faz referência ao valor do parâmetro com a seguinte sintaxe:

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

Ao especificar o valor padrão para um parâmetro, você pode usar a maioria das funções de modelo. Você pode usar outro valor de parâmetro para criar um valor padrão. O modelo a seguir demonstra o uso de funções no valor padrão:

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

Não é possível usar `reference` a função na seção parâmetros. Os parâmetros são avaliados antes da `reference` implantação para que a função não possa obter o estado de tempo de execução de um recurso. 

### <a name="objects-as-parameters"></a>Objetos como parâmetros

Pode ser mais fácil organizar valores relacionados passando-os como um objeto. Essa abordagem também reduz o número de parâmetros no modelo.

Defina o parâmetro em seu modelo e especifique um objeto JSON em vez de um único valor durante a implantação. 

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

Em seguida, referencie as subpropriedades do parâmetro usando o operador ponto.

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

Esses modelos de exemplo demonstram alguns cenários para o uso de parâmetros. Implante-os para testar como os parâmetros são tratados em cenários diferentes.

|Modelo  |Descrição  |
|---------|---------|
|[parâmetros com funções para valores padrão](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Demonstra como usar funções de modelo ao definir valores padrão para parâmetros. O modelo não implanta nenhum recurso. Ele constrói valores de parâmetro e retorna esses valores. |
|[objeto de parâmetro](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Demonstra o uso de um objeto para um parâmetro. O modelo não implanta nenhum recurso. Ele constrói valores de parâmetro e retorna esses valores. |

## <a name="variables"></a>Variáveis

Na seção variáveis, você constrói valores que podem ser usados em todo o modelo. Você não precisa definir variáveis, mas elas geralmente simplificam seu modelo, reduzindo expressões complexas.

### <a name="available-definitions"></a>Definições disponíveis

O exemplo a seguir mostra as opções disponíveis para definir uma variável:

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

Para obter informações sobre `copy` como usar o para criar vários valores para uma variável, consulte [Iteration Variable](resource-group-create-multiple.md#variable-iteration).

### <a name="define-and-use-a-variable"></a>Definir e usar uma variável

O exemplo a seguir mostra uma definição de variável. Ele cria um valor de cadeia de caracteres para um nome de conta de armazenamento. Ele usa várias funções de modelo para obter um valor de parâmetro e concatena-o a uma cadeia de caracteres exclusiva.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Você usa a variável ao definir o recurso.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

### <a name="configuration-variables"></a>Variáveis de configuração

Você pode usar tipos JSON complexos para definir valores relacionados para um ambiente.

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

Em parâmetros, você cria um valor que indica quais valores de configuração usar.

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

Você recupera as configurações atuais com:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

### <a name="variable-example-templates"></a>Modelos de exemplo variáveis

Esses modelos de exemplo demonstram alguns cenários para usar variáveis. Implante-os para testar como as variáveis são tratadas em cenários diferentes. 

|Modelo  |Descrição  |
|---------|---------|
| [definições de variáveis](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Demonstra os diferentes tipos de variáveis. O modelo não implanta nenhum recurso. Ele constrói valores de variáveis e retorna esses valores. |
| [variável de configuração](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Demonstra o uso de uma variável que define os valores de configuração. O modelo não implanta nenhum recurso. Ele constrói valores de variáveis e retorna esses valores. |
| [regras de segurança de rede](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) e [arquivo de parâmetro](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Constrói uma matriz no formato correto para atribuir regras de segurança a um grupo de segurança de rede. |


## <a name="functions"></a>Functions

Em seu modelo, você pode criar suas próprias funções. Essas funções estão disponíveis para uso em seu modelo. Normalmente, você define uma expressão complicada que não deseja repetir em todo o modelo. Você cria as funções definidas pelo usuário a partir de expressões e [funções](resource-group-template-functions.md) com suporte em modelos.

Ao definir uma função de usuário, há algumas restrições:

* A função não pode acessar variáveis.
* A função só pode usar parâmetros que são definidos na função. Quando você usa a [função](resource-group-template-functions-deployment.md#parameters) Parameters em uma função definida pelo usuário, você está restrito aos parâmetros para essa função.
* A função não pode chamar outras funções definidas pelo usuário.
* A função não pode usar a [função de referência](resource-group-template-functions-resource.md#reference).
* Os parâmetros da função não podem ter valores padrão.

Suas funções exigem um valor de namespace para evitar conflitos de nomenclatura com funções de modelo. O exemplo a seguir mostra uma função que retorna um nome de conta de armazenamento:

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

Você chama a função com:

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
Na seção de recursos, você define os recursos que são implantados ou atualizados.

### <a name="available-properties"></a>Propriedades disponíveis

Você define recursos com a seguinte estrutura:

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
| condition | Não | Valor booliano que indica se o recurso será provisionado durante essa implantação. Quando `true`, o recurso é criado durante a implantação. Quando `false`, o recurso é ignorado para essa implantação. Consulte [implantação condicional](conditional-resource-deployment.md). |
| apiVersion |Sim |Versão da API REST a ser usada para criar o recurso. Para determinar os valores disponíveis, consulte [referência de modelo](/azure/templates/). |
| type |Sim |Tipo do recurso. Esse valor é uma combinação do namespace do provedor de recursos e do tipo de recurso (como **Microsoft. Storage/storageAccounts**). Para determinar os valores disponíveis, consulte [referência de modelo](/azure/templates/). Para um recurso filho, o formato do tipo depende de se ele está aninhado dentro do recurso pai ou definido fora do recurso pai. Consulte [definir nome e tipo para recursos filho](child-resource-name-type.md). |
| name |Sim |Nome do recurso. O nome deve seguir as restrições de componente de URI definidas em RFC3986. Além disso, os serviços do Azure que expõem o nome do recurso para partes externas validam o nome para verificar se não há uma tentativa de falsificar outra identidade. Para um recurso filho, o formato do nome depende se ele está aninhado dentro do recurso pai ou definido fora do recurso pai. Consulte [definir nome e tipo para recursos filho](child-resource-name-type.md). |
| location |Varia |Locais geográficos com suporte do recurso fornecido. Você pode selecionar qualquer um dos locais disponíveis, mas, normalmente, faz sentido escolher um que esteja próximo de seus usuários. Normalmente, também faz sentido posicionar recursos que interagem entre si na mesma região. A maioria dos tipos de recursos requer um local, mas alguns tipos (como uma atribuição de função) não exigem um local. Consulte [definir local do recurso](resource-location.md) |
| tags |Não |Marcas associadas ao recurso. Aplique marcas para organizar os recursos de forma lógica em sua assinatura. |
| feitos |Não |Suas notas para documentar os recursos em seu modelo. Para obter mais informações, consulte [comentários em modelos](resource-group-authoring-templates.md#comments). |
| copiar |Não |Se mais de uma instância for necessária, o número de recursos a serem criados. O modo padrão é Parallel. Especifique o modo Serial quando não quiser que todos ou os recursos sejam implantados ao mesmo tempo. Para obter mais informações, consulte [criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Não |Recursos que devem ser implantados antes do recurso ser implantado. O Gerenciador de recursos avalia as dependências entre os recursos e os implanta na ordem correta. Quando os recursos não dependem uns dos outros, eles são implantados em paralelo. O valor pode ser uma lista separada por vírgulas de nomes de recursos ou identificadores exclusivos de recursos. Lista apenas os recursos que são implantados neste modelo. Os recursos que não estão definidos neste modelo já devem existir. Evite adicionar dependências desnecessárias, pois elas podem retardar a implantação e criar dependências circulares. Para obter orientação sobre como definir dependências, consulte [definindo dependências em modelos de Azure Resource Manager](resource-group-define-dependencies.md). |
| properties |Não |Definições de configuração específicas do recurso. Os valores para as propriedades são os mesmos que os valores fornecidos no corpo da solicitação para a operação da API REST (método PUT) para criar o recurso. Você também pode especificar uma matriz de cópia para criar várias instâncias de uma propriedade. Para determinar os valores disponíveis, consulte [referência de modelo](/azure/templates/). |
| sku | Não | Alguns recursos permitem valores que definem a SKU a ser implantada. Por exemplo, você pode especificar o tipo de redundância para uma conta de armazenamento. |
| type | Não | Alguns recursos permitem um valor que define o tipo de recurso que você implanta. Por exemplo, você pode especificar o tipo de Cosmos DB a ser criado. |
| intenção | Não | Alguns recursos permitem valores que definem o plano a ser implantado. Por exemplo, você pode especificar a imagem do Marketplace para uma máquina virtual. | 
| recursos |Não |Recursos filho que dependem do recurso que está sendo definido. Forneça apenas os tipos de recursos que são permitidos pelo esquema do recurso pai. A dependência do recurso pai não está implícita. Você deve definir explicitamente essa dependência. Consulte [definir nome e tipo para recursos filho](child-resource-name-type.md). |

### <a name="resource-names"></a>Nomes de recursos

Em geral, você trabalha com três tipos de nomes de recursos no Resource Manager:

* Nomes de recursos que devem ser exclusivos.
* Nomes de recursos que não precisam ser exclusivos, mas você opta por fornecer um nome que possa ajudá-lo a identificar o recurso.
* Nomes de recursos que podem ser genéricos.

Forneça um **nome de recurso exclusivo** para qualquer tipo de recurso que tenha um ponto de extremidade de acesso de dados. Alguns tipos de recursos comuns que exigem um nome exclusivo incluem:

* Armazenamento do Azure<sup>1</sup> 
* Funcionalidade de Aplicações Web no Serviço de Aplicações do Azure
* SQL Server
* Azure Key Vault
* Cache do Azure para Redis
* Azure Batch
* Gestor de Tráfego do Azure
* Azure Search
* Azure HDInsight

<sup>1</sup> os nomes de conta de armazenamento também devem ser minúsculos, 24 caracteres ou menos e não ter hifens.

Ao definir o nome, você pode criar manualmente um nome exclusivo ou usar a função [uniquestring ()](resource-group-template-functions-string.md#uniquestring) para gerar um nome. Você também pode querer adicionar um prefixo ou sufixo ao resultado **uniquestring** . Modificar o nome exclusivo pode ajudá-lo a identificar mais facilmente o tipo de recurso do nome. Por exemplo, você pode gerar um nome exclusivo para uma conta de armazenamento usando a seguinte variável:

```json
"variables": {
  "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

Para alguns tipos de recursos, talvez você queira fornecer um **nome para identificação**, mas o nome não precisa ser exclusivo. Para esses tipos de recursos, forneça um nome que descreva o uso ou as características de ti.

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

Para tipos de recursos que você acessa principalmente por meio de um recurso diferente, você pode usar um **nome genérico** que é embutido em código no modelo. Por exemplo, você pode definir um nome genérico e padrão para regras de firewall em um SQL Server:

```json
{
  "type": "firewallrules",
  "name": "AllowAllWindowsAzureIps",
  ...
}
```

## <a name="outputs"></a>outputs

Na secção de saídas, especifique os valores que são devolvidos da implementação. Normalmente, você retorna valores de recursos que foram implantados.

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
| condition |Não | Valor booliano que indica se esse valor de saída é retornado. Quando `true`, o valor é incluído na saída para a implantação. Quando `false`, o valor de saída é ignorado para essa implantação. Quando não especificado, o valor padrão é `true`. |
| type |Sim |Tipo do valor de saída. Valores de saída suportam os mesmos tipos de parâmetros de entrada de modelo. Se você especificar **SecureString** para o tipo de saída, o valor não será exibido no histórico de implantação e não poderá ser recuperado de outro modelo. Para usar um valor secreto em mais de um modelo, armazene o segredo em um Key Vault e referencie o segredo no arquivo de parâmetro. Para obter mais informações, consulte [usar Azure Key Vault para passar o valor do parâmetro seguro durante a implantação](resource-manager-keyvault-parameter.md). |
| value |Sim |Expressão de linguagem de modelo que é avaliada e devolvida como valor de saída. |

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

O exemplo a seguir mostra como retornar condicionalmente a ID de recurso para um endereço IP público com base no fato de um novo ter sido implantado:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Para obter um exemplo simples de saída condicional, consulte [modelo de saída condicional](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

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

O exemplo a seguir mostra como definir o endereço IP em um balanceador de carga recuperando um valor de um modelo vinculado.

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

Você tem algumas opções para adicionar comentários e metadados ao seu modelo.

Você pode adicionar um `metadata` objeto quase em qualquer lugar em seu modelo. O Resource Manager ignora o objeto, mas seu editor de JSON pode avisá-lo que a propriedade não é válida. No objeto, defina as propriedades necessárias.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

Para **parâmetros**, adicione um `metadata` objeto com uma `description` propriedade.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

Ao implantar o modelo por meio do portal, o texto que você fornece na descrição é usado automaticamente como uma dica para esse parâmetro.

![Mostrar dica de parâmetro](./media/resource-group-authoring-templates/show-parameter-tip.png)

Para **recursos**, adicione um `comments` elemento ou um objeto de metadados. O exemplo a seguir mostra um elemento Comments e um objeto Metadata.

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

Para **saídas**, adicione um objeto de metadados ao valor de saída.

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

Você não pode adicionar um objeto de metadados a funções definidas pelo usuário.

Para comentários embutidos, você pode `//` usar, mas essa sintaxe não funciona com todas as ferramentas. Você não pode usar CLI do Azure para implantar o modelo com comentários embutidos. E você não pode usar o editor de modelo de portal para trabalhar em modelos com comentários embutidos. Se você adicionar esse estilo de comentário, certifique-se de que as ferramentas usadas suportam comentários JSON embutidos.

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

No VS Code, você pode definir o modo de linguagem como JSON com comentários. Os comentários embutidos não são mais marcados como inválidos. Para alterar o modo:

1. Abrir a seleção do modo de linguagem (CTRL + K M)

1. Selecione **JSON com comentários**.

   ![Selecionar modo de linguagem](./media/resource-group-authoring-templates/select-json-comments.png)

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>Passos seguintes
* Para ver modelos completos para vários tipos de soluções, veja os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).
* Para obter detalhes sobre as funções que pode utilizar a partir de dentro de um modelo, consulte [funções de modelo do Azure Resource Manager](resource-group-template-functions.md).
* Para combinar vários modelos durante a implantação, consulte [usando modelos vinculados com Azure Resource Manager](resource-group-linked-templates.md).
* Para obter recomendações sobre como criar modelos, consulte [Azure Resource Manager modelo de práticas recomendadas](template-best-practices.md).
* Para obter recomendações sobre como criar modelos do Resource Manager que você pode usar em todos os ambientes do Azure e Azure Stack, consulte [desenvolver modelos de Azure Resource Manager para consistência de nuvem](templates-cloud-consistency.md).
