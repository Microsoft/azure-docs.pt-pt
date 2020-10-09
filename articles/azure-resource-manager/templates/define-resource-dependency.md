---
title: Definir ordem de implantação de recursos
description: Descreve como definir um recurso como dependente de outro recurso durante a implementação para garantir que os recursos são implantados na ordem correta.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: 84cea915565ec6ac9872681e1d4173abacb46ac4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85255216"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>Definir a ordem para a implantação de recursos em modelos ARM

Ao implementar um recurso, poderá ter de se certificar de que existem outros recursos antes de ser implantado. Por exemplo, precisa de um servidor SQL lógico antes de implementar uma base de dados. Define esta relação marcando um recurso como dependente do outro recurso. Define uma dependência com o elemento **dependOn,** ou utilizando a função **de referência.**

O Resource Manager avalia as dependências entre os recursos e implementa-os por ordem dependente. Quando os recursos não são dependentes entre si, o Resource Manager implementa-os em paralelo. Basta definir dependências para recursos que são implantados no mesmo modelo.

## <a name="dependson"></a>dependsOn

Dentro do seu modelo, o elemento dependon permite-lhe definir um recurso como dependente de um ou mais recursos. O seu valor é uma matriz de cordas JSON, cada uma das quais é um nome de recurso. A matriz pode incluir recursos que são [implantados condicionalmente.](conditional-resource-deployment.md) Quando um recurso condicional não é implantado, o Azure Resource Manager remove-o automaticamente das dependências necessárias.

O exemplo a seguir mostra um conjunto de escala de máquina virtual que depende de um equilibrador de carga, rede virtual e um loop que cria várias contas de armazenamento. Estes outros recursos não são mostrados no exemplo seguinte, mas eles teriam que existir em outro lugar no modelo.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "apiVersion": "2016-03-30",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
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

No exemplo anterior, uma dependência é incluída nos recursos que são criados através de um ciclo de cópia chamado **storageLoop**. Por exemplo, consulte [Criar várias instâncias de recursos no Azure Resource Manager](copy-resources.md).

Ao definir dependências, pode incluir o espaço de nome do fornecedor de recursos e o tipo de recurso para evitar a ambiguidade. Por exemplo, para clarificar um equilibrador de carga e uma rede virtual que possa ter os mesmos nomes que outros recursos, utilize o seguinte formato:

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
```

Embora possa estar inclinado a usar depende para mapear relações entre os seus recursos, é importante entender porque o está a fazer. Por exemplo, para documentar como os recursos estão interligados, depende deOn não é a abordagem certa. Não é possível consultar quais os recursos definidos no elemento dependOn após a implantação. Ao utilizar o tempo de implementação de impacto, potencialmente, o tempo de implementação porque o Gestor de Recursos não implementa em paralelo dois recursos que têm uma dependência.

## <a name="child-resources"></a>Recursos infantis

A propriedade de recursos permite especificar recursos infantis que estão relacionados com o recurso que está sendo definido. Os recursos infantis só podem ser definidos com cinco níveis de profundidade. É importante notar que uma dependência implícita de implantação não é criada entre um recurso infantil e o recurso principal. Se precisar que o recurso da criança seja implantado após o recurso principal, deve indicar explicitamente essa dependência com a propriedade dependOn.

Cada recurso dos pais aceita apenas certos tipos de recursos como recursos infantis. Os tipos de recursos aceites são especificados no esquema de [modelo](https://github.com/Azure/azure-resource-manager-schemas) do recurso principal. O nome do tipo de recurso infantil inclui o nome do tipo de recurso principal, tais como **Microsoft.Web/sites/config** e **Microsoft.Web/sites/extensions** são ambos recursos infantis do **Microsoft.Web/sites**.

O exemplo a seguir mostra um servidor e base de dados lógicos sql. Note que uma dependência explícita é definida entre a base de dados e o servidor, mesmo que a base de dados seja uma criança do servidor.

```json
"resources": [
  {
    "name": "[variables('sqlserverName')]",
    "apiVersion": "2014-04-01-preview",
    "type": "Microsoft.Sql/servers",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "SqlServer"
    },
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "name": "[parameters('databaseName')]",
        "apiVersion": "2014-04-01-preview",
        "type": "databases",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[variables('sqlserverName')]"
        ],
        "tags": {
          "displayName": "Database"
        },
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

A [função de referência](template-functions-resource.md#reference) permite que uma expressão obtém o seu valor a partir de outros nomes JSON e pares de valor ou recursos de tempo de execução. A [lista* funciona valores](template-functions-resource.md#list) de retorno para um recurso a partir de uma operação de lista.  As expressões de referência e de lista declaram implicitamente que um recurso depende de outro, quando o recurso referenciado é implantado no mesmo modelo e referido pelo seu nome (não ID de recurso). Se passar o ID do recurso nas funções de referência ou lista, não é criada uma referência implícita.

O formato geral da função de referência é:

```json
reference('resourceName').propertyPath
```

O formato geral da função listKeys é:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

No exemplo seguinte, um ponto final cdn depende explicitamente do perfil cdn, e depende implicitamente de uma aplicação web.

```json
{
    "name": "[variables('endpointName')]",
    "apiVersion": "2016-04-02",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "dependsOn": [
      "[variables('profileName')]"
    ],
    "properties": {
      "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
      ...
    }
```

Pode utilizar este elemento ou o elemento dependOn para especificar dependências, mas não precisa de usar ambos para o mesmo recurso dependente. Sempre que possível, utilize uma referência implícita para evitar adicionar uma dependência desnecessária.

Para saber mais, consulte a [função de referência.](template-functions-resource.md#reference)

## <a name="circular-dependencies"></a>Dependências circulares

O Gestor de Recursos identifica dependências circulares durante a validação do modelo. Se receber um erro indicando que existe uma dependência circular, avalie o seu modelo para ver se alguma dependência não é necessária e pode ser removida. Se a remoção das dependências não funcionar, pode evitar dependências circulares movendo algumas operações de implantação em recursos infantis que são implantados após os recursos que têm a dependência circular. Por exemplo, suponha que está a implantar duas máquinas virtuais, mas tem de definir propriedades em cada uma que se refira à outra. Pode implantá-los na seguinte ordem:

1. vm1
2. vm2
3. A extensão em vm1 depende de Vm1 e vm2. A extensão define valores em Vm1 que obtém de vm2.
4. A extensão em vm2 depende de Vm1 e vm2. A extensão define valores em Vm2 que obtém de vm1.

Para obter informações sobre a avaliação da ordem de implantação e a resolução de erros de dependência, consulte [os erros comuns de resolução de Azure com o Azure Resource Manager](common-deployment-errors.md).

## <a name="next-steps"></a>Passos seguintes

* Para passar por um tutorial, consulte [Tutorial: crie modelos de Gestor de Recursos Azure com recursos dependentes.](template-tutorial-create-templates-with-dependent-resources.md)
* Para obter recomendações ao definir dependências, consulte as [melhores práticas do modelo do Gestor de Recursos Azure](template-best-practices.md).
* Para saber mais sobre as dependências de resolução de problemas durante a implementação, consulte [os erros comuns de implementação do Azure com o Azure Resource Manager](common-deployment-errors.md).
* Para saber sobre a criação de modelos de Gestor de Recursos Azure, consulte [os modelos de autoria](template-syntax.md).
* Para obter uma lista das funções disponíveis num modelo, consulte as [funções do Modelo](template-functions.md).

