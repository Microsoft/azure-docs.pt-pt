---
title: Implementação condicional com modelos
description: Descreve como implantar um recurso condicionalmente num modelo de Gestor de Recursos Azure (modelo ARM).
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 1492e9f9f45f23628f9933628fd2740e08ad9eb0
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672853"
---
# <a name="conditional-deployment-in-arm-templates"></a>Implantação condicional em modelos ARM

Por vezes, é necessário implantar opcionalmente um recurso num modelo de Gestor de Recursos Azure (modelo ARM). Utilize o `condition` elemento para especificar se o recurso é implantado. O valor deste elemento resolve-se a verdade ou falso. Quando o valor é verdadeiro, o recurso é criado. Quando o valor é falso, o recurso não é criado. O valor só pode ser aplicado a todo o recurso.

> [!NOTE]
> A implantação condicional não se cascata para [os recursos infantis.](child-resource-name-type.md) Se pretender implantar condicionalmente um recurso e os seus recursos para crianças, deve aplicar a mesma condição a cada tipo de recurso.

## <a name="new-or-existing-resource"></a>Recurso novo ou existente

Pode utilizar a implementação condicional para criar um novo recurso ou utilizar um existente. O exemplo a seguir mostra como usar a condição para implantar uma nova conta de armazenamento ou usar uma conta de armazenamento existente.

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

Quando o **novo parâmetroOrExisting** é definido como **novo,** a condição avalia-se a verdade. A conta de armazenamento está implantada. No entanto, quando o **novoOrExisting** está definido para **existir,** a condição avalia-se em falso e a conta de armazenamento não é implantada.

Para obter um modelo de exemplo completo que utilize o `condition` elemento, consulte [VM com uma rede virtual nova ou existente, armazenamento e IP público.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions)

## <a name="allow-condition"></a>Permitir a condição

Pode passar num valor de parâmetro que indique se uma condição é permitida. O exemplo a seguir implementa um servidor SQL e permite opcionalmente IPs Azure.

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

Para obter o modelo completo, consulte [o servidor lógico Azure SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

## <a name="runtime-functions"></a>Funções de tempo de execução

Se utilizar uma função [de referência](template-functions-resource.md#reference) ou [lista](template-functions-resource.md#list) com um recurso que esteja implantado condicionalmente, a função é avaliada mesmo que o recurso não seja implantado. Obtém-se um erro se a função se referir a um recurso que não existe.

Utilize a função [se](template-functions-logical.md#if) funcionar para se certificar de que a função só é avaliada para as condições quando o recurso é implantado. Consulte a [função se](template-functions-logical.md#if) for para um modelo de amostra que utiliza se e referenciar com um recurso implantado condicionalmente.

Define um [recurso tão dependente](define-resource-dependency.md) de um recurso condicional exatamente como qualquer outro recurso. Quando um recurso condicional não é implantado, o Azure Resource Manager remove-o automaticamente das dependências necessárias.

## <a name="complete-mode"></a>Modo completo

Se implementar um modelo com [o modo completo](deployment-modes.md) e um recurso não for implantado porque a condição avalia falsamente, o resultado depende da versão API DO REST que utiliza para implementar o modelo. Se utilizar uma versão anterior a 2019-05-10, o recurso **não é eliminado**. Com 2019-05-10 ou posteriormente, o recurso **é eliminado.** As versões mais recentes do Azure PowerShell e do Azure CLI apagam o recurso quando a condição é falsa.

## <a name="next-steps"></a>Passos seguintes

* Para um módulo Microsoft Learn que cubra a implementação condicional, consulte [Gerir implementações complexas em nuvem utilizando funcionalidades avançadas do modelo ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
* Para recomendações sobre a criação de modelos, consulte [as melhores práticas do modelo ARM](template-best-practices.md).
* Para criar múltiplas instâncias de um recurso, consulte [a iteração de recursos nos modelos ARM](copy-resources.md).
