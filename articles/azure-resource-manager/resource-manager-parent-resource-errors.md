---
title: Erros de recurso principal do Azure | Documentos da Microsoft
description: Descreve como resolver erros ao trabalhar com um recurso principal.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.author: tomfitz
ms.openlocfilehash: 19642138105f3de0e2bda3f0f80e12cf74a35709
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61059995"
---
# <a name="resolve-errors-for-parent-resources"></a>Resolva os erros para os recursos de principal

Este artigo descreve os erros que pode obter durante a implantação de um recurso que está dependente de um recurso principal.

## <a name="symptom"></a>Sintoma

Ao implementar um recurso que é um filho para outro recurso, poderá receber o erro seguinte:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Causa

Quando um recurso é um filho para outro recurso, o recurso principal tem de existir antes de criar o recurso de subordinados. O nome do recurso subordinado define a ligação com o recurso principal. O nome do recurso subordinado está no formato `<parent-resource-name>/<child-resource-name>`. Por exemplo, uma base de dados SQL pode ser definida como:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Se implementar o servidor e a base de dados no mesmo modelo, mas não especificar uma dependência no servidor, a implementação de base de dados pode iniciar antes de ter implementado o servidor. 

Se o recurso principal já existe e não está implementado no mesmo modelo, pode obter este erro quando o Gestor de recursos não é possível associar o recurso de subordinados com o elemento principal. Este erro poderá ocorrer se o recurso subordinado não está no formato correto ou o recurso de subordinado é implementado num grupo de recursos que é diferente do grupo de recursos para o recurso principal.

## <a name="solution"></a>Solução

Para resolver este erro quando os recursos principais e subordinadas são implementados no mesmo modelo, inclua uma dependência.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

Para resolver este erro quando o recurso principal foi anteriormente implementado num modelo diferente, não definir uma dependência. Em vez disso, implementar o filho no mesmo grupo de recursos e forneça o nome do recurso principal.

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

Para obter mais informações, consulte [definir a ordem para a implementação de recursos nos modelos do Azure Resource Manager](resource-group-define-dependencies.md).