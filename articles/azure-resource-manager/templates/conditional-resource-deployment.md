---
title: Implantação condicional com modelos
description: Descreve como implantar condicionalmente um recurso em um modelo de Azure Resource Manager.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: b4ee733e81f3a923f268f33f5826387059923bcb
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122003"
---
# <a name="conditional-deployment-in-resource-manager-templates"></a>Implantação condicional em modelos do Resource Manager

Às vezes, você precisa implantar um recurso opcionalmente em um modelo. Use o elemento `condition` para especificar se o recurso é implantado. O valor desse elemento é resolvido como verdadeiro ou falso. Quando o valor for true, o recurso será criado. Quando o valor for false, o recurso não será criado. O valor só pode ser aplicado a todo o recurso.

## <a name="new-or-existing-resource"></a>Recurso novo ou existente

Você pode usar a implantação condicional para criar um novo recurso ou usar um existente. O exemplo a seguir mostra como usar a condição para implantar uma nova conta de armazenamento ou usar uma conta de armazenamento existente.

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "2017-06-01",
  "name": "[variables('storageAccountName')]",
  "location": "[parameters('location')]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

Quando o parâmetro **newOrExisting** é definido como **New**, a condição é avaliada como true. A conta de armazenamento é implantada. No entanto, quando **newOrExisting** é definido como **existing**, a condição é avaliada como false e a conta de armazenamento não é implantada.

Para obter um modelo de exemplo completo que usa o elemento `condition`, consulte [VM com uma rede virtual nova ou existente, armazenamento e IP público](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="allow-condition"></a>Condição de permissão

Você pode passar um valor de parâmetro que indica se uma condição é permitida. O exemplo a seguir implanta um SQL Server e, opcionalmente, permite IPs do Azure.

```json
{
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2015-05-01-preview",
  "name": "[parameters('serverName')]",
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
      "apiVersion": "2015-05-01-preview",
      "name": "AllowAllWindowsAzureIps",
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

Se você usar uma função de [referência](template-functions-resource.md#reference) ou de [lista](template-functions-resource.md#list) com um recurso que é implantado condicionalmente, a função será avaliada mesmo que o recurso não seja implantado. Você receberá um erro se a função se referir a um recurso que não existe.

Use a função [If](template-functions-logical.md#if) para certificar-se de que a função é avaliada apenas para condições quando o recurso for implantado. Consulte a [função If](template-functions-logical.md#if) para obter um modelo de exemplo que usa If e Reference com um recurso implantado condicionalmente.

Você define um [recurso como dependente](define-resource-dependency.md) de um recurso condicional exatamente como faria com qualquer outro recurso. Quando um recurso condicional não é implantado, Azure Resource Manager o remove automaticamente das dependências necessárias.

## <a name="condition-with-complete-mode"></a>Condição com modo completo

Se você implantar um modelo com o [modo completo](deployment-modes.md) e um recurso não for implantado porque a condição é avaliada como false, o resultado depende de qual versão da API REST você usa para implantar o modelo. Se você usar uma versão anterior à 2019-05-10, o recurso **não será excluído**. Com o 2019-05-10 ou posterior, o recurso **é excluído**. As versões mais recentes do Azure PowerShell e CLI do Azure excluir o recurso quando a condição for falsa.

## <a name="next-steps"></a>Passos seguintes

* Para obter recomendações sobre como criar modelos, consulte [Azure Resource Manager modelo de práticas recomendadas](template-best-practices.md).
* Para criar várias instâncias de um recurso, consulte [recurso, propriedade ou iteração de variável em modelos de Azure Resource Manager](create-multiple-instances.md).