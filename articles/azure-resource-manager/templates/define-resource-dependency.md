---
title: Definir ordem de implantação de recursos
description: Descreve como definir um recurso Azure como dependente de outro recurso durante a implantação. As dependências asseguram que os recursos são implantados na ordem correta.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: f6b63b066da06a17c3a2e51ab0f3ab9bf521a144
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934752"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>Definir a ordem para a implantação de recursos em modelos ARM

Ao mobilizar recursos, poderá ter de se certificar de que existem alguns recursos antes de outros recursos. Por exemplo, precisa de um servidor SQL lógico antes de implementar uma base de dados. Estabelece-se esta relação marcando um recurso como dependente do outro recurso. Use o `dependsOn` elemento para definir uma dependência explícita. Utilize as funções **de referência** ou **lista** para definir uma dependência implícita.

O Gestor de Recursos Azure avalia as dependências entre os recursos e os implementa na sua ordem dependente. Quando os recursos não são dependentes entre si, o Resource Manager implementa-os em paralelo. Basta definir dependências para recursos que são implantados no mesmo modelo.

## <a name="dependson"></a>dependsOn

Dentro do seu modelo de Gestor de Recursos Azure (modelo ARM), o `dependsOn` elemento permite-lhe definir um recurso como dependente de um ou mais recursos. O seu valor é uma gama de cordas JavaScript Object Notation (JSON), cada uma das quais é um nome de recurso ou ID. A matriz pode incluir recursos que são [implantados condicionalmente.](conditional-resource-deployment.md) Quando um recurso condicional não é implantado, o Azure Resource Manager remove-o automaticamente das dependências necessárias.

O exemplo a seguir mostra uma interface de rede que depende de uma rede virtual, de um grupo de segurança de rede e de um endereço IP público. Para o modelo completo, consulte [o modelo de arranque rápido para um Linux VM](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-simple-linux/azuredeploy.json).

```json
{
    "type": "Microsoft.Network/networkInterfaces",
    "apiVersion": "2020-06-01",
    "name": "[variables('networkInterfaceName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
      "[resourceId('Microsoft.Network/networkSecurityGroups/', parameters('networkSecurityGroupName'))]",
      "[resourceId('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
      "[resourceId('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
    ],
    ...
}
```

Embora possa estar inclinado a usar `dependsOn` para mapear relações entre os seus recursos, é importante entender porque o está a fazer. Por exemplo, documentar como os recursos estão interligados, `dependsOn` não é a abordagem certa. Não se pode consultar quais os recursos definidos no elemento após a `dependsOn` implantação. Estabelecer dependências desnecessárias atrasa o tempo de implementação porque o Gestor de Recursos não consegue implantar esses recursos em paralelo.

## <a name="child-resources"></a>Recursos infantis

Uma dependência implícita de implantação não é criada automaticamente entre um [recurso infantil](child-resource-name-type.md) e o recurso principal. Se precisar de implantar o recurso da criança após o recurso dos pais, desloque a `dependsOn` propriedade.

O exemplo a seguir mostra um servidor e base de dados lógicos sql. Note que uma dependência explícita é definida entre a base de dados e o servidor, mesmo que a base de dados seja uma criança do servidor.

```json
"resources": [
  {
    "type": "Microsoft.Sql/servers",
    "apiVersion": "2020-02-02-preview",
    "name": "[parameters('serverName')]",
    "location": "[parameters('location')]",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "type": "databases",
        "apiVersion": "2020-08-01-preview",
        "name": "[parameters('sqlDBName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
          },
        "dependsOn": [
          "[resourceId('Microsoft.Sql/servers', concat(parameters('serverName')))]"
        ]
      }
    ]
  }
]
```

