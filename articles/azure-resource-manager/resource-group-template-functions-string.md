---
title: Funções de modelo – cadeia de caracteres
description: Descreve as funções a serem usadas em um modelo de Azure Resource Manager para trabalhar com cadeias de caracteres.
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: 37bf03f2934980d143edeec327a0c424216fd8a1
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74784515"
---
# <a name="string-functions-for-azure-resource-manager-templates"></a>Funções de cadeia de caracteres para modelos de Azure Resource Manager

O Gerenciador de recursos fornece as seguintes funções para trabalhar com cadeias de caracteres:

* [Base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [Concat](#concat)
* [terá](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [esvaziá](#empty)
* [endsWith](#endswith)
* [primeiro](#first)
* [ao](#format)
* [volume](#guid)
* [indexOf](#indexof)
* [última](#last)
* [lastIndexOf](#lastindexof)
* [muito](#length)
* [newGuid](#newguid)
* [PadLeft preenche](#padleft)
* [Substitua](#replace)
* [saltar](#skip)
* [dividir](#split)
* [startsWith](#startswith)
* [Strings](#string)
* [Subcadeia](#substring)
* [ter](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [Trim](#trim)
* [uniqueString](#uniquestring)
* [URI](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)
* [utcNow](#utcnow)

## <a name="base64"></a>Base64

`base64(inputString)`

Retorna a representação Base64 da cadeia de caracteres de entrada.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| inputString |Sim |string |O valor a ser retornado como uma representação base64. |

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres que contém a representação base64.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) a seguir mostra como usar a função base64.

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | Um dois três |
| toJsonOutput | Object | {"One": "a", "Two": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Converte uma representação base64 em um objeto JSON.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| base64Value |Sim |string |A representação Base64 a ser convertida em um objeto JSON. |

### <a name="return-value"></a>Valor de retorno

Um objeto JSON.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) a seguir usa a função base64ToJson para converter um valor Base64:

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | Um dois três |
| toJsonOutput | Object | {"One": "a", "Two": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Converte uma representação base64 em uma cadeia de caracteres.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| base64Value |Sim |string |A representação Base64 a ser convertida em uma cadeia de caracteres. |

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres do valor Base64 convertido.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) a seguir usa a função base64ToString para converter um valor Base64:

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | Um dois três |
| toJsonOutput | Object | {"One": "a", "Two": "b"} |

## <a name="concat"></a>Concat

`concat (arg1, arg2, arg3, ...)`

Combina vários valores de cadeia de caracteres e retorna a cadeia de caracteres concatenada, ou combina várias matrizes e retorna a matriz concatenada.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |Cadeia de caracteres ou matriz |A primeira cadeia de caracteres ou matriz para concatenação. |
| argumentos adicionais |Não |Cadeia de caracteres ou matriz |Cadeias de caracteres ou matrizes adicionais em ordem sequencial para concatenação. |

Essa função pode ter qualquer número de argumentos e pode aceitar cadeias de caracteres ou matrizes para os parâmetros. No entanto, você não pode fornecer matrizes e cadeias de caracteres para parâmetros. As cadeias de caracteres são concatenadas apenas com outras cadeias de caracteres.

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres ou matriz de valores concatenados.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) a seguir mostra como combinar dois valores de cadeia de caracteres e retornar uma cadeia de caracteres concatenada.

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| concatOutput | String | prefixo-5yj4yjf5mbg72 |

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) a seguir mostra como combinar duas matrizes.

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| exibir | Array | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>terá

`contains (container, itemToFind)`

Verifica se uma matriz contém um valor, se um objeto contém uma chave ou uma cadeia de caracteres contém uma subcadeia de caracteres. A comparação de cadeia de caracteres diferencia maiúsculas de minúsculas. No entanto, ao testar se um objeto contém uma chave, a comparação não diferencia maiúsculas de minúsculas.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| contentor |Sim |matriz, objeto ou cadeia de caracteres |O valor que contém o valor a ser localizado. |
| itemToFind |Sim |Cadeia de caracteres ou int |O valor a ser localizado. |

### <a name="return-value"></a>Valor de retorno

**True** se o item for encontrado; caso contrário, **false**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) a seguir mostra como usar Contains com tipos diferentes:

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| stringTrue | Bool | Verdadeiro |
| stringFalse | Bool | Falso |
| objecttrue | Bool | Verdadeiro |
| objectfalse | Bool | Falso |
| arrayTrue | Bool | Verdadeiro |
| arrayFalse | Bool | Falso |

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Converte um valor em um URI de dados.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToConvert |Sim |string |O valor a ser convertido em um URI de dados. |

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres formatada como um URI de dados.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) a seguir converte um valor em um URI de dados e converte um URI de dados em uma cadeia de caracteres:

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| dataUriOutput | String | dados: text/plain; charset = UTF8; base64, SGVsbG8 = |
| toStringOutput | String | Olá, Mundo! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Converte um valor formatado de URI de dados em uma cadeia de caracteres.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Sim |string |O valor do URI de dados a ser convertido. |

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres que contém o valor convertido.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) a seguir converte um valor em um URI de dados e converte um URI de dados em uma cadeia de caracteres:

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| dataUriOutput | String | dados: text/plain; charset = UTF8; base64, SGVsbG8 = |
| toStringOutput | String | Olá, Mundo! |

## <a name="empty"></a>Esvaziá

`empty(itemToTest)`

Determina se uma matriz, um objeto ou uma cadeia de caracteres está vazia.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| itemToTest |Sim |matriz, objeto ou cadeia de caracteres |O valor para verificar se ele está vazio. |

### <a name="return-value"></a>Valor de retorno

Retornará **true** se o valor estiver vazio; caso contrário, **false**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) a seguir verifica se uma matriz, um objeto e uma cadeia de caracteres estão vazios.

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayEmpty | Bool | Verdadeiro |
| objectempty | Bool | Verdadeiro |
| stringEmpty | Bool | Verdadeiro |

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Determina se uma cadeia de caracteres termina com um valor. A comparação não diferencia maiúsculas de minúsculas.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sim |string |O valor que contém o item a ser localizado. |
| stringToFind |Sim |string |O valor a ser localizado. |

### <a name="return-value"></a>Valor de retorno

**True** se o último caractere ou caracteres da cadeia de caracteres corresponderem ao valor; caso contrário, **false**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) a seguir mostra como usar as funções StartsWith e EndsWith:

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| startsTrue | Bool | Verdadeiro |
| startsCapTrue | Bool | Verdadeiro |
| startsFalse | Bool | Falso |
| endsTrue | Bool | Verdadeiro |
| endsCapTrue | Bool | Verdadeiro |
| endsFalse | Bool | Falso |

## <a name="first"></a>primeiro

`first(arg1)`

Retorna o primeiro caractere da cadeia de caracteres ou o primeiro elemento da matriz.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz ou cadeia de caracteres |O valor para recuperar o primeiro elemento ou caractere. |

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres do primeiro caractere ou o tipo (cadeia de caracteres, int, matriz ou objeto) do primeiro elemento em uma matriz.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) a seguir mostra como usar a primeira função com uma matriz e uma cadeia de caracteres.

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayOutput | String | um |
| stringOutput | String | Minúscula |

## <a name="format"></a>ao

`format(formatString, arg1, arg2, ...)`

Cria uma cadeia de caracteres formatada a partir de valores de entrada.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| formatString | Sim | string | A cadeia de caracteres de formato composto. |
| arg1 | Sim | Cadeia de caracteres, inteiro ou booliano | O valor a ser incluído na cadeia de caracteres formatada. |
| argumentos adicionais | Não | Cadeia de caracteres, inteiro ou booliano | Valores adicionais a serem incluídos na cadeia de caracteres formatada. |

### <a name="remarks"></a>Observações

Use essa função para formatar uma cadeia de caracteres em seu modelo. Ele usa as mesmas opções de formatação que o método [System. String. Format](/dotnet/api/system.string.format) no .net.

### <a name="examples"></a>Exemplos

O modelo de exemplo a seguir mostra como usar a função Format.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "greeting": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "name": {
            "type": "string",
            "defaultValue": "User"
        },
        "numberToFormat": {
            "type": "int",
            "defaultValue": 8175133
        }
    },
    "resources": [
    ],
    "outputs": {
        "formatTest": {
            "type": "string",
            "value": "[format('{0}, {1}. Formatted number: {2:N0}', parameters('greeting'), parameters('name'), parameters('numberToFormat'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| formatTest | String | Olá, usuário. Número formatado: 8.175.133 |

## <a name="guid"></a>volume

`guid(baseString, ...)`

Cria um valor no formato de um identificador global exclusivo com base nos valores fornecidos como parâmetros.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| basestring |Sim |string |O valor usado na função de hash para criar o GUID. |
| parâmetros adicionais conforme necessário |Não |string |Você pode adicionar quantas cadeias de caracteres forem necessárias para criar o valor que especifica o nível de exclusividade. |

### <a name="remarks"></a>Observações

Essa função é útil quando você precisa criar um valor no formato de um identificador global exclusivo. Você fornece valores de parâmetro que limitam o escopo de exclusividade para o resultado. Você pode especificar se o nome é exclusivo para a assinatura, grupo de recursos ou implantação.

O valor retornado não é uma cadeia de caracteres aleatória, mas sim o resultado de uma função de hash nos parâmetros. O valor retornado tem de 36 a caracteres de comprimento. Não é globalmente exclusivo. Para criar um novo GUID que não seja baseado nesse valor de hash dos parâmetros, use a função [newGuid](#newguid) .

Os exemplos a seguir mostram como usar o GUID para criar um valor exclusivo para níveis comumente usados.

Escopo exclusivo para assinatura

```json
"[guid(subscription().subscriptionId)]"
```

Escopo exclusivo para o grupo de recursos

```json
"[guid(resourceGroup().id)]"
```

Escopo exclusivo para implantação para um grupo de recursos

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres que contém 36 caracteres no formato de um identificador global exclusivo.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) a seguir retorna resultados do GUID:

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

Retorna a primeira posição de um valor dentro de uma cadeia de caracteres. A comparação não diferencia maiúsculas de minúsculas.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sim |string |O valor que contém o item a ser localizado. |
| stringToFind |Sim |string |O valor a ser localizado. |

### <a name="return-value"></a>Valor de retorno

Um inteiro que representa a posição do item a ser localizado. O valor é baseado em zero. Se o item não for encontrado,-1 será retornado.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) a seguir mostra como usar as funções IndexOf e LastIndexOf:

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| primeiro a | Inteiro | 0 |
| último a | Inteiro | 3 |
| nome da primeira | Inteiro | 2 |
| últimastring | Inteiro | 0 |
| notFound | Inteiro | -1 |

## <a name="last"></a>última

`last (arg1)`

Retorna o último caractere da cadeia de caracteres ou o último elemento da matriz.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz ou cadeia de caracteres |O valor para recuperar o último elemento ou caractere. |

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres do último caractere ou o tipo (cadeia de caracteres, int, matriz ou objeto) do último elemento em uma matriz.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) a seguir mostra como usar a última função com uma matriz e uma cadeia de caracteres.

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayOutput | String | três |
| stringOutput | String | Oriental |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Retorna a última posição de um valor dentro de uma cadeia de caracteres. A comparação não diferencia maiúsculas de minúsculas.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sim |string |O valor que contém o item a ser localizado. |
| stringToFind |Sim |string |O valor a ser localizado. |

### <a name="return-value"></a>Valor de retorno

Um inteiro que representa a última posição do item a ser localizado. O valor é baseado em zero. Se o item não for encontrado,-1 será retornado.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) a seguir mostra como usar as funções IndexOf e LastIndexOf:

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| primeiro a | Inteiro | 0 |
| último a | Inteiro | 3 |
| nome da primeira | Inteiro | 2 |
| últimastring | Inteiro | 0 |
| notFound | Inteiro | -1 |

## <a name="length"></a>muito

`length(string)`

Retorna o número de caracteres em uma cadeia de caracteres, elementos em uma matriz ou propriedades de nível raiz em um objeto.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz, Cadeia de caracteres ou objeto |A matriz a ser usada para obter o número de elementos, a cadeia de caracteres a ser usada para obter o número de caracteres ou o objeto a ser usado para obter o número de propriedades no nível raiz. |

### <a name="return-value"></a>Valor de retorno

Um int. 

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) a seguir mostra como usar o comprimento com uma matriz e uma cadeia de caracteres:

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
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "propA": "one",
                "propB": "two",
                "propC": "three",
                "propD": {
                    "propD-1": "sub",
                    "propD-2": "sub"
                }
            }
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
        },
        "objectLength": {
            "type": "int",
            "value": "[length(parameters('objectToTest'))]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayLength | Inteiro | 3 |
| stringLength | Inteiro | 13 |
| ObjectLength | Inteiro | 4 |

## <a name="newguid"></a>newGuid

`newGuid()`

Retorna um valor no formato de um identificador global exclusivo. **Essa função só pode ser usada no valor padrão para um parâmetro.**

### <a name="remarks"></a>Observações

Você só pode usar essa função dentro de uma expressão para o valor padrão de um parâmetro. O uso dessa função em qualquer outro lugar em um modelo retorna um erro. A função não é permitida em outras partes do modelo porque ela retorna um valor diferente cada vez que é chamada. Implantar o mesmo modelo com os mesmos parâmetros não produziria com confiança os mesmos resultados.

A função newGuid difere da função [GUID](#guid) porque ela não assume nenhum parâmetro. Quando você chama o GUID com o mesmo parâmetro, ele retorna o mesmo identificador a cada vez. Use o GUID quando precisar gerar com confiança o mesmo GUID para um ambiente específico. Use newGuid quando precisar de um identificador diferente a cada vez, como a implantação de recursos em um ambiente de teste.

Se você usar a [opção para reimplantar uma implantação bem-sucedida anterior](rollback-on-error.md)e a implantação anterior incluir um parâmetro que usa newGuid, o parâmetro não será reavaliado. Em vez disso, o valor do parâmetro da implantação anterior é automaticamente reutilizado na implantação de reversão.

Em um ambiente de teste, talvez seja necessário implantar repetidamente os recursos que só moram por um curto período de tempo. Em vez de construir nomes exclusivos, você pode usar newGuid com [uniquestring](#uniquestring) para criar nomes exclusivos.

Tenha cuidado ao reimplantar um modelo que dependa da função newGuid para um valor padrão. Quando você reimplanta e não fornece um valor para o parâmetro, a função é reavaliada. Se você quiser atualizar um recurso existente em vez de criar um novo, passe o valor do parâmetro da implantação anterior.

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres que contém 36 caracteres no formato de um identificador global exclusivo.

### <a name="examples"></a>Exemplos

O modelo de exemplo a seguir mostra um parâmetro com um novo identificador.

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

A saída do exemplo anterior varia de acordo com cada implantação, mas será semelhante a:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| guidOutput | string | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

O exemplo a seguir usa a função newGuid para criar um nome exclusivo para uma conta de armazenamento. Esse modelo pode funcionar para o ambiente de teste em que a conta de armazenamento existe por um curto período e não é reimplantada.

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

A saída do exemplo anterior varia de acordo com cada implantação, mas será semelhante a:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| nameOutput | string | storagenziwvyru7uxie |


## <a name="padleft"></a>PadLeft preenche

`padLeft(valueToPad, totalLength, paddingCharacter)`

Retorna uma cadeia de caracteres alinhada à direita adicionando caracteres à esquerda até atingir o comprimento total especificado.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| valueToPad |Sim |Cadeia de caracteres ou int |O valor para alinhar à direita. |
| totalLength |Sim |int |O número total de caracteres na cadeia de caracteres retornada. |
| paddingCharacter |Não |caractere único |O caractere a ser usado para o preenchimento à esquerda até que o comprimento total seja atingido. O valor padrão é um espaço. |

Se a cadeia de caracteres original for maior do que o número de caracteres a ser pad, nenhum caractere será adicionado.

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres com pelo menos o número de caracteres especificados.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) a seguir mostra como preencher o valor do parâmetro fornecido pelo usuário adicionando o caractere zero até atingir o número total de caracteres. 

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| stringOutput | String | 0000000123 |

## <a name="replace"></a>Substitua

`replace(originalString, oldString, newString)`

Retorna uma nova cadeia de caracteres com todas as instâncias de uma cadeia de caracteres substituídas por outra cadeia de caracteres.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| OriginalString |Sim |string |O valor que tem todas as instâncias de uma cadeia de caracteres substituídas por outra cadeia de caracteres. |
| Cadeiadecaracteresantiga |Sim |string |A cadeia de caracteres a ser removida da cadeia de caracteres original. |
| Novacadeiadecaracteres |Sim |string |A cadeia de caracteres a ser adicionada no lugar da cadeia de caracteres removida. |

### <a name="return-value"></a>Valor de retorno

Uma cadeia com os caracteres substituídos.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) a seguir mostra como remover todos os traços da cadeia de caracteres fornecida pelo usuário e como substituir parte da cadeia de caracteres por outra cadeia de caracteres.

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
        "secondOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| firstOutput | String | 1231231234 |
| secondOutput | String | 123-123-xxxx |

## <a name="skip"></a>saltar

`skip(originalValue, numberToSkip)`

Retorna uma string com todos os caracteres após o número especificado de caracteres ou uma matriz com todos os elementos após o número especificado de elementos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| originalValue |Sim |matriz ou cadeia de caracteres |A matriz ou a cadeia de caracteres a ser usada para ignorar. |
| numberToSkip |Sim |int |O número de elementos ou caracteres a serem ignorados. Se esse valor for 0 ou menos, todos os elementos ou caracteres no valor serão retornados. Se for maior do que o comprimento da matriz ou da cadeia de caracteres, uma matriz ou cadeia de caracteres vazia será retornada. |

### <a name="return-value"></a>Valor de retorno

Uma matriz ou cadeia de caracteres.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) a seguir ignora o número especificado de elementos na matriz e o número especificado de caracteres em uma cadeia de caracteres.

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayOutput | Array | ["três"] |
| stringOutput | String | 2 3 |

## <a name="split"></a>dividir

`split(inputString, delimiter)`

Retorna uma matriz de cadeias de caracteres que contém as subcadeias da cadeia de caracteres de entrada delimitadas pelos delimitadores especificados.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| inputString |Sim |string |A cadeia de caracteres a ser dividida. |
| delimitador |Sim |String ou matriz de cadeias de caracteres |O delimitador a ser usado para dividir a cadeia de caracteres. |

### <a name="return-value"></a>Valor de retorno

Uma matriz de cadeias de caracteres.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) a seguir divide a cadeia de caracteres de entrada com uma vírgula e com uma vírgula ou um ponto-e-vírgula.

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| firstOutput | Array | ["One", "Two", "três"] |
| secondOutput | Array | ["One", "Two", "três"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Determina se uma cadeia de caracteres começa com um valor. A comparação não diferencia maiúsculas de minúsculas.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sim |string |O valor que contém o item a ser localizado. |
| stringToFind |Sim |string |O valor a ser localizado. |

### <a name="return-value"></a>Valor de retorno

**True** se o primeiro caractere ou caracteres da cadeia de caracteres corresponderem ao valor; caso contrário, **false**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) a seguir mostra como usar as funções StartsWith e EndsWith:

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| startsTrue | Bool | Verdadeiro |
| startsCapTrue | Bool | Verdadeiro |
| startsFalse | Bool | Falso |
| endsTrue | Bool | Verdadeiro |
| endsCapTrue | Bool | Verdadeiro |
| endsFalse | Bool | Falso |

## <a name="string"></a>string

`string(valueToConvert)`

Converte o valor especificado em uma cadeia de caracteres.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| valueToConvert |Sim | Qualquer |O valor a ser convertido em cadeia de caracteres. Qualquer tipo de valor pode ser convertido, incluindo objetos e matrizes. |

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres do valor convertido.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) a seguir mostra como converter diferentes tipos de valores em cadeias de caracteres:

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| ObjectOutput | String | {"value": 10, "valorB": "texto de exemplo"} |
| arrayOutput | String | ["a", "b", "c"] |
| intOutput | String | 5 |

## <a name="substring"></a>Subcadeia

`substring(stringToParse, startIndex, length)`

Retorna uma subcadeia de caracteres que começa na posição de caractere especificada e contém o número de caractere especificado.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToParse |Sim |string |A cadeia de caracteres original da qual a subcadeia de caracteres é extraída. |
| startIndex |Não |int |A posição de caractere inicial com base em zero para a subcadeia de caracteres. |
| muito |Não |int |O número de caracteres para a subcadeia. Deve se referir a um local dentro da cadeia de caracteres. Deve ser zero ou maior. |

### <a name="return-value"></a>Valor de retorno

A subcadeia de caracteres. Ou uma cadeia de caracteres vazia se o comprimento for zero.

### <a name="remarks"></a>Observações

A função falha quando a subcadeia de caracteres ultrapassa o final da cadeia de caracteres ou quando length é menor que zero. O exemplo a seguir falha com o erro "os parâmetros de índice e comprimento devem se referir a um local dentro da cadeia de caracteres. O parâmetro de índice: ' 0 ', o parâmetro de comprimento: ' 11 ', o comprimento do parâmetro de cadeia de caracteres: ' 10 '. ".

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) a seguir extrai uma subcadeia de caracteres de um parâmetro.

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| substringOutput | String | dois |

## <a name="take"></a>ter

`take(originalValue, numberToTake)`

Retorna uma cadeia com o número especificado de caracteres do início da cadeia de caracteres ou uma matriz com o número especificado de elementos desde o início da matriz.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| originalValue |Sim |matriz ou cadeia de caracteres |A matriz ou a cadeia de caracteres da qual tirar os elementos. |
| numberToTake |Sim |int |O número de elementos ou caracteres a serem adotados. Se esse valor for 0 ou menos, uma matriz ou cadeia de caracteres vazia será retornada. Se for maior do que o comprimento da matriz ou cadeia de caracteres determinada, todos os elementos na matriz ou na cadeia de caracteres serão retornados. |

### <a name="return-value"></a>Valor de retorno

Uma matriz ou cadeia de caracteres.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) a seguir usa o número especificado de elementos da matriz e os caracteres de uma cadeia.

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayOutput | Array | ["One", "Two"] |
| stringOutput | String | no |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

Converte a cadeia de caracteres especificada em letras minúsculas.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToChange |Sim |string |O valor a ser convertido em minúsculas. |

### <a name="return-value"></a>Valor de retorno

A cadeia de caracteres convertida em minúsculas.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) a seguir converte um valor de parâmetro em minúsculas e em letras maiúsculas.

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| toLowerOutput | String | Um dois três |
| toUpperOutput | String | Um dois três |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

Converte a cadeia de caracteres especificada em letras maiúsculas.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToChange |Sim |string |O valor a ser convertido em letras maiúsculas. |

### <a name="return-value"></a>Valor de retorno

A cadeia de caracteres convertida em maiúsculas.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) a seguir converte um valor de parâmetro em minúsculas e em letras maiúsculas.

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| toLowerOutput | String | Um dois três |
| toUpperOutput | String | Um dois três |

## <a name="trim"></a>Trim

`trim (stringToTrim)`

Remove todos os caracteres de espaço em branco à esquerda e à direita da cadeia de caracteres especificada.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToTrim |Sim |string |O valor a ser cortado. |

### <a name="return-value"></a>Valor de retorno

A cadeia de caracteres sem espaços de espaço em branco à esquerda e à direita.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) a seguir corta os caracteres de espaço em branco do parâmetro.

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| exibir | String | Um dois três |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Cria uma cadeia de caracteres de hash determinística com base nos valores fornecidos como parâmetros. 

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| basestring |Sim |string |O valor usado na função de hash para criar uma cadeia de caracteres exclusiva. |
| parâmetros adicionais conforme necessário |Não |string |Você pode adicionar quantas cadeias de caracteres forem necessárias para criar o valor que especifica o nível de exclusividade. |

### <a name="remarks"></a>Observações

Essa função é útil quando você precisa criar um nome exclusivo para um recurso. Você fornece valores de parâmetro que limitam o escopo de exclusividade para o resultado. Você pode especificar se o nome é exclusivo para a assinatura, grupo de recursos ou implantação. 

O valor retornado não é uma cadeia de caracteres aleatória, mas sim o resultado de uma função de hash. O valor retornado tem 13 caracteres. Não é globalmente exclusivo. Talvez você queira combinar o valor com um prefixo de sua Convenção de nomenclatura para criar um nome que seja significativo. O exemplo a seguir mostra o formato do valor retornado. O valor real varia de acordo com os parâmetros fornecidos.

    tcvhiyu5h2o5o

Os exemplos a seguir mostram como usar uniquestring para criar um valor exclusivo para níveis comumente usados.

Escopo exclusivo para assinatura

```json
"[uniqueString(subscription().subscriptionId)]"
```

Escopo exclusivo para o grupo de recursos

```json
"[uniqueString(resourceGroup().id)]"
```

Escopo exclusivo para implantação para um grupo de recursos

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

O exemplo a seguir mostra como criar um nome exclusivo para uma conta de armazenamento com base em seu grupo de recursos. Dentro do grupo de recursos, o nome não é exclusivo se construído da mesma maneira.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

Se você precisar criar um novo nome exclusivo cada vez que implantar um modelo e não pretender atualizar o recurso, poderá usar a função [UtcNow](#utcnow) com uniquestring. Você pode usar essa abordagem em um ambiente de teste. Para obter um exemplo, consulte [UtcNow](#utcnow).

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres que contém 13 caracteres.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) a seguir retorna os resultados de uniquestring:

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

## <a name="uri"></a>URI

`uri (baseUri, relativeUri)`

Cria um URI absoluto combinando o baseUri e a cadeia de caracteres relativeUri.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| baseUri |Sim |string |A cadeia de caracteres de URI de base. Tome cuidado para observar o comportamento em relação à manipulação da barra à direita ('/'), conforme descrito a seguir nesta tabela.  |
| relativeUri |Sim |string |A cadeia de caracteres de URI relativa a ser adicionada à cadeia de caracteres de URI de base. |

* Se **BaseUri** terminar em uma barra à direita, o resultado será simplesmente **BaseUri** seguido por **relativeUri**.

* Se **BaseUri** não terminar em uma barra à direita, uma das duas coisas ocorrerá.  

   * Se **BaseUri** não tiver nenhuma barra (além da "//" próxima à frente), o resultado será simplesmente **BaseUri** seguido por **relativeUri**.

   * Se **BaseUri** tiver algumas barras, mas não terminar com uma barra, tudo da última barra em diante será removido do **BaseUri** e o resultado será **BaseUri** seguido por **relativeUri**.
     
Eis alguns exemplos:

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```
Para obter detalhes completos, os parâmetros **BaseUri** e **relativeUri** são resolvidos conforme especificado na [RFC 3986, seção 5](https://tools.ietf.org/html/rfc3986#section-5).

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres que representa o URI absoluto para os valores base e relativo.

### <a name="examples"></a>Exemplos

O exemplo a seguir mostra como construir um link para um modelo aninhado com base no valor do modelo pai.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) a seguir mostra como usar URI, UriComponent e uriComponentToString:

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

Codifica um URI.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToEncode |Sim |string |O valor a ser codificado. |

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres do valor codificado de URI.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) a seguir mostra como usar URI, UriComponent e uriComponentToString:

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Retorna uma cadeia de caracteres de um valor codificado de URI.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Sim |string |O valor codificado de URI a ser convertido em uma cadeia de caracteres. |

### <a name="return-value"></a>Valor de retorno

Uma cadeia de caracteres decodificada de valor codificado de URI.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) a seguir mostra como usar URI, UriComponent e uriComponentToString:

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

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Retorna o valor DateTime (UTC) atual no formato especificado. Se nenhum formato for fornecido, o formato ISO 8601 (yyyyMMddTHHmmssZ) será usado. **Essa função só pode ser usada no valor padrão para um parâmetro.**

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| ao |Não |string |O valor codificado de URI a ser convertido em uma cadeia de caracteres. Use cadeias de caracteres de [formato padrão](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [cadeias de caracteres de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Observações

Você só pode usar essa função dentro de uma expressão para o valor padrão de um parâmetro. O uso dessa função em qualquer outro lugar em um modelo retorna um erro. A função não é permitida em outras partes do modelo porque ela retorna um valor diferente cada vez que é chamada. Implantar o mesmo modelo com os mesmos parâmetros não produziria com confiança os mesmos resultados.

Se você usar a [opção para reimplantar uma implantação bem-sucedida anterior](rollback-on-error.md)e a implantação anterior incluir um parâmetro que usa UtcNow, o parâmetro não será reavaliado. Em vez disso, o valor do parâmetro da implantação anterior é automaticamente reutilizado na implantação de reversão.

Tenha cuidado ao reimplantar um modelo que dependa da função utcNow para um valor padrão. Quando você reimplanta e não fornece um valor para o parâmetro, a função é reavaliada. Se você quiser atualizar um recurso existente em vez de criar um novo, passe o valor do parâmetro da implantação anterior.

### <a name="return-value"></a>Valor de retorno

O valor DateTime UTC atual.

### <a name="examples"></a>Exemplos

O modelo de exemplo a seguir mostra formatos diferentes para o valor DateTime.

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

A saída do exemplo anterior varia de acordo com cada implantação, mas será semelhante a:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| utcOutput | string | 20190305T175318Z |
| utcShortOutput | string | 03/05/2019 |
| utcCustomOutput | string | 3 5 |

O exemplo a seguir mostra como usar um valor da função ao definir um valor de marca.

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

## <a name="next-steps"></a>Passos seguintes
* Para obter uma descrição das seções em um modelo de Azure Resource Manager, consulte [criando modelos de Azure Resource Manager](resource-group-authoring-templates.md).
* Para mesclar vários modelos, consulte [usando modelos vinculados com Azure Resource Manager](resource-group-linked-templates.md).
* Para iterar um número especificado de vezes ao criar um tipo de recurso, consulte [criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md).
* Para ver como implantar o modelo que você criou, consulte [implantar um aplicativo com Azure Resource Manager modelo](resource-group-template-deploy.md).

