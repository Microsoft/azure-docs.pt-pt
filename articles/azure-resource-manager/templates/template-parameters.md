---
title: Parâmetros em modelos
description: Descreve como definir parâmetros num modelo de Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 89c6984c587e8dae59c1825a99d4f8da1c06dafb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76122428"
---
# <a name="parameters-in-azure-resource-manager-templates"></a>Parâmetros em modelos de gestor de recursos Azure

Este artigo descreve como definir e usar parâmetros no seu modelo de Gestor de Recursos Azure. Ao fornecer diferentes valores para parâmetros, pode reutilizar um modelo para diferentes ambientes.

O Gestor de Recursos resolve os valores dos parâmetros antes de iniciar as operações de implantação. Onde quer que o parâmetro seja utilizado no modelo, o Gestor de Recursos substitui-o pelo valor resolvido.

## <a name="define-parameter"></a>Definir parâmetro

O exemplo a seguir mostra uma definição de parâmetro simples. Define um parâmetro chamado **armazenamentoSKU.** O parâmetro é um valor de cadeia, e apenas aceita valores válidos para a sua utilização pretendida. O parâmetro utiliza um valor predefinido quando não é fornecido qualquer valor durante a implementação.

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

## <a name="use-parameter"></a>Use o parâmetro

No modelo, refere-se o valor do parâmetro utilizando a função [de parâmetros.](template-functions-deployment.md#parameters) No exemplo seguinte, o valor do parâmetro é utilizado para definir SKU para a conta de armazenamento.

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

## <a name="template-functions"></a>Funções de modelos

Ao especificar o valor predefinido para um parâmetro, pode utilizar a maioria das funções do modelo. Pode utilizar outro valor de parâmetro para construir um valor padrão. O modelo a seguir demonstra a utilização de funções no valor predefinido. Quando não é fornecido nenhum nome para o site, cria um valor de corda único e anexa-o ao **site.** Quando não é fornecido nenhum nome para o plano de anfitrião, é preciso o valor para o site, e anexa **-plan**.

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

Não é possível utilizar a função [de referência](template-functions-resource.md#reference) ou qualquer das funções da [lista](template-functions-resource.md#list) na secção de parâmetros. Estas funções obtêm o estado de funcionamento de um recurso, e não podem ser executadas antes da implementação quando os parâmetros são resolvidos.

## <a name="objects-as-parameters"></a>Objetos como parâmetros

Pode ser mais fácil organizar valores relacionados passando-os como um objeto. Esta abordagem também reduz o número de parâmetros no modelo.

O exemplo a seguir mostra um parâmetro que é um objeto. O valor predefinido mostra as propriedades esperadas para o objeto.

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

Refere as propriedades do objeto utilizando o operador de pontos.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2015-06-15",
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

## <a name="example-templates"></a>Modelos de exemplo

Os exemplos a seguir demonstram cenários para a utilização de parâmetros.

|Modelo  |Descrição  |
|---------|---------|
|[parâmetros com funções para valores predefinidos](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Demonstra como utilizar as funções do modelo ao definir valores predefinidos para parâmetros. O modelo não implementa recursos. Constrói valores de parâmetros e devolve esses valores. |
|[objeto de parâmetro](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Demonstra usar um objeto para um parâmetro. O modelo não implementa recursos. Constrói valores de parâmetros e devolve esses valores. |


## <a name="next-steps"></a>Próximos passos

* Para conhecer as propriedades disponíveis para parâmetros, consulte [a estrutura e sintaxe dos modelos Azure Resource Manager](template-syntax.md).
* Para saber como passar os valores dos parâmetros como ficheiro, consulte [o ficheiro de parâmetros Do Gestor de Recursos](parameter-files.md).
* Para recomendações sobre a criação de parâmetros, consulte [as melhores práticas - parâmetros.](template-best-practices.md#parameters)
