---
title: A cadeia de caracteres de funções de modelo do Resource Manager do Azure - | Documentos da Microsoft
description: Descreve as funções para utilizar num modelo do Azure Resource Manager para trabalhar com cadeias de caracteres.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/11/2019
ms.author: tomfitz
ms.openlocfilehash: c35352c47edb4f34100501ac791c84108fa9ac17
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762845"
---
# <a name="string-functions-for-azure-resource-manager-templates"></a>Funções de cadeia de caracteres para modelos Azure Resource Manager

O Resource Manager fornece as seguintes funções para trabalhar com cadeias de caracteres:

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [contains](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [empty](#empty)
* [endsWith](#endswith)
* [first](#first)
* [guid](#guid)
* [indexOf](#indexof)
* [last](#last)
* [lastIndexOf](#lastindexof)
* [Comprimento](#length)
* [newGuid](#newguid)
* [padLeft](#padleft)
* [replace](#replace)
* [skip](#skip)
* [dividir](#split)
* [startsWith](#startswith)
* [string](#string)
* [substring](#substring)
* [take](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [trim](#trim)
* [uniqueString](#uniquestring)
* [uri](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)
* [utcNow](#utcnow)

## <a name="base64"></a>base64

`base64(inputString)`

Devolve a representação base64 de cadeia de entrada.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| inputString |Sim |string |O valor a devolver como representação base64. |

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres que contém a representação base64.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) mostra como usar a função de base64.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | um, dois, três |
| toJsonOutput | Objeto | {"one": "a", "two": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Converte uma representação base64 para um objeto JSON.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| base64Value |Sim |string |A representação base64 para converter um objeto JSON. |

### <a name="return-value"></a>Valor de retorno

Um objeto JSON.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) usa a função de base64ToJson para converter um valor de base64:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | um, dois, três |
| toJsonOutput | Objeto | {"one": "a", "two": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Converte uma representação base64 numa cadeia.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| base64Value |Sim |string |A representação base64 para converter uma cadeia de caracteres. |

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres do valor base64 convertido.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) usa a função de base64ToString para converter um valor de base64:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | um, dois, três |
| toJsonOutput | Objeto | {"one": "a", "two": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

Combina vários valores de cadeia de caracteres e retorna a cadeia de caracteres concatenada, ou combina várias matrizes e retorna a matriz concatenada.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |cadeia de caracteres ou matriz |O primeiro valor para concatenação. |
| argumentos adicionais |Não |string |Valores adicionais na ordem sequencial, de concatenação. |

### <a name="return-value"></a>Valor de retorno
Uma cadeia ou matriz de valores concatenados.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) mostra como combinar os dois valores de cadeia de caracteres e retornar uma cadeia de caracteres concatenada.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "prefix": {
            "type": "string",
            "defaultValue": "prefix"
        }
    },
    "resources": [],
    "outputs": {
        "concatOutput": {
            "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| concatOutput | String | prefix-5yj4yjf5mbg72 |

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) mostra como combinar duas matrizes.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "firstArray": { 
            "type": "array", 
            "defaultValue": [ 
                "1-1", 
                "1-2", 
                "1-3" 
            ] 
        },
        "secondArray": {
            "type": "array", 
            "defaultValue": [ 
                "2-1", 
                "2-2",
                "2-3" 
            ] 
        }
    },
    "resources": [
    ],
    "outputs": {
        "return": {
            "type": "array",
            "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| devolver | Matriz | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>contém

`contains (container, itemToFind)`

Verifica se uma matriz contém um valor, um objeto contém uma chave ou uma cadeia de caracteres contém uma subcadeia. A comparação de cadeia de caracteres diferencia maiúsculas de minúsculas. No entanto, ao testar se um objeto contém uma chave, a comparação diferencia maiúsculas de minúsculas.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| contentor |Sim |cadeia de caracteres, objeto ou matriz |O valor que contém o valor a localizar. |
| itemToFind |Sim |cadeia de caracteres ou int |O valor a localizar. |

### <a name="return-value"></a>Valor de retorno

**TRUE** se o item for encontrado; caso contrário, **False**.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) mostra como utilizar contém com tipos diferentes:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "OneTwoThree"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringTrue": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'e')]"
        },
        "stringFalse": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'z')]"
        },
        "objectTrue": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'one')]"
        },
        "objectFalse": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'a')]"
        },
        "arrayTrue": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'three')]"
        },
        "arrayFalse": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'four')]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| stringTrue | Bool | Verdadeiro |
| stringFalse | Bool | Falso |
| objectTrue | Bool | Verdadeiro |
| objectFalse | Bool | Falso |
| arrayTrue | Bool | Verdadeiro |
| arrayFalse | Bool | Falso |

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Converte um valor para um URI de dados.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToConvert |Sim |string |O valor a converter para um URI de dados. |

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres formatada como um URI de dados.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) converte um valor para um URI de dados e converte um URI de dados para uma cadeia de caracteres:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Olá, mundo! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Converte um URI de dados formatado valor numa cadeia.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Sim |string |Os valor do URI a converter os dados. |

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres que contém o valor convertido.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) converte um valor para um URI de dados e converte um URI de dados para uma cadeia de caracteres:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Olá, mundo! |

## <a name="empty"></a>Vazio

`empty(itemToTest)`

Determina se uma matriz, um objeto ou uma cadeia vazia.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| itemToTest |Sim |cadeia de caracteres, objeto ou matriz |O valor a verificar se está vazia. |

### <a name="return-value"></a>Valor de retorno

Devolve **True** se o valor está vazio; caso contrário, **falso**.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) verifica se uma matriz, o objeto e a cadeia de caracteres são vazios.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": []
        },
        "testObject": {
            "type": "object",
            "defaultValue": {}
        },
        "testString": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testArray'))]"
        },
        "objectEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testObject'))]"
        },
        "stringEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testString'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| arrayEmpty | Bool | Verdadeiro |
