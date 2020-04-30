---
title: Definir ordem de implantação de recursos
description: Descreve como definir um recurso como dependente de outro recurso durante a implantação para garantir que os recursos são implantados na ordem correta.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: 764b718416e1185f56c7eb6b8335792a5822f212
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81535473"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>Defina a ordem para a implantação de recursos em modelos ARM

Ao implantar um recurso, poderá ter de se certificar de que existem outros recursos antes de ser implantado. Por exemplo, precisa de um servidor SQL antes de implementar uma base de dados SQL. Define esta relação marcando um recurso como dependente do outro recurso. Define uma dependência com o elemento **dependeon,** ou utilizando a função de **referência.**

O Resource Manager avalia as dependências entre os recursos e implementa-os por ordem dependente. Quando os recursos não são dependentes entre si, o Resource Manager implementa-os em paralelo. Basta definir dependências para recursos que são implantados no mesmo modelo.

## <a name="dependson"></a>dependsOn

Dentro do seu modelo, o elemento dependsOn permite definir um recurso como dependente de um ou mais recursos. O seu valor é uma gama de cordas JSON, cada uma das quais é um nome de recurso. A matriz pode incluir recursos que são [implantados condicionalmente.](conditional-resource-deployment.md) Quando um recurso condicional não é implantado, o Gestor de Recursos Azure remove-o automaticamente das dependências necessárias.

O exemplo seguinte mostra um conjunto de escala de máquina virtual que depende de um equilibrador de carga, rede virtual e um loop que cria várias contas de armazenamento. Estes outros recursos não são mostrados no exemplo seguinte, mas teriam de existir em outro lugar no modelo.

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

No exemplo anterior, uma dependência está incluída nos recursos que são criados através de um ciclo de cópia chamado **storageLoop**. Por exemplo, consulte [Criar múltiplas instâncias de recursos no Gestor de Recursos Azure](copy-resources.md).

Ao definir dependências, pode incluir o espaço de nome do fornecedor de recursos e o tipo de recurso para evitar ambiguidades. Por exemplo, para clarificar um equilibrador de carga e uma rede virtual que possa ter os mesmos nomes que outros recursos, utilize o seguinte formato:

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
```

Embora esteja inclinado a usar depende para mapear relações entre os seus recursos, é importante entender por que está a fazê-lo. Por exemplo, documentar como os recursos estão interligados, depende não é a abordagem certa. Não se pode consultar quais os recursos definidos no elemento dependsOn após a implantação. Ao utilizar depende, pode ter impacto no tempo de implantação porque o Gestor de Recursos não implementa em dois recursos paralelos que têm uma dependência.

## <a name="child-resources"></a>Recursos infantis

A propriedade de recursos permite especificar recursos infantis que estão relacionados com o recurso que está sendo definido. Os recursos infantis só podem ser definidos com cinco níveis de profundidade. É importante notar que não é criada uma dependência implícita de implantação entre um recurso infantil e o recurso-mãe. Se precisa que o recurso da criança seja implantado após o recurso principal, deve declarar explicitamente essa dependência com a propriedade dependente.

Cada recurso-mãe aceita apenas certos tipos de recursos como recursos infantis. Os tipos de recursos aceites são especificados no esquema do [modelo](https://github.com/Azure/azure-resource-manager-schemas) do recurso-mãe. O nome do tipo de recursos infantis inclui o nome do tipo de recurso-mãe, tais como **Microsoft.Web/sites/config** e **Microsoft.Web/sites/extensões** são ambos recursos infantis da **Microsoft.Web/sites**.

O exemplo seguinte mostra um servidor SQL e uma base de dados SQL. Note que uma dependência explícita é definida entre a base de dados SQL e o servidor SQL, mesmo que a base de dados seja uma criança do servidor.

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

A [função de referência](template-functions-resource.md#reference) permite que uma expressão obtenha o seu valor a partir de outros pares de nomejões e valores ou recursos de tempo de execução. As [funções da lista*](template-functions-resource.md#list) devolvem valores para um recurso de uma operação de lista.  As expressões de referência e lista declaram implicitamente que um recurso depende de outro, quando o recurso referenciado é implantado no mesmo modelo e referido pelo seu nome (não id de recurso). Se passar o ID de recurso para as funções de referência ou lista, não é criada uma referência implícita.

O formato geral da função de referência é:

```json
reference('resourceName').propertyPath
```

O formato geral da função listKeys é:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

No exemplo seguinte, um ponto final da CDN depende explicitamente do perfil de CDN, e implicitamente depende de uma aplicação web.

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

Pode utilizar este elemento ou o elemento dependsOn para especificar dependências, mas não precisa de usar ambos para o mesmo recurso dependente. Sempre que possível, utilize uma referência implícita para evitar adicionar uma dependência desnecessária.

Para saber mais, consulte a [função de referência.](template-functions-resource.md#reference)

## <a name="circular-dependencies"></a>Dependências circulares

O Gestor de Recursos identifica dependências circulares durante a validação do modelo. Se receber um erro indicando que existe uma dependência circular, avalie o seu modelo para ver se não são necessárias dependências e podem ser removidas. Se a remoção das dependências não funcionar, pode evitar dependências circulares movendo algumas operações de implantação em recursos infantis que são implantados após os recursos que têm a dependência circular. Por exemplo, suponha que está a implementar duas máquinas virtuais, mas tem de definir propriedades em cada uma que se refira à outra. Pode implantá-los na seguinte ordem:

1. vm1
2. vm2
3. A extensão do vm1 depende do vm1 e vm2. A extensão define valores em vm1 que obtém a partir de vm2.
4. A extensão do vm2 depende do vm1 e vm2. A extensão define valores em vm2 que obtém a partir de vm1.

Para obter informações sobre a avaliação da ordem de implantação e a resolução de erros de dependência, consulte erros comuns de [implantação do Azure Comum](common-deployment-errors.md)de Desenvolvimento De Segurança .

## <a name="next-steps"></a>Passos seguintes

* Para passar por um tutorial, consulte [Tutorial: crie modelos de Gestor de Recursos Azure com recursos dependentes](template-tutorial-create-templates-with-dependent-resources.md).
* Para recomendações ao definir dependências, consulte [as melhores práticas](template-best-practices.md)do modelo do Gestor de Recursos do Azure .
* Para aprender sobre dependências de resolução de problemas durante a implementação, consulte os erros comuns de implantação do [Azure Coma a Desacato.](common-deployment-errors.md)
* Para aprender sobre a criação de modelos de Gestor de Recursos Azure, consulte [os modelos de autor.](template-syntax.md)
* Para obter uma lista das funções disponíveis num modelo, consulte [funções de modelo](template-functions.md).

