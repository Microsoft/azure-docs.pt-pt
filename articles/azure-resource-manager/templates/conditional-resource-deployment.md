---
title: Implantação condicional com modelos
description: Descreve como implantar condicionalmente um recurso num modelo de Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: 001a1a7d6d15fe29b0f3184b75892f4ec75cef27
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84017499"
---
# <a name="conditional-deployment-in-arm-templates"></a>Implantação condicional em modelos ARM

Por vezes, é necessário implementar opcionalmente um recurso num modelo de Gestor de Recursos Azure (ARM). Utilize o `condition` elemento para especificar se o recurso está implantado. O valor deste elemento resolve-se a verdade ou a ser falso. Quando o valor é verdadeiro, o recurso é criado. Quando o valor é falso, o recurso não é criado. O valor só pode ser aplicado a todo o recurso.

## <a name="new-or-existing-resource"></a>Recurso novo ou existente

Pode utilizar a implantação condicional para criar um novo recurso ou utilizar um existente. O exemplo seguinte mostra como usar a condição para implementar uma nova conta de armazenamento ou usar uma conta de armazenamento existente.

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

Quando o **parâmetro novoOrExisting** está definido para **novo,** a condição avalia-se verdadeiramente. A conta de armazenamento está implantada. No entanto, quando o **novo OrExisting** está definido para **existir,** a condição avalia para falso e a conta de armazenamento não é implementada.

Para um modelo de exemplo completo que utiliza o `condition` elemento, consulte [VM com uma rede virtual nova ou existente, armazenamento e IP público.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions)

## <a name="allow-condition"></a>Permitir condição

Pode passar num valor de parâmetro que indique se uma condição é permitida. O exemplo seguinte implementa um servidor SQL e permite opcionalmente iPs Azure.

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

Para obter o modelo completo, consulte o [servidor lógico Azure SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

## <a name="runtime-functions"></a>Funções de tempo de funcionamento

Se utilizar uma função de [referência](template-functions-resource.md#reference) ou [lista](template-functions-resource.md#list) com um recurso que é implantado condicionalmente, a função é avaliada mesmo que o recurso não seja implantado. Obtém-se um erro se a função se referir a um recurso que não existe.

Utilize a função [se](template-functions-logical.md#if) para se certificar de que a função só é avaliada para as condições em que o recurso é implantado. Consulte a [função se](template-functions-logical.md#if) para um modelo de amostra que utiliza se e referência com um recurso implantado condicionalmente.

Você define um [recurso tão dependente](define-resource-dependency.md) de um recurso condicional exatamente como qualquer outro recurso. Quando um recurso condicional não é implantado, o Gestor de Recursos Azure remove-o automaticamente das dependências necessárias.

## <a name="condition-with-complete-mode"></a>Condição com modo completo

Se implementar um modelo com [o modo completo](deployment-modes.md) e um recurso não for implantado porque a condição avalia falsamente, o resultado depende da versão REST API que utilizar para implementar o modelo. Se utilizar uma versão mais cedo do que 2019-05-10, o recurso **não é apagado**. Com 2019-05-10 ou mais tarde, o recurso **é apagado.** As versões mais recentes do Azure PowerShell e do Azure CLI apagam o recurso quando a condição é falsa.

## <a name="next-steps"></a>Próximos passos

* Para recomendações sobre a criação de modelos, consulte [as melhores práticas](template-best-practices.md)do modelo ARM.
* Para criar várias instâncias de um recurso, consulte a [iteração de recursos nos modelos ARM](copy-resources.md).
