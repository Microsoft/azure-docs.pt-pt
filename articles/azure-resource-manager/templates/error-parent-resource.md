---
title: Erros de recurso pai
description: Descreve como resolver erros ao trabalhar com um recurso pai em um modelo de Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.openlocfilehash: 9fcf12db7375e6d19ef9e77ea4dcaf13130175b5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75484535"
---
# <a name="resolve-errors-for-parent-resources"></a>Resolver erros de recursos pai

Este artigo descreve os erros que você pode obter ao implantar um recurso que é dependente de um recurso pai.

## <a name="symptom"></a>Sintoma

Ao implantar um recurso que seja filho de outro recurso, você pode receber o seguinte erro:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Causa

Quando um recurso é filho para outro recurso, o recurso pai deve existir antes de criar o recurso filho. O nome do recurso filho define a conexão com o recurso pai. O nome do recurso filho está no formato `<parent-resource-name>/<child-resource-name>`. Por exemplo, um banco de dados SQL pode ser definido como:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Se você implantar o servidor e o banco de dados no mesmo modelo, mas não especificar uma dependência no servidor, a implantação do banco de dados poderá ser iniciada antes da implantação do servidor. 

Se o recurso pai já existir e não estiver implantado no mesmo modelo, você receberá esse erro quando o Gerenciador de recursos não puder associar o recurso filho ao pai. Esse erro pode ocorrer quando o recurso filho não está no formato correto ou o recurso filho é implantado em um grupo de recursos diferente do grupo de recursos para o recurso pai.

## <a name="solution"></a>Solução

Para resolver esse erro quando os recursos pai e filho são implantados no mesmo modelo, inclua uma dependência.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

Para resolver esse erro quando o recurso pai foi implantado anteriormente em um modelo diferente, você não define uma dependência. Em vez disso, implante o filho no mesmo grupo de recursos e forneça o nome do recurso pai.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "sqlServerName": {
            "type": "string"
        },
        "databaseName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2014-04-01",
            "type": "Microsoft.Sql/servers/databases",
            "location": "[resourceGroup().location]",
            "name": "[concat(parameters('sqlServerName'), '/', parameters('databaseName'))]",
            "properties": {
                "collation": "SQL_Latin1_General_CP1_CI_AS",
                "edition": "Basic"
            }
        }
    ],
    "outputs": {}
}
```

Para obter mais informações, consulte [definir a ordem de implantação de recursos em modelos de Azure Resource Manager](define-resource-dependency.md).