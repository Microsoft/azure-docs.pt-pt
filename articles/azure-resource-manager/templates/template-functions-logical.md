---
title: Funções do modelo - lógica
description: Descreve as funções a utilizar num modelo de Gestor de Recursos Azure (modelo ARM) para determinar valores lógicos.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 27d94f10374daf0b9a351469579a5eb659cf5445
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96920475"
---
# <a name="logical-functions-for-arm-templates"></a>Funções lógicas para modelos ARM

O Gestor de Recursos fornece várias funções para fazer comparações no seu modelo de Gestor de Recursos Azure (modelo ARM):

* [and](#and)
* [bool](#bool)
* [falso](#false)
* [se](#if)
* [não](#not)
* [ou](#or)
* [verdade](#true)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="and"></a>e

`and(arg1, arg2, ...)`

Verifica se todos os valores dos parâmetros são verdadeiros. A `and` função não é suportada em Bicep. Em vez disso, utilize o `&&` operador.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |boolean |O primeiro valor para verificar se é verdade. |
| arg2 |Yes |boolean |O segundo valor para verificar se é verdade. |
| argumentos adicionais |No |boolean |Argumentos adicionais para verificar se são verdadeiros. |

### <a name="return-value"></a>Valor devolvido

Devoluções **Verdadeiras** se todos os valores forem verdadeiros; caso contrário, **Falso**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a seguir mostra como usar funções lógicas.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]"
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

A saída do exemplo anterior é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| eExampleOutput | Booleano | Falso |
| ouExampleOutput | Booleano | Verdadeiro |
| notExampleOutput | Booleano | Falso |

## <a name="bool"></a>bool

`bool(arg1)`

Converte o parâmetro num booleano.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |corda ou int |O valor para converter para um boolean. |

### <a name="return-value"></a>Valor devolvido

Um booleano do valor convertido.

### <a name="remarks"></a>Observações

Também pode usar [valores verdadeiros](#true) e [falsos](#false) para obter valores boolean.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) a seguir mostra como usar bool com uma corda ou inteiro.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueString": {
      "type": "bool",
      "value": "[bool('true')]",
    },
    "falseString": {
      "type": "bool",
      "value": "[bool('false')]"
    },
    "trueInt": {
      "type": "bool",
      "value": "[bool(1)]"
    },
    "falseInt": {
      "type": "bool",
      "value": "[bool(0)]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output trueString bool = bool('true')
output falseString bool = bool('false')
output trueInt bool = bool(1)
output falseInt bool = bool(0)
```

---
A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| trueString | Booleano | Verdadeiro |
| falseString | Booleano | Falso |
| trueInt | Booleano | Verdadeiro |
| falseInt | Booleano | Falso |

## <a name="false"></a>false

`false()`

Devoluções falsas. A `false` função não está disponível em Bicep.  Use a `false` palavra-chave em vez disso.

### <a name="parameters"></a>Parâmetros

A falsa função não aceita parâmetros.

### <a name="return-value"></a>Valor devolvido

Um booleano que é sempre falso.

### <a name="example"></a>Exemplo

O exemplo a seguir devolve um valor de saída falso.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "falseOutput": {
      "type": "bool",
      "value": "[false()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output falseOutput bool = false
```

---

A saída do exemplo anterior é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| falsoOutput | Booleano | Falso |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Devolve um valor baseado no facto de uma condição ser verdadeira ou falsa. A `if` função não é suportada em Bicep. Em vez disso, utilize o `?:` operador.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| condição |Yes |boolean |O valor para verificar se é verdade ou falso. |
| verdadeiroValue |Yes | cadeia, int, objeto ou matriz |O valor para voltar quando a condição é verdadeira. |
| falsoValue |Yes | cadeia, int, objeto ou matriz |O valor para devolver quando a condição é falsa. |

### <a name="return-value"></a>Valor devolvido

Devolve o segundo parâmetro quando o primeiro parâmetro é **verdadeiro;** caso contrário, devolve o terceiro parâmetro.

### <a name="remarks"></a>Observações

Quando a condição é **verdadeira,** apenas o verdadeiro valor é avaliado. Quando a condição é **falsa,** apenas o valor falso é avaliado. Com a `if` função, pode incluir expressões que são apenas válidas condicionalmente. Por exemplo, pode referenciar um recurso que existe sob uma condição, mas não sob outra condição. Na secção seguinte é apresentado um exemplo de avaliação condicional das expressões.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) a seguir mostra como utilizar a `if` função.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "yesOutput": {
      "type": "string",
      "value": "[if(equals('a', 'a'), 'yes', 'no')]"
    },
    "noOutput": {
      "type": "string",
      "value": "[if(equals('a', 'b'), 'yes', 'no')]"
    },
    "objectOutput": {
      "type": "object",
      "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output yesOutput string = 'a' == 'a' ? 'yes' : 'no'
output noOutput string = 'a' == 'b' ? 'yes' : 'no'
output objectOutput object = 'a' == 'a' ? json('{"test": "value1"}') : json('null')
```

---

A saída do exemplo anterior é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| simOutput | String | sim |
| noOutput | String | não |
| objetoOutput | Objeto | { "teste": "valor1" } |

O [modelo de exemplo](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) a seguir mostra como usar esta função com expressões que são apenas válidas condicionalmente.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "logAnalytics": {
      "type": "string",
      "defaultValue": ""
    }
  },
  "resources": [
    {
      "condition": "[not(empty(parameters('logAnalytics')))]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "apiVersion": "2017-03-30",
      "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
      "location": "[parameters('location')]",
      "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
        },
        "protectedSettings": {
          "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
        }
      }
    }
  ],
  "outputs": {
    "mgmtStatus": {
      "type": "string",
      "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> `Conditions` ainda não são implementados em Bicep. Ver [Condições](https://github.com/Azure/bicep/issues/186).

---

## <a name="not"></a>not

`not(arg1)`

Converte o valor booleano ao seu valor oposto. A `not` função não é suportada em Bicep. Em vez disso, utilize o `!` operador.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |boolean |O valor para converter. |

### <a name="return-value"></a>Valor devolvido

**Retorna verdadeiro** quando o parâmetro é **falso**. Retorna **falso** quando o parâmetro é **verdadeiro.**

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a seguir mostra como usar funções lógicas.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]",
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

A saída do exemplo anterior é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| eExampleOutput | Booleano | Falso |
| ouExampleOutput | Booleano | Verdadeiro |
| notExampleOutput | Booleano | Falso |

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) a seguir **não** usa com [iguais](template-functions-comparison.md#equals).

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "checkNotEquals": {
      "type": "bool",
      "value": "[not(equals(1, 2))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output checkNotEquals bool = !(1 == 2)
```

---

A saída do exemplo anterior é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| checkNotEquals | Booleano | Verdadeiro |

## <a name="or"></a>ou

`or(arg1, arg2, ...)`

Verifica se qualquer valor de parâmetro é verdadeiro. A `or` função não é suportada em Bicep. Em vez disso, utilize o `||` operador.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |boolean |O primeiro valor para verificar se é verdade. |
| arg2 |Yes |boolean |O segundo valor para verificar se é verdade. |
| argumentos adicionais |No |boolean |Argumentos adicionais para verificar se são verdadeiros. |

### <a name="return-value"></a>Valor devolvido

Devoluções **Verdadeiras** se algum valor for verdadeiro; caso contrário, **Falso**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a seguir mostra como usar funções lógicas.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "value": "[and(bool('true'), bool('false'))]",
      "type": "bool"
    },
    "orExampleOutput": {
      "value": "[or(bool('true'), bool('false'))]",
      "type": "bool"
    },
    "notExampleOutput": {
      "value": "[not(bool('true'))]",
      "type": "bool"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

A saída do exemplo anterior é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| eExampleOutput | Booleano | Falso |
| ouExampleOutput | Booleano | Verdadeiro |
| notExampleOutput | Booleano | Falso |

## <a name="true"></a>true

`true()`

Retorna verdadeira. A `true` função não está disponível em Bicep.  Use a `true` palavra-chave em vez disso.

### <a name="parameters"></a>Parâmetros

A verdadeira função não aceita parâmetros. A `true` função não está disponível em Bicep.  Use a `true` palavra-chave em vez disso.

### <a name="return-value"></a>Valor devolvido

Um booleano que é sempre verdade.

### <a name="example"></a>Exemplo

O exemplo a seguir devolve um verdadeiro valor de saída.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueOutput": {
      "type": "bool",
      "value": "[true()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output trueOutput bool = true
```

---

A saída do exemplo anterior é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| trueOutput | Booleano | Verdadeiro |

## <a name="next-steps"></a>Passos seguintes

* Para obter uma descrição das secções num modelo ARM, consulte [a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