Para o modelo completo, consulte o [modelo de arranque rápido para a base de dados Azure SQL](https://github.com/Azure/azure-quickstart-templates/blob/master/101-sql-database/azuredeploy.json).

## <a name="reference-and-list-functions"></a>funções de referência e lista

A [função de referência](template-functions-resource.md#reference) permite que uma expressão obtém o seu valor a partir de outros nomes JSON e pares de valor ou recursos de tempo de execução. A [lista* funciona valores](template-functions-resource.md#list) de retorno para um recurso a partir de uma operação de lista.

As expressões de referência e lista declaram implicitamente que um recurso depende de outro. Sempre que possível, utilize uma referência implícita para evitar adicionar uma dependência desnecessária.

Para impor uma dependência implícita, consulte o recurso pelo nome e não o ID do recurso. Se passar o ID do recurso nas funções de referência ou lista, não é criada uma referência implícita.

O formato geral da `reference` função é:

```json
reference('resourceName').propertyPath
```

O formato geral da `listKeys` função é:

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

Para saber mais, consulte a [função de referência.](template-functions-resource.md#reference)

## <a name="depend-on-resources-in-a-loop"></a>Dependa dos recursos em loop

Para mobilizar recursos que dependem de recursos num [ciclo de cópia,](copy-resources.md)tem duas opções. Pode definir uma dependência dos recursos individuais no ciclo ou em todo o ciclo.

> [!NOTE]
> Para a maioria dos cenários, deve definir a dependência dos recursos individuais dentro do ciclo de cópia. Só depende de todo o ciclo quando precisar de todos os recursos no loop para existir antes de criar o próximo recurso. Definir a dependência de todo o ciclo faz com que o gráfico de dependências se expanda significativamente, especialmente se esses recursos em loop dependem de outros recursos. As dependências alargadas dificultam a conclusão eficiente da implantação.

O exemplo a seguir mostra como implantar várias máquinas virtuais. O modelo cria o mesmo número de interfaces de rede. Cada máquina virtual depende de uma interface de rede, em vez de todo o loop.

```json
{
  "type": "Microsoft.Network/networkInterfaces",
  "apiVersion": "2020-05-01",
  "name": "[concat(variables('nicPrefix'),'-',copyIndex())]",
  "location": "[parameters('location')]",
  "copy": {
    "name": "nicCopy",
    "count": "[parameters('vmCount')]"
  },
  ...
},
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "name": "[concat(variables('vmPrefix'),copyIndex())]",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]"
  ],
  "copy": {
    "name": "vmCopy",
    "count": "[parameters('vmCount')]"
  },
  "properties": {
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]",
          "properties": {
            "primary": "true"
          }
        }
      ]
    },
    ...
  }
}
```

O exemplo a seguir mostra como implantar três contas de armazenamento antes de implantar a máquina virtual. Note que o `copy` elemento `name` se definiu e o elemento `storagecopy` para a máquina virtual também está definido para `dependsOn` `storagecopy` .

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="circular-dependencies"></a>Dependências circulares

O Gestor de Recursos identifica dependências circulares durante a validação do modelo. Se receber um erro de dependência circular, avalie o seu modelo para ver se alguma dependência pode ser removida. Se remover as dependências não funcionar, pode evitar dependências circulares movendo algumas operações de implantação em recursos infantis. Desdobre os recursos infantis após os recursos que têm a dependência circular. Por exemplo, suponha que está a implantar duas máquinas virtuais, mas tem de definir propriedades em cada uma que se refira à outra. Pode implantá-los na seguinte ordem:

1. vm1
2. vm2
3. A extensão em vm1 depende de Vm1 e vm2. A extensão define valores em Vm1 que obtém de vm2.
4. A extensão em vm2 depende de Vm1 e vm2. A extensão define valores em Vm2 que obtém de vm1.

Para obter informações sobre a avaliação da ordem de implantação e a resolução de erros de dependência, consulte [os erros comuns de resolução de Azure com o Azure Resource Manager](common-deployment-errors.md).

## <a name="next-steps"></a>Passos seguintes

* Para passar por um tutorial, consulte [Tutorial: Crie modelos ARM com recursos dependentes.](template-tutorial-create-templates-with-dependent-resources.md)
* Para um módulo Microsoft Learn que cubra as dependências de recursos, consulte [Gerir implementações complexas em nuvem utilizando funcionalidades avançadas do modelo ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
* Para obter recomendações ao definir dependências, consulte [as melhores práticas do modelo ARM](template-best-practices.md).
* Para saber mais sobre as dependências de resolução de problemas durante a implementação, consulte [os erros comuns de implementação do Azure com o Azure Resource Manager](common-deployment-errors.md).
* Para saber sobre a criação de modelos de Gestor de Recursos Azure, consulte [a estrutura e sintaxe dos modelos ARM](template-syntax.md).
* Para obter uma lista das funções disponíveis num modelo, consulte as [funções do modelo ARM](template-functions.md).
