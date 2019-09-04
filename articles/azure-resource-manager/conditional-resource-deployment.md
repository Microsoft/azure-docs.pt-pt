---
title: Implantação condicional com modelos de Azure Resource Manager
description: Descreve como implantar condicionalmente um recurso em um modelo de Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: tomfitz
ms.openlocfilehash: 88f8b6a8dcce0e498a7b81b8741072bcf4cfcad8
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259512"
---
# <a name="conditional-deployment-in-resource-manager-templates"></a>Implantação condicional em modelos do Resource Manager

Às vezes, você precisa implantar um recurso opcionalmente em um modelo. Use o `condition` elemento para especificar se o recurso é implantado. O valor desse elemento é resolvido como verdadeiro ou falso. Quando o valor for true, o recurso será criado. Quando o valor for false, o recurso não será criado. O valor só pode ser aplicado a todo o recurso.

## <a name="new-or-existing-resource"></a>Recurso novo ou existente

Você pode usar a implantação condicional para criar um novo recurso ou usar um existente. O exemplo a seguir mostra como usar a condição para implantar uma nova conta de armazenamento ou usar uma conta de armazenamento existente.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Quando o parâmetro **newOrExisting** é definido como **New**, a condição é avaliada como true. A conta de armazenamento é implantada. No entanto, quando **newOrExisting** édefinido como existing, a condição é avaliada como false e a conta de armazenamento não é implantada.

Para obter um modelo de exemplo completo que `condition` usa o elemento, consulte [VM com uma rede virtual nova ou existente, armazenamento e IP público](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="allow-condition"></a>Condição de permissão

Você pode passar um valor de parâmetro que indica se uma condição é permitida. O exemplo a seguir implanta um SQL Server e, opcionalmente, permite IPs do Azure.

```json
{
    "type": "Microsoft.Sql/servers",
    "name": "[parameters('serverName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('location')]",
    "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
        "version": "12.0"
    },
    "resources": [
        {
            "condition": "[parameters('allowAzureIPs')]",
            "type": "firewallRules",
            "name": "AllowAllWindowsAzureIps",
            "apiVersion": "2015-05-01-preview",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/', parameters('serverName'))]"
            ],
            "properties": {
                "endIpAddress": "0.0.0.0",
                "startIpAddress": "0.0.0.0"
            }
        }
    ]
}
```

Para obter o modelo completo, consulte [servidor lógico do SQL do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

## <a name="runtime-functions"></a>Funções de tempo de execução

Se você usar uma função de [referência](resource-group-template-functions-resource.md#reference) ou de [lista](resource-group-template-functions-resource.md#list) com um recurso que é implantado condicionalmente, a função será avaliada mesmo que o recurso não seja implantado. Você receberá um erro se a função se referir a um recurso que não existe.

Use a função [If](resource-group-template-functions-logical.md#if) para certificar-se de que a função é avaliada apenas para condições quando o recurso for implantado. Consulte a [função If](resource-group-template-functions-logical.md#if) para obter um modelo de exemplo que usa If e Reference com um recurso implantado condicionalmente.

## <a name="next-steps"></a>Passos seguintes

* Para obter recomendações sobre como criar modelos, consulte [Azure Resource Manager modelo de práticas recomendadas](template-best-practices.md).
* Para criar várias instâncias de um recurso, consulte [recurso, propriedade ou iteração de variável em modelos de Azure Resource Manager](resource-group-create-multiple.md).