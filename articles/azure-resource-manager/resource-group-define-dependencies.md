---
title: Definir a ordem de implementação para recursos do Azure | Documentos da Microsoft
description: Descreve como definir um recurso como depende de outro recurso durante a implementação para garantir que recursos são implementados na ordem correta.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''
ms.assetid: 34ebaf1e-480c-4b4d-9bf6-251bd3f8f2cf
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: tomfitz
ms.openlocfilehash: 91325b7884eae4c6f4c85c142b1e81cf2121c039
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295339"
---
# <a name="define-the-order-for-deploying-resources-in-azure-resource-manager-templates"></a>Definir a ordem para a implementação de recursos nos modelos do Azure Resource Manager
Para um determinado recurso, pode haver outros recursos que tem de existir antes do recurso está implementado. Por exemplo, um SQL server tem de existir antes de tentar implementar uma base de dados SQL. Define esta relação, marcando um recurso como dependente do recurso de outro. Define uma dependência com o **dependsOn** elemento, ou utilizando o **referência** função. 

O Resource Manager avalia as dependências entre os recursos e implementa-os por ordem dependente. Quando os recursos não são dependentes entre si, o Resource Manager implementa-os em paralelo. Apenas terá de definir as dependências de recursos que são implementados no mesmo modelo. 

Para obter um tutorial, veja [Tutorial: criar modelos Azure Resource Manager com recursos dependentes](./resource-manager-tutorial-create-templates-with-dependent-resources.md).

## <a name="dependson"></a>dependsOn
No seu modelo, o elemento de dependsOn permite-lhe definir um recurso como um dependendo de um ou mais recursos. O valor pode ser uma lista separada por vírgulas de nomes de recursos. 

O exemplo seguinte mostra um conjunto de dimensionamento de máquinas virtuais que depende de um loop que cria várias contas de armazenamento, rede virtual e um balanceador de carga. Estes outros recursos não são apresentados no exemplo a seguir, mas elas precisariam de existir em outro lugar no modelo.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
  "apiVersion": "2016-03-30",
  "tags": {
    "displayName": "VMScaleSet"
  },
  "dependsOn": [
    "[variables('loadBalancerName')]",
    "[variables('virtualNetworkName')]",
    "storageLoop",
  ],
  ...
}
```

No exemplo anterior, uma dependência está incluída nos recursos que são criados por meio de um ciclo de cópia com o nome **storageLoop**. Por exemplo, veja [criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md).

Ao definir as dependências, pode incluir o espaço de nomes de fornecedor de recursos e o tipo de recurso para evitar ambiguidade. Por exemplo, para esclarecer a um balanceador de carga e a rede virtual que pode ter os mesmos nomes que outros recursos, utilize o seguinte formato:

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
``` 

Embora poderá ser artísticas utilizar dependsOn para mapear as relações entre os seus recursos, é importante compreender por que está fazendo isso. Por exemplo, documente a forma como os recursos estão interligados, dependsOn não é a abordagem correta. Não é possível consultar a que recursos foram definidos no elemento dependsOn após a implementação. Usando dependsOn, potencialmente afeta o tempo de implantação porque o Gestor de recursos não implementar em paralelos dois recursos que têm uma dependência. 

## <a name="child-resources"></a>Recursos subordinados
A propriedade de recursos permite-lhe especificar os recursos subordinados que estão relacionados com o recurso a ser definido. Recursos subordinados só podem ser definida cinco níveis de profundidade. É importante ter em atenção que uma dependência de implementação implícito não é criada entre um recurso de subordinado e o recurso principal. Se precisar do recurso de subordinados para ser implementada depois do recurso principal, têm de indicar explicitamente essa dependência com a propriedade dependsOn. 