| objectEmpty | Bool | Verdadeiro |
| stringEmpty | Bool | Verdadeiro |

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Determina se uma cadeia de caracteres termina com um valor. A comparação diferencia maiúsculas de minúsculas.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sim |string |O valor que contém o item para localizar. |
| stringToFind |Sim |string |O valor a localizar. |

### <a name="return-value"></a>Valor de retorno

**TRUE** se o último caráter ou carateres da cadeia de caracteres corresponde ao valor; caso contrário, **False**.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) mostra como utilizar as funções startsWith e endsWith:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| startsTrue | Bool | Verdadeiro |
| startsCapTrue | Bool | Verdadeiro |
| startsFalse | Bool | Falso |
| endsTrue | Bool | Verdadeiro |
| endsCapTrue | Bool | Verdadeiro |
| endsFalse | Bool | Falso |

## <a name="first"></a>primeiro

`first(arg1)`

Devolve o primeiro caráter da cadeia de caracteres ou primeiro elemento da matriz.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz ou cadeia de caracteres |O valor para recuperar o primeiro elemento ou caráter. |

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres do primeiro caráter ou o tipo (string, int, matriz ou objeto) do primeiro elemento numa matriz.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) mostra como usar a primeira função com uma matriz e a cadeia de caracteres.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[first(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[first('One Two Three')]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| arrayOutput | String | um |
| stringOutput | String | O |

## <a name="guid"></a>GUID

`guid (baseString, ...)`

Cria um valor no formato de um identificador exclusivo global com base nos valores fornecidos como parâmetros.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| baseString |Sim |string |O valor utilizado na função de hash para criar o GUID. |
| parâmetros adicionais conforme necessário |Não |string |Pode adicionar tantos cadeias de caracteres conforme necessário para criar o valor que especifica o nível de exclusividade. |

### <a name="remarks"></a>Observações

Esta função é útil quando precisa criar um valor no formato de um identificador exclusivo global. Fornecer valores de parâmetro que limitam o âmbito de exclusividade para o resultado. Pode especificar se o nome é exclusivo para baixo para a subscrição, grupo de recursos ou a implementação.

O valor devolvido não é uma cadeia de caracteres aleatória, mas em vez disso, o resultado de uma função de hash nos parâmetros. O valor retornado é 36 carateres de comprimento. Não é exclusivo globalmente. Para criar um novo GUID que não se baseia nesse valor de hash dos parâmetros, utilize o [Novo_guid](#newguid) função.

Os exemplos seguintes mostram como utilizar o guid para criar um valor exclusivo para níveis mais usados.

Recursos exclusivos no âmbito de subscrição

```json
"[guid(subscription().subscriptionId)]"
```

Recursos exclusivos no âmbito do grupo de recursos

```json
"[guid(resourceGroup().id)]"
```

Recursos exclusivos no âmbito de implementação para um grupo de recursos

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres contendo 36 caracteres no formato de um identificador exclusivo global.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) devolve resultados de guid:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "guidPerSubscription": {
            "value": "[guid(subscription().subscriptionId)]",
            "type": "string"
        },
        "guidPerResourceGroup": {
            "value": "[guid(resourceGroup().id)]",
            "type": "string"
        },
        "guidPerDeployment": {
            "value": "[guid(resourceGroup().id, deployment().name)]",
            "type": "string"
        }
    }
}
```

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

Devolve a primeira posição de um valor numa cadeia. A comparação diferencia maiúsculas de minúsculas.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sim |string |O valor que contém o item para localizar. |
| stringToFind |Sim |string |O valor a localizar. |

### <a name="return-value"></a>Valor de retorno

Um número inteiro que representa a posição do item a encontrar. O valor é baseado em zero. Se o item não for encontrado, é devolvido -1.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) mostra como utilizar as funções indexOf e lastIndexOf:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="last"></a>última

`last (arg1)`

Devolve a última caráter da cadeia ou o último elemento da matriz.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz ou cadeia de caracteres |O valor para recuperar o último elemento ou caráter. |

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres do último caráter, ou do tipo (string, int, matriz ou objeto) do último elemento numa matriz.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) mostra como utilizar a última função com uma matriz e a cadeia de caracteres.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[last(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[last('One Two Three')]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| arrayOutput | String | três |
| stringOutput | String | e |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Devolve a última posição de um valor numa cadeia. A comparação diferencia maiúsculas de minúsculas.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sim |string |O valor que contém o item para localizar. |
| stringToFind |Sim |string |O valor a localizar. |

### <a name="return-value"></a>Valor de retorno

Um número inteiro que representa a última posição do item a encontrar. O valor é baseado em zero. Se o item não for encontrado, é devolvido -1.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) mostra como utilizar as funções indexOf e lastIndexOf:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="length"></a>Comprimento

`length(string)`

Devolve o número de carateres numa cadeia de caracteres ou elementos numa matriz.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz ou cadeia de caracteres |A matriz a utilizar para obter o número de elementos, ou a cadeia de caracteres para utilizar para obter o número de carateres. |

### <a name="return-value"></a>Valor de retorno

Um int. 

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) mostra como utilizar o comprimento com uma matriz e a cadeia de caracteres:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "stringToTest": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "arrayLength": {
            "type": "int",
            "value": "[length(parameters('arrayToTest'))]"
        },
        "stringLength": {
            "type": "int",
            "value": "[length(parameters('stringToTest'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |

## <a name="newguid"></a>newGuid

`newGuid()`

Devolve um valor no formato de um identificador exclusivo global. **Esta função só pode ser utilizada no valor predefinido para um parâmetro.**

### <a name="remarks"></a>Observações

Só pode utilizar esta função dentro de uma expressão para o valor predefinido de um parâmetro. Usando essa função em qualquer parte num modelo devolve um erro. A função não é permitida em outras partes do modelo, porque retorna um valor diferente cada vez que for chamado. Implementar o mesmo modelo com os mesmos parâmetros não produzir de forma fiável os mesmos resultados.

A função de Novo_guid difere do [guid](#guid) porque ele não tem nenhum parâmetro de função. Quando chama o guid com o mesmo parâmetro, ele retorna o mesmo identificador de cada vez. Utilize o guid quando precisar de forma fiável gerar o mesmo GUID de um ambiente específico. Utilize Novo_guid quando precisar de um identificador de diferente cada vez, como a implementação de recursos para um ambiente de teste.

Se utilizar o [opção para voltar a implementar uma implementação com êxito anteriormente](resource-group-template-deploy-rest.md#redeploy-when-deployment-fails)e a implementação anterior inclui um parâmetro que utiliza Novo_guid, o parâmetro não é reavaliado. Em vez disso, o valor do parâmetro da implementação anterior é automaticamente reutilizado a implementação de reversão.

Num ambiente de teste, terá de implementar repetidamente a recursos apenas em direto para um curto período de tempo. Em vez de construir nomes exclusivos, pode usar Novo_guid com [uniqueString](#uniquestring) criar nomes exclusivos.

Cuidado-se de que a Reimplementar um modelo que se baseia na função Novo_guid para um valor predefinido. Quando voltar a implementar e não fornecer um valor para o parâmetro, a função é reavaliada. Se quiser atualizar um recurso existente, em vez de criar um novo, passe o valor do parâmetro da implementação anterior.

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres contendo 36 caracteres no formato de um identificador exclusivo global.

### <a name="examples"></a>Exemplos

O modelo de exemplo seguinte mostra um parâmetro com um novo identificador.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "guidOutput": {
            "type": "string",
            "value": "[parameters('guidValue')]"
        }
    }
}
```

