---
title: Definir ordem de implantação para recursos
description: Descreve como definir um recurso como dependente de outro recurso durante a implantação para garantir que os recursos sejam implantados na ordem correta.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 6b608111f2fe24a0b426e5697ceb07349f2d4693
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149720"
---
# <a name="define-the-order-for-deploying-resources-in-azure-resource-manager-templates"></a>Definir a ordem de implantação de recursos em modelos de Azure Resource Manager

Para um determinado recurso, pode haver outros recursos que devem existir antes que o recurso seja implantado. Por exemplo, um SQL Server deve existir antes de tentar implantar um banco de dados SQL. Você define essa relação marcando um recurso como dependente do outro recurso. Você define uma dependência com o elemento **depends** ou usando a função de **referência** . 

O Resource Manager avalia as dependências entre os recursos e implementa-os por ordem dependente. Quando os recursos não são dependentes entre si, o Resource Manager implementa-os em paralelo. Você só precisa definir dependências para recursos que são implantados no mesmo modelo. 

Para obter um tutorial, consulte [tutorial: criar modelos de Azure Resource Manager com recursos dependentes](./resource-manager-tutorial-create-templates-with-dependent-resources.md).

## <a name="dependson"></a>dependsOn

Dentro de seu modelo, o elemento depends permite que você defina um recurso como dependente de um ou mais recursos. Seu valor pode ser uma lista separada por vírgulas de nomes de recursos. 

O exemplo a seguir mostra um conjunto de dimensionamento de máquinas virtuais que depende de um balanceador de carga, rede virtual e um loop que cria várias contas de armazenamento. Esses outros recursos não são mostrados no exemplo a seguir, mas eles precisariam existir em outro lugar no modelo.

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

No exemplo anterior, uma dependência é incluída nos recursos que são criados por meio de um loop de cópia chamado **storageLoop**. Para obter um exemplo, consulte [criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md).

Ao definir dependências, você pode incluir o namespace do provedor de recursos e o tipo de recurso para evitar ambigüidade. Por exemplo, para esclarecer um balanceador de carga e uma rede virtual que podem ter os mesmos nomes que outros recursos, use o seguinte formato:

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
``` 

Embora você possa ser inclinado a usar dependentes para mapear relações entre seus recursos, é importante entender por que você está fazendo isso. Por exemplo, para documentar como os recursos são interconectados, a depende não é a abordagem certa. Não é possível consultar quais recursos foram definidos no elemento depends após a implantação. Usando o depending, você pode impactar o tempo de implantação porque o Resource Manager não implanta em paralelo dois recursos que têm uma dependência. 

## <a name="child-resources"></a>Recursos filho
A propriedade Resources permite que você especifique recursos filho que estão relacionados ao recurso que está sendo definido. Os recursos filho só podem ser definidos em cinco níveis de profundidade. É importante observar que uma dependência de implantação implícita não é criada entre um recurso filho e o recurso pai. Se você precisar que o recurso filho seja implantado após o recurso pai, deverá declarar explicitamente essa dependência com a propriedade depending. 

Cada recurso pai aceita apenas determinados tipos de recursos como recursos filho. Os tipos de recurso aceitos são especificados no [esquema de modelo](https://github.com/Azure/azure-resource-manager-schemas) do recurso pai. O nome do tipo de recurso filho inclui o nome do tipo de recurso pai, como **Microsoft. Web/sites/config** e **Microsoft. Web/sites/Extensions** são ambos recursos filho do **Microsoft. Web/sites**.

O exemplo a seguir mostra um SQL Server e um banco de dados SQL. Observe que uma dependência explícita é definida entre o banco de dados SQL e o SQL Server, embora o banco de dados seja um filho do servidor.

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
A [função de referência](resource-group-template-functions-resource.md#reference) permite que uma expressão derive seu valor de outros pares de nome e valor JSON ou recursos de tempo de execução. As [funções List *](resource-group-template-functions-resource.md#list) retornam valores para um recurso de uma operação de lista.  Expressões de lista e referência implicitamente declaram que um recurso depende de outro, quando o recurso referenciado é implantado no mesmo modelo e referenciado por seu nome (não ID de recurso). Se você passar a ID do recurso para as funções de referência ou lista, uma referência implícita não será criada.

O formato geral da função de referência é:

```json
reference('resourceName').propertyPath
```

O formato geral da função listKeys é:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

No exemplo a seguir, um ponto de extremidade CDN depende explicitamente do perfil CDN e depende implicitamente de um aplicativo Web.

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

Você pode usar esse elemento ou o elemento depende para especificar dependências, mas não precisa usar ambos para o mesmo recurso dependente. Sempre que possível, use uma referência implícita para evitar a adição de uma dependência desnecessária.

Para saber mais, consulte [função de referência](resource-group-template-functions-resource.md#reference).

## <a name="circular-dependencies"></a>Dependências circulares

O Gerenciador de recursos identifica dependências circulares durante a validação do modelo. Se você receber um erro informando que existe uma dependência circular, avalie o modelo para ver se as dependências não são necessárias e podem ser removidas. Se a remoção de dependências não funcionar, você poderá evitar dependências circulares movendo algumas operações de implantação para recursos filho que são implantados após os recursos que têm a dependência circular. Por exemplo, suponha que você esteja implantando duas máquinas virtuais, mas deve definir propriedades em cada uma delas referente à outra. Você pode implantá-los na seguinte ordem:

1. vm1
2. vm2
3. A extensão em VM1 depende de VM1 e VM2. A extensão define valores em VM1 que obtém de VM2.
4. A extensão em VM2 depende de VM1 e VM2. A extensão define valores em VM2 que obtém de VM1.

Para obter informações sobre como avaliar a ordem de implantação e resolver erros de dependência, consulte [solucionar erros comuns de implantação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="next-steps"></a>Passos seguintes

* Para percorrer um tutorial, consulte [tutorial: criar modelos de Azure Resource Manager com recursos dependentes](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
* Para obter recomendações ao definir dependências, consulte [Azure Resource Manager modelo de práticas recomendadas](template-best-practices.md).
* Para saber mais sobre como solucionar problemas de dependências durante a implantação, consulte [solucionar erros comuns de implantação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para saber mais sobre como criar modelos de Azure Resource Manager, consulte Criando [modelos](resource-group-authoring-templates.md). 
* Para obter uma lista das funções disponíveis em um modelo, consulte [funções de modelo](resource-group-template-functions.md).

