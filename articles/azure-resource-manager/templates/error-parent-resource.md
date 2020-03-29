---
title: Erros de recursos parentais
description: Descreve como resolver erros ao trabalhar com um recurso-mãe num modelo de Gestor de Recursos Azure.
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.openlocfilehash: f1847389d60ddf3c6abc70bc3309940c2246084e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76154045"
---
# <a name="resolve-errors-for-parent-resources"></a>Resolver erros para os recursos parentais

Este artigo descreve os erros que pode obter ao implementar um recurso que está dependente de um recurso-mãe.

## <a name="symptom"></a>Sintoma

Ao utilizar um recurso que seja uma criança para outro recurso, poderá receber o seguinte erro:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Causa

Quando um recurso é uma criança para outro recurso, o recurso-mãe deve existir antes de criar o recurso para crianças. O nome do recurso infantil define a ligação com o recurso-mãe. O nome do recurso para `<parent-resource-name>/<child-resource-name>`crianças está no formato . Por exemplo, uma base de dados SQL pode ser definida como:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Se implementar o servidor e a base de dados no mesmo modelo, mas não especificar uma dependência do servidor, a implementação da base de dados poderá começar antes de o servidor ser implementado.

Se o recurso-mãe já existe e não está implantado no mesmo modelo, obtém-se este erro quando o Gestor de Recursos não pode associar o recurso à criança com o progenitor. Este erro pode acontecer quando o recurso da criança não está no formato correto, ou o recurso infantil é implantado para um grupo de recursos que é diferente do grupo de recursos para o recurso principal.

## <a name="solution"></a>Solução

Para resolver este erro quando os recursos dos pais e das crianças são implantados no mesmo modelo, inclua uma dependência.

```json
"dependsOn": [
  "[variables('databaseServerName')]"
]
```

Para resolver este erro quando o recurso-mãe foi previamente implantado num modelo diferente, não se define uma dependência. Em vez disso, desloque a criança para o mesmo grupo de recursos e forneça o nome do recurso-mãe.

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

Para mais informações, consulte [Definir a ordem de implantação de recursos nos modelos do Gestor](define-resource-dependency.md)de Recursos Azure .