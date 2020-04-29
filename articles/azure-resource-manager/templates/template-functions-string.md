---
title: Funções do modelo - cadeia
description: Descreve as funções a utilizar num modelo de Gestor de Recursos Azure para trabalhar com cordas.
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: c0517375b273384f263e8ba421995d4afb6c193b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80982419"
---
# <a name="string-functions-for-arm-templates"></a>Funções de corda para modelos ARM

O Gestor de Recursos fornece as seguintes funções para trabalhar com cordas nos seus modelos de Gestor de Recursos Azure (ARM):

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64Tostring](#base64tostring)
* [concat](#concat)
* [contém](#contains)
* [dataUri](#datauri)
* [datauritostring](#datauritostring)
* [vazio](#empty)
* [terminaCom](#endswith)
* [primeiro](#first)
* [formato](#format)
* [guid](#guid)
* [indexof](#indexof)
* [última](#last)
* [últimoIndexOf](#lastindexof)
* [comprimento](#length)
* [newGuid](#newguid)
* [padLeft](#padleft)
* [substituir](#replace)
* [saltar](#skip)
* [dividir](#split)
* [começaCom](#startswith)
* [string](#string)
* [substring](#substring)
* [tomar](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [aparar](#trim)
* [único String](#uniquestring)
* [uri](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)

## <a name="base64"></a>base64

`base64(inputString)`

Devolve a representação base64 da cadeia de entrada.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| inputString |Sim |string |O valor para voltar como uma representação base64. |

### <a name="return-value"></a>Valor devolvido

Uma corda contendo a representação base64.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) de exemplo seguinte mostra como usar a função base64.

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| paraStringOutput | String | Um dois três |
| toJsonOutput | Objeto | {"um": "a", "dois": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Converte uma representação base64 num objeto JSON.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| base64Valor |Sim |string |A representação base64 para converter-se em um objeto JSON. |

### <a name="return-value"></a>Valor devolvido

Um objeto JSON.

### <a name="examples"></a>Exemplos

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) de exemplo utiliza a função base64ToJson para converter um valor base64:

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| paraStringOutput | String | Um dois três |
| toJsonOutput | Objeto | {"um": "a", "dois": "b"} |

## <a name="base64tostring"></a>base64Tostring

`base64ToString(base64Value)`

Converte uma representação base64 numa corda.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| base64Valor |Sim |string |A representação base64 para converter-se em uma corda. |

### <a name="return-value"></a>Valor devolvido

Uma cadeia do valor base64 convertido.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) de exemplo seguinte utiliza a função base64ToString para converter um valor base64:

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| paraStringOutput | String | Um dois três |
| toJsonOutput | Objeto | {"um": "a", "dois": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

Combina múltiplos valores de cordas e devolve a corda concatenada, ou combina várias matrizes e devolve a matriz concatenada.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |corda ou matriz |A primeira corda ou matriz para a concatenação. |
| argumentos adicionais |Não |corda ou matriz |Cordas ou matrizes adicionais em ordem sequencial de concatenação. |

Esta função pode assumir qualquer número de argumentos, e pode aceitar cordas ou matrizes para os parâmetros. No entanto, não é possível fornecer matrizes e cordas para parâmetros. As cordas só são concatenadas com outras cordas.

### <a name="return-value"></a>Valor devolvido

Uma cadeia ou uma série de valores concatenados.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) de exemplo seguinte mostra como combinar dois valores de corda e devolver uma corda concatenada.

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| concatOutput | String | prefixo-5yj4yjf5mbg72 |

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) de exemplo mostra como combinar duas matrizes.

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| regressar | Matriz | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>contém

`contains (container, itemToFind)`

Verifica se uma matriz contém um valor, um objeto contém uma chave, ou uma corda contém um substring. A comparação de cordas é sensível a casos. No entanto, ao testar se um objeto contém uma chave, a comparação é insensível a casos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| contentor |Sim |matriz, objeto ou corda |O valor que contém o valor a encontrar. |
| itemToFind |Sim |corda ou int |O valor a encontrar. |

### <a name="return-value"></a>Valor devolvido

**É verdade** se o item for encontrado; caso contrário, **Falso.**

### <a name="examples"></a>Exemplos

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) de exemplo mostra como usar contém com diferentes tipos:

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| cordaTrue | Booleano | Verdadeiro |
| cadeiaFalso | Booleano | Falso |
| objetoVerdadeiro | Booleano | Verdadeiro |
| objetoFalso | Booleano | Falso |
| arrayTrue | Booleano | Verdadeiro |
| arrayFalso | Booleano | Falso |

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Converte um valor para um URI de dados.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToConverter |Sim |string |O valor para converter para um URI de dados. |

### <a name="return-value"></a>Valor devolvido

Uma corda formatada como um URI de dados.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) de exemplo seguinte converte um valor para um URI de dados, e converte um URI de dados para uma cadeia:

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| dataUriOutput | String | dados:texto/planície;charset=utf8;base64,SGVsbG8= |
| paraStringOutput | String | Olá, mundo! |

## <a name="datauritostring"></a>datauritostring

`dataUriToString(dataUriToConvert)`

Converte um valor formatado URI de dados para uma corda.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| datauriToconverter |Sim |string |Os dados URI valorizam a conversão. |

### <a name="return-value"></a>Valor devolvido

Uma corda contendo o valor convertido.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) de exemplo seguinte converte um valor para um URI de dados, e converte um URI de dados para uma cadeia:

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| dataUriOutput | String | dados:texto/planície;charset=utf8;base64,SGVsbG8= |
| paraStringOutput | String | Olá, mundo! |

## <a name="empty"></a>vazio

`empty(itemToTest)`

Determina se uma matriz, objeto ou corda está vazia.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| itemToTest |Sim |matriz, objeto ou corda |O valor para verificar se está vazio. |

### <a name="return-value"></a>Valor devolvido

Devoluções **Verdadeiras** se o valor estiver vazio; caso contrário, **Falso.**

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) de exemplo seguinte verifica se uma matriz, objeto e corda estão vazios.

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| matrizVazio | Booleano | Verdadeiro |
| objetoVazio | Booleano | Verdadeiro |
| cordaVazio | Booleano | Verdadeiro |

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Determina se uma corda termina com um valor. A comparação é insensível a casos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sim |string |O valor que contém o item a encontrar. |
| stringToFind |Sim |string |O valor a encontrar. |

### <a name="return-value"></a>Valor devolvido

**Verdade** se o último personagem ou caracteres da corda correspondem ao valor; caso contrário, **Falso.**

### <a name="examples"></a>Exemplos

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) de exemplo mostra como usar os arranquesCom e terminaCom funções:

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| começaTrue | Booleano | Verdadeiro |
| começaCapTrue | Booleano | Verdadeiro |
| começaFalso | Booleano | Falso |
| terminaTrue | Booleano | Verdadeiro |
| terminaCapTrue | Booleano | Verdadeiro |
| terminaFalso | Booleano | Falso |

## <a name="first"></a>primeiro

`first(arg1)`

Devolve o primeiro personagem da corda, ou primeiro elemento da matriz.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz ou corda |O valor para recuperar o primeiro elemento ou personagem. |

### <a name="return-value"></a>Valor devolvido

Uma sequência do primeiro personagem, ou do tipo (corda, int, matriz ou objeto) do primeiro elemento de uma matriz.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) de exemplo seguinte mostra como usar a primeira função com uma matriz e uma corda.

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayOutput | String | um |
| cadeiaOutput | String | O |

## <a name="format"></a>formato

`format(formatString, arg1, arg2, ...)`

Cria uma cadeia formatada a partir de valores de entrada.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| formatString | Sim | string | A cadeia de formato composto. |
| arg1 | Sim | corda, inteiro, ou booleano | O valor a incluir na corda formatada. |
| argumentos adicionais | Não | corda, inteiro, ou booleano | Valores adicionais a incluir na corda formatada. |

### <a name="remarks"></a>Observações

Use esta função para formatar uma corda no seu modelo. Utiliza as mesmas opções de formatação que o método [System.String.Format](/dotnet/api/system.string.format) em .NET.

### <a name="examples"></a>Exemplos

O modelo de exemplo seguinte mostra como usar a função de formato.

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| formatoTeste | String | Olá, Utilizador. Número formato: 8.175.133 |

## <a name="guid"></a>guid

`guid(baseString, ...)`

Cria um valor no formato de um identificador globalmente único com base nos valores fornecidos como parâmetros.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| baseString |Sim |string |O valor utilizado na função hash para criar o GUID. |
| parâmetros adicionais, conforme necessário |Não |string |Pode adicionar o máximo de cordas necessárias para criar o valor que especifica o nível de singularidade. |

### <a name="remarks"></a>Observações

Esta função é útil quando precisa de criar um valor no formato de um identificador globalmente único. Fornece valores parâmetros que limitam o âmbito de singularidade para o resultado. Pode especificar se o nome é exclusivo até à subscrição, grupo de recursos ou implementação.

O valor devolvido não é uma corda aleatória, mas sim o resultado de uma função de hash nos parâmetros. O valor devolvido tem 36 caracteres de comprimento. Não é globalmente único. Para criar um novo GUID que não se baseie no valor do haxixe dos parâmetros, use a nova função [Guid.](#newguid)

Os seguintes exemplos mostram como usar o guid para criar um valor único para níveis comumente usados.

Exclusivo com vista à subscrição

```json
"[guid(subscription().subscriptionId)]"
```

Exclusivo do grupo de recursos

```json
"[guid(resourceGroup().id)]"
```

Um espaço único para a implantação de um grupo de recursos

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Valor devolvido

Uma cadeia contendo 36 caracteres no formato de um identificador globalmente único.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) de exemplo seguinte retorna os resultados do guia:

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

## <a name="indexof"></a>indexof

`indexOf(stringToSearch, stringToFind)`

Devolve a primeira posição de um valor dentro de uma corda. A comparação é insensível a casos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sim |string |O valor que contém o item a encontrar. |
| stringToFind |Sim |string |O valor a encontrar. |

### <a name="return-value"></a>Valor devolvido

Um inteiro que representa a posição do item a encontrar. O valor é baseado em zero. Se o item não for encontrado, -1 será devolvido.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) de exemplo seguinte mostra como usar as funções indexOf e lastIndexOf:

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| primeiroT | int | 0 |
| últimoT | int | 3 |
| primeiraString | int | 2 |
| última String | int | 0 |
| não Encontrado | int | -1 |

## <a name="last"></a>última

`last (arg1)`

Devolve o último personagem da corda, ou o último elemento da matriz.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz ou corda |O valor para recuperar o último elemento ou personagem. |

### <a name="return-value"></a>Valor devolvido

Uma sequência do último personagem, ou do tipo (corda, int, matriz ou objeto) do último elemento numa matriz.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) de exemplo seguinte mostra como usar a última função com uma matriz e uma corda.

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayOutput | String | três |
| cadeiaOutput | String | e |

## <a name="lastindexof"></a>últimoIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Devolve a última posição de um valor dentro de uma corda. A comparação é insensível a casos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sim |string |O valor que contém o item a encontrar. |
| stringToFind |Sim |string |O valor a encontrar. |

### <a name="return-value"></a>Valor devolvido

Um inteiro que representa a última posição do item a encontrar. O valor é baseado em zero. Se o item não for encontrado, -1 será devolvido.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) de exemplo seguinte mostra como usar as funções indexOf e lastIndexOf:

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| primeiroT | int | 0 |
| últimoT | int | 3 |
| primeiraString | int | 2 |
| última String | int | 0 |
| não Encontrado | int | -1 |

## <a name="length"></a>length

`length(string)`

Devolve o número de caracteres numa corda, elementos numa matriz ou propriedades de nível raiz num objeto.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz, corda ou objeto |A matriz a utilizar para obter o número de elementos, a corda a utilizar para obter o número de caracteres, ou o objeto a usar para obter o número de propriedades de nível raiz. |

### <a name="return-value"></a>Valor devolvido

Um int. 

### <a name="examples"></a>Exemplos

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) de exemplo mostra como usar o comprimento com uma matriz e uma corda:

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayLength | int | 3 |
| stringLength | int | 13 |
| objetoComprimento | int | 4 |

## <a name="newguid"></a>newGuid

`newGuid()`

Devolve um valor no formato de um identificador globalmente único. **Esta função só pode ser utilizada no valor predefinido para um parâmetro.**

### <a name="remarks"></a>Observações

Só pode utilizar esta função dentro de uma expressão para o valor padrão de um parâmetro. A utilização desta função em qualquer outro lugar de um modelo devolve um erro. A função não é permitida noutras partes do modelo porque devolve um valor diferente cada vez que é chamada. A implantação do mesmo modelo com os mesmos parâmetros não produziria os mesmos resultados.

A nova função Guid difere da função [guida](#guid) porque não leva nenhum parâmetro. Quando se chama guia com o mesmo parâmetro, devolve o mesmo identificador de cada vez. Use guia quando precisa de gerar o mesmo GUID de forma fiável para um ambiente específico. Use newGuid quando precisar de um identificador diferente de cada vez, como a implantação de recursos para um ambiente de teste.

A nova função Guid utiliza a [estrutura Guid](/dotnet/api/system.guid) no .NET Framework para gerar o identificador globalmente único.

Se utilizar a [opção de reimplantar uma implementação mais recente](rollback-on-error.md)e bem sucedida, e a implementação anterior inclui um parâmetro que utiliza o newGuid, o parâmetro não é reavaliado. Em vez disso, o valor do parâmetro da implementação anterior é automaticamente reutilizado na implementação de reversão.

Num ambiente de teste, poderá ser necessário mobilizar repetidamente recursos que apenas vivem por um curto período de tempo. Em vez de construir nomes únicos, pode usar o newGuid com string [único](#uniquestring) para criar nomes únicos.

Tenha cuidado ao recolocar um modelo que dependa da função newGuid para um valor predefinido. Quando reimplanta e não fornece um valor para o parâmetro, a função é reavaliada. Se quiser atualizar um recurso existente em vez de criar um novo, passe o valor do parâmetro a partir da implementação anterior.

### <a name="return-value"></a>Valor devolvido

Uma cadeia contendo 36 caracteres no formato de um identificador globalmente único.

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

A saída do exemplo anterior varia para cada implantação, mas será semelhante a:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| guidOutput | string | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

O exemplo que se segue utiliza a nova função Guid para criar um nome único para uma conta de armazenamento. Este modelo pode funcionar para o ambiente de teste onde a conta de armazenamento existe por um curto período de tempo e não é reimplantada.

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

A saída do exemplo anterior varia para cada implantação, mas será semelhante a:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| nomeSaída | string | storagenziwvyru7uxie |


## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

Devolve uma corda alinhada à direita adicionando caracteres à esquerda até atingir o comprimento total especificado.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| valueToPad |Sim |corda ou int |O valor para alinhar à direita. |
| totalComprimento |Sim |int |O número total de caracteres na corda devolvida. |
| acolchoadoPersonage |Não |personagem único |O carácter a utilizar para o acolchoamento esquerdo até que o comprimento total seja atingido. O valor padrão é um espaço. |

Se a cadeia original for mais comprida do que o número de caracteres a revestir, não são adicionados caracteres.

### <a name="return-value"></a>Valor devolvido

Uma corda com pelo menos o número de caracteres especificados.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) de exemplo seguinte mostra como remar o valor do parâmetro fornecido pelo utilizador adicionando o caráter zero até atingir o número total de caracteres. 

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| cadeiaOutput | String | 0000000123 |

## <a name="replace"></a>substituir

`replace(originalString, oldString, newString)`

Devolve uma nova corda com todos os casos de uma corda substituída por outra corda.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| String original |Sim |string |O valor que tem todos os casos de uma corda substituída por outra corda. |
| oldString |Sim |string |A corda a ser removida da corda original. |
| newString |Sim |string |A corda para adicionar no lugar da corda removida. |

### <a name="return-value"></a>Valor devolvido

Uma corda com os caracteres substituídos.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) de exemplo seguinte mostra como remover todos os traços da corda fornecida pelo utilizador e como substituir parte da corda por outra corda.

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| primeiraSaída | String | 1231231234 |
| segundaSaída | String | 123-123-xxxx |

## <a name="skip"></a>saltar

`skip(originalValue, numberToSkip)`

Devolve uma corda com todos os caracteres após o número especificado de caracteres, ou uma matriz com todos os elementos após o número especificado de elementos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| originalValue |Sim |matriz ou corda |A matriz ou corda para usar para saltar. |
| númeroToSkip |Sim |int |O número de elementos ou caracteres a saltar. Se este valor for de 0 ou menos, todos os elementos ou caracteres do valor são devolvidos. Se for maior do que o comprimento da matriz ou corda, uma matriz ou corda vazia é devolvida. |

### <a name="return-value"></a>Valor devolvido

Uma matriz ou corda.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) de exemplo seguinte ignora o número especificado de elementos na matriz e o número especificado de caracteres numa corda.

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayOutput | Matriz | ["três"] |
| cadeiaOutput | String | dois três |

## <a name="split"></a>dividir

`split(inputString, delimiter)`

Devolve uma série de cordas que contém as subcordas da cadeia de entrada que são delimitadas pelos delimitadores especificados.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| inputString |Sim |string |A corda para partir. |
| delimitador |Sim |cadeia ou matriz de cordas |O delimitador a utilizar para dividir a corda. |

### <a name="return-value"></a>Valor devolvido

Uma variedade de cordas.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) de exemplo seguinte divide a cadeia de entrada com uma vírgula, e com uma vírgula ou um ponto e vírgula.

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| primeiraSaída | Matriz | ["um", "dois", "três"] |
| segundaSaída | Matriz | ["um", "dois", "três"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Determina se uma corda começa com um valor. A comparação é insensível a casos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sim |string |O valor que contém o item a encontrar. |
| stringToFind |Sim |string |O valor a encontrar. |

### <a name="return-value"></a>Valor devolvido

**Verdade** se o primeiro personagem ou caracteres da corda correspondem ao valor; caso contrário, **Falso.**

### <a name="examples"></a>Exemplos

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) de exemplo mostra como usar os arranquesCom e terminaCom funções:

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| começaTrue | Booleano | Verdadeiro |
| começaCapTrue | Booleano | Verdadeiro |
| começaFalso | Booleano | Falso |
| terminaTrue | Booleano | Verdadeiro |
| terminaCapTrue | Booleano | Verdadeiro |
| terminaFalso | Booleano | Falso |

## <a name="string"></a>string

`string(valueToConvert)`

Converte o valor especificado numa corda.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| valorToConverter |Sim | Qualquer |O valor para converter em corda. Qualquer tipo de valor pode ser convertido, incluindo objetos e matrizes. |

### <a name="return-value"></a>Valor devolvido

Uma cadeia do valor convertido.

### <a name="examples"></a>Exemplos

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) de exemplo mostra como converter diferentes tipos de valores em cordas:

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| objetoSaída | String | {"valueA":10,"valueB":"Exemplo Texto"} |
| arrayOutput | String | ["a", "b", "c"] |
| intOutput | String | 5 |

## <a name="substring"></a>substring

`substring(stringToParse, startIndex, length)`

Devolve um substring que começa na posição de caracteres especificada e contém o número especificado de caracteres.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToParse |Sim |string |A corda original a partir da qual a subcorda é extraída. |
| startIndex |Não |int |A posição de personagem inicial de base zero para o substring. |
| length |Não |int |O número de caracteres para o substring. Deve referir-se a um local dentro da corda. Deve ser zero ou maior. |

### <a name="return-value"></a>Valor devolvido

O substring. Ou uma corda vazia se o comprimento for zero.

### <a name="remarks"></a>Observações

A função falha quando a subcadeia se estende para além da extremidade da corda, ou quando o comprimento é inferior a zero. O exemplo seguinte falha com o erro "Os parâmetros de índice e comprimento devem referir-se a uma localização dentro da corda. O parâmetro de índice: '0', o parâmetro de comprimento: '11', o comprimento do parâmetro da corda: '10'."

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) de exemplo seguinte extrai uma subcadeia de um parâmetro.

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| substringOutput | String | dois |

## <a name="take"></a>tomar

`take(originalValue, numberToTake)`

Devolve uma corda com o número especificado de caracteres desde o início da cadeia, ou uma matriz com o número especificado de elementos desde o início da matriz.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| originalValue |Sim |matriz ou corda |A matriz ou corda para retirar os elementos. |
| númeroToTake |Sim |int |O número de elementos ou caracteres a tomar. Se este valor for de 0 ou menos, uma matriz ou corda vazia é devolvida. Se for maior do que o comprimento da dada matriz ou corda, todos os elementos da matriz ou corda são devolvidos. |

### <a name="return-value"></a>Valor devolvido

Uma matriz ou corda.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) de exemplo seguinte retira o número especificado de elementos da matriz, e caracteres de uma corda.

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| arrayOutput | Matriz | ["um", "dois"] |
| cadeiaOutput | String | em |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

Converte a corda especificada para minúscula.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToChange |Sim |string |O valor para converter em minúsculas. |

### <a name="return-value"></a>Valor devolvido

A corda converteu-se em minúscula.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) de exemplo seguinte converte um valor de parâmetro para minúscula e para maiúscula.

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| toLowerOutput | String | Um dois três |
| toUpperOutput | String | Um dois três |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

Converte a corda especificada para a caixa superior.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToChange |Sim |string |O valor para converter em maiúscula. |

### <a name="return-value"></a>Valor devolvido

A corda converteu-se em maiúscula.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) de exemplo seguinte converte um valor de parâmetro para minúscula e para maiúscula.

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| toLowerOutput | String | Um dois três |
| toUpperOutput | String | Um dois três |

## <a name="trim"></a>aparar

`trim (stringToTrim)`

Remove todos os caracteres de espaço branco líder esistantes e seguidos da corda especificada.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToTrim |Sim |string |O valor a aparar. |

### <a name="return-value"></a>Valor devolvido

A corda sem liderar e seguir personagens do espaço branco.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) de exemplo seguinte apara os caracteres do espaço branco do parâmetro.

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| regressar | String | Um dois três |

## <a name="uniquestring"></a>único String

`uniqueString (baseString, ...)`

Cria uma cadeia de hash determinista baseada nos valores fornecidos como parâmetros. 

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| baseString |Sim |string |O valor utilizado na função hash para criar uma cadeia única. |
| parâmetros adicionais, conforme necessário |Não |string |Pode adicionar o máximo de cordas necessárias para criar o valor que especifica o nível de singularidade. |

### <a name="remarks"></a>Observações

Esta função é útil quando precisa de criar um nome único para um recurso. Fornece valores parâmetros que limitam o âmbito de singularidade para o resultado. Pode especificar se o nome é exclusivo até à subscrição, grupo de recursos ou implementação. 

O valor devolvido não é uma corda aleatória, mas sim o resultado de uma função de hash. O valor devolvido tem 13 caracteres de comprimento. Não é globalmente único. Você pode querer combinar o valor com um prefixo da sua convenção de nomeação para criar um nome que seja significativo. O exemplo seguinte mostra o formato do valor devolvido. O valor real varia em consoantes pelos parâmetros fornecidos.

    tcvhiyu5h2o5o

Os exemplos seguintes mostram como usar string único para criar um valor único para níveis comumente usados.

Exclusivo com vista à subscrição

```json
"[uniqueString(subscription().subscriptionId)]"
```

Exclusivo do grupo de recursos

```json
"[uniqueString(resourceGroup().id)]"
```

Um espaço único para a implantação de um grupo de recursos

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

O exemplo que se segue mostra como criar um nome único para uma conta de armazenamento baseada no seu grupo de recursos. Dentro do grupo de recursos, o nome não é único se construído da mesma forma.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

Se precisar de criar um novo nome único cada vez que implementar um modelo, e não pretender atualizar o recurso, pode utilizar a função [utcNow](template-functions-date.md#utcnow) com string único. Poderia usar esta abordagem num ambiente de teste. Por exemplo, consulte [utcNow](template-functions-date.md#utcnow).

### <a name="return-value"></a>Valor devolvido

Uma corda contendo 13 caracteres.

### <a name="examples"></a>Exemplos

O [modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) de exemplo seguinte retorna os resultados de uma corda única:

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

Cria um URI absoluto combinando a baseUri e a corda relativaUri.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| baseUri |Sim |string |A corda uri base. Tenha cuidado para observar o comportamento em relação ao manuseamento do corte de rasto ('/'), como descrito seguindo esta tabela.  |
| parenteUri |Sim |string |A corda uri relativa para adicionar à cadeia uri base. |

* Se **baseUri** terminar em um corte de rasto, o resultado é simplesmente **baseUri** seguido por **parenteUri**.

* Se a **baseUri** não acabar num corte de rastos, uma de duas coisas acontece.  

   * Se **baseUri** não tiver cortes (além do "//" perto da frente), o resultado é simplesmente **baseUri** seguido por **relativaUri**.

   * Se **baseUri** tiver alguns cortes, mas não termina com um corte, tudo a partir do último corte para a frente é removido da **baseUri** e o resultado é **baseUri** seguido por **parenteUri**.
     
Eis alguns exemplos:

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```
Para mais detalhes, os parâmetros **baseUri** e **relativosUri** são resolvidos conforme especificado no [RFC 3986, secção 5](https://tools.ietf.org/html/rfc3986#section-5).

### <a name="return-value"></a>Valor devolvido

Uma corda que representa o URI absoluto para a base e valores relativos.

### <a name="examples"></a>Exemplos

O exemplo seguinte mostra como construir uma ligação a um modelo aninhado com base no valor do modelo dos pais.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) de exemplo mostra como usar uri, uriComponent e uriComponentToString:

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentesSaída de produção | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| paraStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

Codifica um URI.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| stringToEncode |Sim |string |O valor para codificar. |

### <a name="return-value"></a>Valor devolvido

Uma cadeia do valor codificado pela URI.

### <a name="examples"></a>Exemplos

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) de exemplo mostra como usar uri, uriComponent e uriComponentToString:

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentesSaída de produção | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| paraStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Devolve uma série de um valor codificado por URI.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| string uriEncoded |Sim |string |O URI codificado valor para converter-se em uma corda. |

### <a name="return-value"></a>Valor devolvido

Uma cadeia de valor codificado de URI descodificada.

### <a name="examples"></a>Exemplos

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) de exemplo mostra como usar uri, uriComponent e uriComponentToString:

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

A saída do exemplo anterior com os valores predefinidos é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentesSaída de produção | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| paraStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="next-steps"></a>Passos seguintes
* Para uma descrição das secções num modelo de Gestor de Recursos Azure, consulte os modelos de [Gestor de Recursos Azure da Autoria](template-syntax.md).
* Para fundir vários modelos, consulte [Utilizar modelos ligados com](linked-templates.md)o Gestor de Recursos Azure .
* Para iterar um número especificado de vezes ao criar um tipo de recurso, consulte [Criar múltiplas instâncias de recursos no Gestor de Recursos Azure](copy-resources.md).
* Para ver como implementar o modelo que criou, consulte [implementar uma aplicação com o modelo de Gestor](deploy-powershell.md)de Recursos Azure .