Cada recurso principal aceita apenas determinados tipos de recursos, como recursos subordinados. Os tipos de recurso aceites são especificados na [esquema de modelo](https://github.com/Azure/azure-resource-manager-schemas) do recurso principal. O nome do tipo de recurso subordinado inclui o nome do tipo de recurso principal, como **Microsoft.Web/sites/config** e **Microsoft.Web/sites/extensions** são os dois recursos subordinados do **Microsoft.Web/sites**.

O exemplo seguinte mostra um SQL server e base de dados SQL. Tenha em atenção que uma dependência explícita é definida entre a base de dados SQL e o SQL server, apesar da base de dados é um filho do servidor.

```json
"resources": [
  {
    "name": "[variables('sqlserverName')]",
    "type": "Microsoft.Sql/servers",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "SqlServer"
    },
    "apiVersion": "2014-04-01-preview",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "name": "[parameters('databaseName')]",
        "type": "databases",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "Database"
        },
        "apiVersion": "2014-04-01-preview",
        "dependsOn": [
          "[variables('sqlserverName')]"
        ],
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
        }
      }
    ]
  }
]
```

## <a name="reference-and-list-functions"></a>funções de referência e lista
O [fazem referência função](resource-group-template-functions-resource.md#reference) permite que uma expressão derive o respetivo valor a partir de outras JSON pares nome / valor ou a recursos de tempo de execução. O [lista * funções](resource-group-template-functions-resource.md#list) retornar valores para um recurso de uma operação de lista.  Expressões de referência e lista implicitamente declarar que um recurso depende de outra, quando o recurso referenciado está implementado no mesmo modelo e referido pelo respetivo nome (ID de recurso não). Se passar o ID de recurso para as funções de referência ou de lista, uma referência implícita não é criada.

O formato geral da função de referência é:

```json
reference('resourceName').propertyPath
```

O formato geral da função listKeys é:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

No exemplo a seguir, um ponto final da CDN explicitamente depende do perfil CDN e implicitamente depende de uma aplicação web.

```json
{
    "name": "[variables('endpointName')]",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "apiVersion": "2016-04-02",
    "dependsOn": [
            "[variables('profileName')]"
    ],
    "properties": {
        "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
        ...
    }
```

Pode utilizar este elemento ou o elemento de dependsOn para especificar as dependências, mas não precisa de utilizar para o mesmo recurso dependente. Sempre que possível, use uma referência implícita para evitar a adição de uma dependência desnecessária.

Para obter mais informações, consulte [fazem referência função](resource-group-template-functions-resource.md#reference).

## <a name="circular-dependencies"></a>Dependências circulares

Gestor de recursos identifica dependências circulares durante a validação do modelo. Se receber um erro a indicar que existe uma dependência circular, avalie o seu modelo para ver se todas as dependências não são necessários e podem ser removidas. Se remover as dependências não funcionar, pode evitar dependências circulares ao mover algumas operações de implementação para os recursos subordinados que são implementados depois dos recursos que têm a dependência circular. Por exemplo, suponha que estiver implantando duas máquinas virtuais, mas tem de definir as propriedades em cada um que se referem a outra. Pode implementá-las na seguinte ordem:

1. vm1
2. vm2
3. Extensão na vm1 depende da vm1 e vm2. A extensão define os valores na vm1 que obtém a partir do vm2.
4. Extensão na vm2 depende da vm1 e vm2. A extensão define os valores de vm2 que ele obtém da vm1.

Para obter informações sobre como avaliar a ordem de implementação e resolução de erros de dependência, consulte [resolver erros comuns de implementação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="next-steps"></a>Passos Seguintes

* Para seguir um tutorial, veja [Tutorial: criar modelos Azure Resource Manager com recursos dependentes](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
* Para obter recomendações ao definir as dependências, veja [práticas recomendadas do modelo do Azure Resource Manager](template-best-practices.md).
* Para saber mais sobre as dependências de resolução de problemas durante a implementação, consulte [resolver erros comuns de implementação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para saber mais sobre a criação de modelos Azure Resource Manager, veja [criação de modelos](resource-group-authoring-templates.md). 
* Para obter uma lista das funções disponíveis num modelo, consulte [funções de modelo](resource-group-template-functions.md).

