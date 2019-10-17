---
title: Erros de recurso pai do Azure | Microsoft Docs
description: Descreve como resolver erros ao trabalhar com um recurso pai em um modelo de Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.author: tomfitz
ms.openlocfilehash: 197554e16e28b4928cab351838f00e1631c269fd
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390243"
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

Para obter mais informações, consulte [definir a ordem de implantação de recursos em modelos de Azure Resource Manager](resource-group-define-dependencies.md).