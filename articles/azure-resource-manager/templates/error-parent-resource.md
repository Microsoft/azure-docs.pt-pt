---
title: Erros de recursos dos pais
description: Descreve como resolver erros ao trabalhar com um recurso-mãe num modelo de Gestor de Recursos Azure.
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.openlocfilehash: 474cb85d16382136e24e5502b87ba8a1a65488ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84673665"
---
# <a name="resolve-errors-for-parent-resources"></a>Resolver erros para os recursos dos pais

Este artigo descreve os erros que pode obter ao implementar um recurso que depende de um recurso principal.

## <a name="symptom"></a>Sintoma

Ao utilizar um recurso que seja uma criança para outro recurso, poderá receber o seguinte erro:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Causa

Quando um recurso é uma criança para outro recurso, o recurso principal deve existir antes de criar o recurso da criança. O nome do recurso da criança define a ligação com o recurso principal. O nome do recurso para crianças está no formato `<parent-resource-name>/<child-resource-name>` . Por exemplo, uma base de dados SQL pode ser definida como:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Se implementar tanto o servidor como a base de dados no mesmo modelo, mas não especificar uma dependência do servidor, a implementação da base de dados pode começar antes de o servidor ter sido implantado.

Se o recurso principal já existe e não é implantado no mesmo modelo, obtém-se este erro quando o Gestor de Recursos não pode associar o recurso da criança ao progenitor. Este erro pode ocorrer quando o recurso da criança não está no formato correto, ou o recurso da criança é implantado num grupo de recursos diferente do grupo de recursos para o recurso dos pais.

## <a name="solution"></a>Solução

Para resolver este erro quando os recursos dos pais e da criança são implantados no mesmo modelo, inclua uma dependência.

```json
"dependsOn": [
  "[variables('databaseServerName')]"
]
```

Para resolver este erro quando o recurso principal foi previamente implantado num modelo diferente, não se define uma dependência. Em vez disso, desloque a criança para o mesmo grupo de recursos e forneça o nome do recurso principal.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "type": "Microsoft.Sql/servers/databases",
      "apiVersion": "2014-04-01",
      "name": "[concat(parameters('sqlServerName'), '/', parameters('databaseName'))]",
      "location": "[resourceGroup().location]",
      "properties": {
        "collation": "SQL_Latin1_General_CP1_CI_AS",
        "edition": "Basic"
      }
    }
  ],
  "outputs": {}
}
```

Para obter mais informações, consulte [Definir a ordem para a implantação de recursos nos modelos do Gestor de Recursos Azure](define-resource-dependency.md).