A saída do exemplo anterior varia para cada implementação, mas será semelhante a:

| Name | Tipo | Value |
| ---- | ---- | ----- |
| guidOutput | string | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

O exemplo seguinte utiliza a função de Novo_guid para criar um nome exclusivo para uma conta de armazenamento. Este modelo pode funcionar para o ambiente de teste em que a conta de armazenamento existe para um curto período de tempo e não está implementada novamente.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "variables": {
        "storageName": "[concat('storage', uniqueString(parameters('guidValue')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "West US",
            "apiVersion": "2018-07-01",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {}
        }
    ],
    "outputs": {
        "nameOutput": {
            "type": "string",
            "value": "[variables('storageName')]"
        }
    }
}
```

A saída do exemplo anterior varia para cada implementação, mas será semelhante a:

| Name | Tipo | Value |
| ---- | ---- | ----- |
| nameOutput | string | storagenziwvyru7uxie |


## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

Devolve uma cadeia alinhada à direita através da adição de carateres para a esquerda até alcançar o comprimento especificado total.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| valueToPad |Sim |cadeia de caracteres ou int |O valor para se Alinhar à direita. |
| totalLength |Sim |int |O número total de carateres na cadeia devolvida. |
| paddingCharacter |Não |caráter único |O caráter a utilizar para preenchimento à esquerda até que o comprimento total seja atingido. O valor predefinido é um espaço. |

Se a cadeia de caracteres original for maior do que o número de carateres a preencher, sem carateres são adicionadas.

### <a name="return-value"></a>Valor de retorno

A cadeia de caracteres com, pelo menos, o número de carateres especificados.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) mostra como a encher o valor do parâmetro fornecidos pelo usuário, adicionando o caractere de zero até atingir o número total de carateres. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123"
        }
    },
    "resources": [],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[padLeft(parameters('testString'),10,'0')]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| stringOutput | String | 0000000123 |

## <a name="replace"></a>substituir

`replace(originalString, oldString, newString)`

Devolve uma nova cadeia com todas as instâncias de uma cadeia substituída por outra cadeia.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| originalString |Sim |string |O valor que tem todas as instâncias de uma cadeia substituída por outra cadeia. |
| oldString |Sim |string |A cadeia de caracteres a serem removidos da cadeia de caracteres original. |
| newString |Sim |string |A cadeia de caracteres para adicionar no lugar a cadeia de caracteres foi removida. |

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres com os carateres substituídos.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) mostra como remover todos os traços de cadeia de caracteres fornecidos pelo usuário e como substituir a parte da cadeia de caracteres por outra cadeia.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123-123-1234"
        }
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'-', '')]"
        },
        "secodeOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| firstOutput | String | 1231231234 |
| secodeOutput | String | 123-123-xxxx |

## <a name="skip"></a>ignorar

`skip(originalValue, numberToSkip)`

Devolve uma cadeia de caracteres com todos os carateres após o número especificado de carateres, ou uma matriz com todos os elementos após o número especificado de elementos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| originalValue |Sim |matriz ou cadeia de caracteres |A matriz ou cadeia de caracteres a utilizar para a ignorar. |
| numberToSkip |Sim |int |O número de elementos ou carateres para ignorar. Se este valor for 0 ou inferior, são devolvidos todos os elementos ou caracteres no valor. Se for maior do que o comprimento da matriz ou cadeia de caracteres, é devolvida uma matriz vazia ou uma cadeia de caracteres. |

### <a name="return-value"></a>Valor de retorno

Uma matriz ou cadeia de caracteres.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) ignora o número especificado de elementos na matriz e o número especificado de carateres numa cadeia.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToSkip": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToSkip": {
            "type": "int",
            "defaultValue": 4
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| arrayOutput | Matriz | ["three"] |
| stringOutput | String | dois três |

## <a name="split"></a>dividir

`split(inputString, delimiter)`

Devolve uma matriz de cadeias de caracteres que contém as subcadeias de carateres da cadeia de entrada são delimitadas por delimitadores especificados.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| inputString |Sim |string |A cadeia a dividir. |
| delimitador |Sim |cadeia de caracteres ou matriz de cadeias de caracteres |O delimitador a utilizar para dividir a cadeia. |

### <a name="return-value"></a>Valor de retorno

Uma matriz de cadeias de caracteres.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) divide a cadeia de entrada com uma vírgula e, com uma vírgula ou ponto e vírgula.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstString": {
            "type": "string",
            "defaultValue": "one,two,three"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "one;two,three"
        }
    },
    "variables": {
        "delimiters": [ ",", ";" ]
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "array",
            "value": "[split(parameters('firstString'),',')]"
        },
        "secondOutput": {
            "type": "array",
            "value": "[split(parameters('secondString'),variables('delimiters'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| firstOutput | Matriz | ["one", "two", "three"] |
| secondOutput | Matriz | ["one", "two", "three"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Determina se uma cadeia de caracteres começa com um valor. A comparação diferencia maiúsculas de minúsculas.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sim |string |O valor que contém o item para localizar. |
| stringToFind |Sim |string |O valor a localizar. |

### <a name="return-value"></a>Valor de retorno

**TRUE** se o primeiro caráter ou carateres da cadeia de caracteres corresponde ao valor; caso contrário, **False**.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) mostra como utilizar as funções startsWith e endsWith:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| startsTrue | Bool | Verdadeiro |
| startsCapTrue | Bool | Verdadeiro |
| startsFalse | Bool | Falso |
| endsTrue | Bool | Verdadeiro |
| endsCapTrue | Bool | Verdadeiro |
| endsFalse | Bool | Falso |

## <a name="string"></a>string

`string(valueToConvert)`

Converte o valor especificado para uma cadeia de caracteres.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| valueToConvert |Sim | Qualquer |O valor a converter a cadeia de caracteres. Qualquer tipo de valor pode ser convertido, incluindo objetos e matrizes. |

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres do valor convertido.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) mostra como converter tipos diferentes de valores de cadeias de caracteres:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testObject": {
            "type": "object",
            "defaultValue": {
                "valueA": 10,
                "valueB": "Example Text"
            }
        },
        "testArray": {
            "type": "array",
            "defaultValue": [
                "a",
                "b",
                "c"
            ]
        },
        "testInt": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "resources": [],
    "outputs": {
        "objectOutput": {
            "type": "string",
            "value": "[string(parameters('testObject'))]"
        },
        "arrayOutput": {
            "type": "string",
            "value": "[string(parameters('testArray'))]"
        },
        "intOutput": {
            "type": "string",
            "value": "[string(parameters('testInt'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| objectOutput | String | {"seus": 10, "Valorb": "Texto de exemplo"} |
| arrayOutput | String | ["a","b","c"] |
| intOutput | String | 5 |

## <a name="substring"></a>subcadeia

`substring(stringToParse, startIndex, length)`

Devolve uma subcadeia que começa com a posição do caráter especificado e contém o número especificado de carateres.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToParse |Sim |string |A cadeia de caracteres original do qual a subcadeia é extraída. |
| startIndex |Não |int |A baseado em zero caractere posição inicial para a subcadeia. |
| Comprimento |Não |int |O número de carateres para a subcadeia. Deve referir-se para uma localização na cadeia. Tem de ser zero ou superior. |

### <a name="return-value"></a>Valor de retorno

A subcadeia. Em alternativa, uma cadeia vazia se o comprimento for igual a zero.

### <a name="remarks"></a>Observações

A função falhar quando a subcadeia ultrapassa o fim da cadeia, ou quando o comprimento é inferior a zero. O exemplo a seguir falha com o erro "os parâmetros de índice e o comprimento devem referir-se para uma localização na cadeia. O parâmetro de índice: '0', o parâmetro de comprimento: "11", o comprimento do parâmetro de cadeia de caracteres: '10'.".

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) extrai uma subcadeia de um parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        }
    },
    "resources": [],
    "outputs": {
        "substringOutput": {
            "value": "[substring(parameters('testString'), 4, 3)]",
            "type": "string"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| substringOutput | String | dois |

## <a name="take"></a>tirar

`take(originalValue, numberToTake)`

Devolve uma cadeia com o número especificado de carateres desde o início da cadeia de caracteres ou uma matriz com o número especificado de elementos desde o início da matriz.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| originalValue |Sim |matriz ou cadeia de caracteres |A matriz ou cadeia de caracteres para tirar os elementos da. |
| numberToTake |Sim |int |O número de elementos ou carateres a tomar. Se este valor for 0 ou menos, uma matriz vazia ou uma cadeia de caracteres é devolvida. Se for maior do que o comprimento do determinada matriz ou cadeia de caracteres, todos os elementos da matriz ou cadeia de caracteres são devolvidos. |

### <a name="return-value"></a>Valor de retorno

Uma matriz ou cadeia de caracteres.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) usa o número especificado de elementos da matriz e carateres de uma cadeia.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToTake": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToTake": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| arrayOutput | Matriz | ["one", "two"] |
| stringOutput | String | em |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

Converte a cadeia especificada em minúsculas.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToChange |Sim |string |O valor a converter em minúsculas. |

### <a name="return-value"></a>Valor de retorno

A cadeia de caracteres é convertido em minúsculas.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) converte um valor de parâmetro em minúsculas e maiúsculas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| toLowerOutput | String | um dois três |
| toUpperOutput | String | UM DOIS TRÊS |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

Converte a cadeia especificada em maiúsculas.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToChange |Sim |string |O valor a converter em maiúsculas. |

### <a name="return-value"></a>Valor de retorno

A cadeia de caracteres convertida em maiúsculas.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) converte um valor de parâmetro em minúsculas e maiúsculas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| toLowerOutput | String | um dois três |
| toUpperOutput | String | UM DOIS TRÊS |

## <a name="trim"></a>Cortar

`trim (stringToTrim)`

Remove todos os caracteres de espaço em branco à esquerda e à direita da cadeia de caracteres especificada.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToTrim |Sim |string |O valor para cortar. |

### <a name="return-value"></a>Valor de retorno

A cadeia de caracteres à esquerda e carateres de espaço em branco à direita.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) corta os carateres de espaço em branco do parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "    one two three   "
        }
    },
    "resources": [],
    "outputs": {
        "return": {
            "type": "string",
            "value": "[trim(parameters('testString'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| devolver | String | um dois três |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Cria uma cadeia de hash determinístico com base nos valores fornecidos como parâmetros. 

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| baseString |Sim |string |O valor usado na função de hash para criar uma cadeia exclusiva. |
| parâmetros adicionais conforme necessário |Não |string |Pode adicionar tantos cadeias de caracteres conforme necessário para criar o valor que especifica o nível de exclusividade. |

### <a name="remarks"></a>Observações

Esta função é útil quando precisa criar um nome exclusivo para um recurso. Fornecer valores de parâmetro que limitam o âmbito de exclusividade para o resultado. Pode especificar se o nome é exclusivo para baixo para a subscrição, grupo de recursos ou a implementação. 

O valor devolvido não é uma cadeia de caracteres aleatória, mas em vez disso, o resultado de uma função de hash. O valor retornado é 13 carateres de comprimento. Não é exclusivo globalmente. Pode querer combinar o valor com um prefixo de sua Convenção de nomenclatura para criar um nome que faça sentido. O exemplo seguinte mostra o formato do valor retornado. O valor real varia consoante os parâmetros fornecidos.

    tcvhiyu5h2o5o

Os exemplos seguintes mostram como utilizar uniqueString para criar um valor exclusivo para níveis mais usados.

Recursos exclusivos no âmbito de subscrição

```json
"[uniqueString(subscription().subscriptionId)]"
```

Recursos exclusivos no âmbito do grupo de recursos

```json
"[uniqueString(resourceGroup().id)]"
```

Recursos exclusivos no âmbito de implementação para um grupo de recursos

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

O exemplo seguinte mostra como criar um nome exclusivo para uma conta de armazenamento com base no seu grupo de recursos. Dentro do grupo de recursos, o nome não é exclusivo se construídos da mesma forma.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

Se precisar de criar um novo nome exclusivo sempre que implementar um modelo e não a intenção de atualizar o recurso, pode utilizar o [utcNow](#utcnow) função com uniqueString. Poderia usar essa abordagem num ambiente de teste. Por exemplo, veja [utcNow](#utcNow).

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres contendo 13 carateres.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) devolve resultados de uniquestring:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "uniqueRG": {
            "value": "[uniqueString(resourceGroup().id)]",
            "type" : "string"
        },
        "uniqueDeploy": {
            "value": "[uniqueString(resourceGroup().id, deployment().name)]",
            "type" : "string"
        }
    }
}
```

