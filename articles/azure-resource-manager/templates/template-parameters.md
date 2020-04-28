---
title: Parâmetros em modelos
description: Descreve como definir parâmetros num modelo de Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 89c6984c587e8dae59c1825a99d4f8da1c06dafb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76122428"
---
# <a name="parameters-in-azure-resource-manager-templates"></a>Parâmetros nos modelos do Gestor de Recursos Azure

Este artigo descreve como definir e usar parâmetros no seu modelo de Gestor de Recursos Azure. Ao fornecer diferentes valores para parâmetros, pode reutilizar um modelo para diferentes ambientes.

O Gestor de Recursos resolve os valores dos parâmetros antes de iniciar as operações de implantação. Onde quer que o parâmetro seja utilizado no modelo, o Gestor de Recursos substitui-o pelo valor resolvido.

## <a name="define-parameter"></a>Definir parâmetro

O exemplo que se segue mostra uma definição simples de parâmetro. Define um parâmetro chamado **storageSKU**. O parâmetro é um valor de cadeia, e só aceita valores válidos para a sua utilização pretendida. O parâmetro utiliza um valor predefinido quando não é fornecido qualquer valor durante a implantação.

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

## <a name="use-parameter"></a>Utilizar parâmetro

No modelo, referencia-se o valor do parâmetro utilizando a função [de parâmetros.](template-functions-deployment.md#parameters) No exemplo seguinte, o valor do parâmetro é utilizado para definir SKU para a conta de armazenamento.

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

Ao especificar o valor predefinido para um parâmetro, pode utilizar a maioria das funções do modelo. Pode utilizar outro valor de parâmetro para construir um valor predefinido. O seguinte modelo demonstra a utilização de funções no valor predefinido. Quando não é fornecido nenhum nome para o site, cria um valor de cadeia único e anexa-o ao **site**. Quando não é fornecido nenhum nome para o plano de acolhimento, leva o valor para o site, e anexa **-plan**.

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

Não pode utilizar a função [de referência](template-functions-resource.md#reference) ou qualquer uma das funções da [lista](template-functions-resource.md#list) na secção de parâmetros. Estas funções obtêm o estado de execução de um recurso, e não podem ser executadas antes da implantação quando os parâmetros são resolvidos.

## <a name="objects-as-parameters"></a>Objetos como parâmetros

Pode ser mais fácil organizar valores relacionados passando-os como um objeto. Esta abordagem também reduz o número de parâmetros no modelo.

O exemplo que se segue mostra um parâmetro que é um objeto. O valor predefinido mostra as propriedades esperadas para o objeto.

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

Referencia as propriedades do objeto utilizando o operador de pontos.

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

Os exemplos que se seguem demonstram cenários para a utilização de parâmetros.

|Modelo  |Descrição  |
|---------|---------|
|[parâmetros com funções para valores padrão](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Demonstra como usar as funções do modelo ao definir valores predefinidos para parâmetros. O modelo não distribui recursos. Constrói valores de parâmetros e devolve esses valores. |
|[objeto de parâmetro](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Demonstra usar um objeto para um parâmetro. O modelo não distribui recursos. Constrói valores de parâmetros e devolve esses valores. |


## <a name="next-steps"></a>Passos seguintes

* Para conhecer as propriedades disponíveis para parâmetros, consulte [Compreender a estrutura e a sintaxe dos modelos do Gestor de Recursos Azure.](template-syntax.md)
* Para aprender sobre passar os valores dos parâmetros como um ficheiro, consulte [o ficheiro de parâmetro seletiva Create Resource Manager](parameter-files.md).
* Para recomendações sobre a criação de parâmetros, consulte [As melhores práticas - parâmetros](template-best-practices.md#parameters).