## <a name="uri"></a>uri

`uri (baseUri, relativeUri)`

Cria um URI absoluto, combinando o baseUri e a cadeia de caracteres relativeUri.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| baseUri |Sim |string |A cadeia de caracteres do uri de base. |
| relativeUri |Sim |string |A cadeia uri relativo para adicionar na cadeia de caracteres do uri de base. |

O valor para o **baseUri** parâmetro pode incluir um ficheiro específico, mas apenas o caminho de base é utilizado ao construir o URI. Por exemplo, passando `http://contoso.com/resources/azuredeploy.json` como os resultados de parâmetro baseUri num URI de base de `http://contoso.com/resources/`.

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres que representa o URI absoluto para os valores de bases e relativos.

### <a name="examples"></a>Exemplos

O exemplo seguinte mostra como construir uma ligação para um modelo aninhado com base no valor do modelo principal.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) mostra como utilizar o uri, uriComponent e uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

Codifica um URI.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToEncode |Sim |string |O valor a codificar. |

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres do URI codificado valor.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) mostra como utilizar o uri, uriComponent e uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Devolve uma cadeia de caracteres de um URI codificado em valor.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Sim |string |O URI codificado valor a converter numa cadeia. |

### <a name="return-value"></a>Valor de retorno

Uma cadeia descodificada de URI codificado valor.

### <a name="examples"></a>Exemplos

O seguinte procedimento [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) mostra como utilizar o uri, uriComponent e uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

O resultado do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Value |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Devolve o valor de datetime (UTC) atual no formato especificado. Se não for fornecido nenhum formato, é utilizado o formato ISO 8601 (yyyyMMddTHHmmssZ). **Esta função só pode ser utilizada no valor predefinido para um parâmetro.**

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| Formato |Não |string |O URI codificado valor a converter numa cadeia. A utilização de uma [cadeias de caracteres de formato padrão](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [cadeias de caracteres de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Observações

Só pode utilizar esta função dentro de uma expressão para o valor predefinido de um parâmetro. Usando essa função em qualquer parte num modelo devolve um erro. A função não é permitida em outras partes do modelo, porque retorna um valor diferente cada vez que for chamado. Implementar o mesmo modelo com os mesmos parâmetros não produzir de forma fiável os mesmos resultados.

Se utilizar o [opção para voltar a implementar uma implementação com êxito anteriormente](resource-group-template-deploy-rest.md#redeploy-when-deployment-fails)e a implementação anterior inclui um parâmetro que utiliza utcNow, o parâmetro não é reavaliado. Em vez disso, o valor do parâmetro da implementação anterior é automaticamente reutilizado a implementação de reversão.

Cuidado-se de que a Reimplementar um modelo que se baseia na função utcNow para um valor predefinido. Quando voltar a implementar e não fornecer um valor para o parâmetro, a função é reavaliada. Se quiser atualizar um recurso existente, em vez de criar um novo, passe o valor do parâmetro da implementação anterior.

### <a name="return-value"></a>Valor de retorno

O valor de datetime UTC atual.

### <a name="examples"></a>Exemplos

O modelo de exemplo seguinte mostra os diferentes formatos para o valor de datetime.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcValue": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        },
        "utcShortValue": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "utcCustomValue": {
            "type": "string",
            "defaultValue": "[utcNow('M d')]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "utcOutput": {
            "type": "string",
            "value": "[parameters('utcValue')]"
        },
        "utcShortOutput": {
            "type": "string",
            "value": "[parameters('utcShortValue')]"
        },
        "utcCustomOutput": {
            "type": "string",
            "value": "[parameters('utcCustomValue')]"
        }
    }
}
```

A saída do exemplo anterior varia para cada implementação, mas será semelhante a:

| Name | Tipo | Value |
| ---- | ---- | ----- |
| utcOutput | string | 20190305T175318Z |
| utcShortOutput | string | 03/05/2019 |
| utcCustomOutput | string | 3 5 |

O exemplo seguinte mostra como utilizar um valor da função, ao definir um valor de etiqueta.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "rgName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```

## <a name="next-steps"></a>Passos Seguintes
* Para obter uma descrição das secções num modelo Azure Resource Manager, consulte [modelos Authoring Azure Resource Manager](resource-group-authoring-templates.md).
* Para intercalar vários modelos, veja [utilizar modelos ligados com o Azure Resource Manager](resource-group-linked-templates.md).
* Para fazer a iteração de um número especificado de vezes ao criar um tipo de recurso, consulte [criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md).
* Para ver como implementar o modelo que criou, veja [implementar uma aplicação com o modelo Azure Resource Manager](resource-group-template-deploy.md).

