---
title: Expressão e funções no Azure Data Factory
description: Este artigo fornece informações sobre expressões e funções que você pode usar ao criar data factory entidades.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: 9ef4b569fd8413d2825374c963fb272dd450cf0e
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533127"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Expressões e funções no Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-functions-variables.md)
> * [Versão atual](control-flow-expression-language-functions.md)

Este artigo fornece detalhes sobre expressões e funções com suporte pelo Azure Data Factory. 

## <a name="expressions"></a>Expressões

Os valores JSON na definição podem ser literais ou expressões que são avaliadas em tempo de execução. Por exemplo:  
  
```json
"name": "value"
```

 ou  
  
```json
"name": "@pipeline().parameters.password"
```

As expressões podem aparecer em qualquer lugar em um valor de cadeia de caracteres JSON e sempre resultar em outro valor JSON. Se um valor JSON for uma expressão, o corpo da expressão será extraído removendo o sinal de arroba (\@). Se for necessária uma cadeia de caracteres literal que comece com \@, ela deverá ser substituída usando \@\@. Os exemplos a seguir mostram como as expressões são avaliadas.  
  
|Valor JSON|Resultado|  
|----------------|------------|  
|parâmetro|Os caracteres ' Parameters ' são retornados.|  
|"parâmetros [1]"|Os caracteres ' parameters [1] ' são retornados.|  
|"\@\@"|Uma cadeia de caracteres de 1 caractere que contém '\@' é retornada.|  
|"\@"|Uma cadeia de caracteres de 2 caracteres que contém ' \@' é retornada.|  
  
 As expressões também podem aparecer dentro de cadeias de caracteres, usando um recurso chamado *interpolação de cadeia de caracteres* onde as expressões são encapsuladas em `@{ ... }`. Por exemplo: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Usando a interpolação de cadeia de caracteres, o resultado é sempre uma cadeia de caracteres. Digamos que defini `myNumber` como `42` e `myString` como `foo`:  
  
|Valor JSON|Resultado|  
|----------------|------------|  
|"\@pipeline (). Parameters. myString"| Retorna `foo` como uma cadeia de caracteres.|  
|"\@{pipeline (). Parameters. myString}"| Retorna `foo` como uma cadeia de caracteres.|  
|"\@pipeline (). Parameters. MyNumber"| Retorna `42` como um *número*.|  
|"\@{pipeline (). Parameters. MyNumber}"| Retorna `42` como uma *cadeia de caracteres*.|  
|"A resposta é: @ {pipeline (). Parameters. MyNumber}"| Retorna a cadeia de caracteres `Answer is: 42`.|  
|"\@Concat (' a resposta é: ', String (pipeline (). Parameters. MyNumber))"| Retorna a cadeia de caracteres `Answer is: 42`|  
|"A resposta é: \@\@{pipeline (). Parameters. MyNumber}"| Retorna a cadeia de caracteres `Answer is: @{pipeline().parameters.myNumber}`.|  
  
## <a name="examples"></a>Exemplos

### <a name="a-dataset-with-a-parameter"></a>Um DataSet com um parâmetro
No exemplo a seguir, o BlobDataset usa um parâmetro chamado **path**. Seu valor é usado para definir um valor para a propriedade **FolderPath** usando a expressão: `dataset().path`. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="a-pipeline-with-a-parameter"></a>Um pipeline com um parâmetro
No exemplo a seguir, o pipeline usa os parâmetros **inputPath** e **outputPath** . O **caminho** para o conjunto de valores de blob com parâmetros é definido usando valores desses parâmetros. A sintaxe usada aqui é: `pipeline().parameters.parametername`. 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```
### <a name="tutorial"></a>Tutorial
Este [tutorial](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) orienta você sobre como passar parâmetros entre um pipeline e uma atividade, bem como entre as atividades.

  
## <a name="functions"></a>Funções

Você pode chamar funções dentro de expressões. As seções a seguir fornecem informações sobre as funções que podem ser usadas em uma expressão.  

## <a name="string-functions"></a>Funções de cadeia  

Para trabalhar com cadeias de caracteres, você pode usar essas funções de cadeia de caracteres e também algumas [funções de coleta](#collection-functions).
As funções de cadeia de caracteres funcionam apenas em cadeias de caracteres.

| Função de cadeia de caracteres | Tarefa |
| --------------- | ---- |
| [Concat](control-flow-expression-language-functions.md#concat) | Combine duas ou mais cadeias de caracteres e retorne a cadeia combinada. |
| [endsWith](control-flow-expression-language-functions.md#endswith) | Verifique se uma cadeia de caracteres termina com a subcadeia de caracteres especificada. |
| [volume](control-flow-expression-language-functions.md#guid) | Gere um GUID (identificador global exclusivo) como uma cadeia de caracteres. |
| [indexOf](control-flow-expression-language-functions.md#indexof) | Retorna a posição inicial de uma subcadeia de caracteres. |
| [lastIndexOf](control-flow-expression-language-functions.md#lastindexof) | Retorna a posição inicial da última ocorrência de uma subcadeia de caracteres. |
| [Substitua](control-flow-expression-language-functions.md#replace) | Substitui uma subcadeia de caracteres pela cadeia de caracteres especificada e retorna a cadeia de caracteres atualizada. |
| [dividir](control-flow-expression-language-functions.md#split) | Retorna uma matriz que contém subcadeias de caracteres, separadas por vírgulas, de uma cadeia de caracteres maior com base em um caractere delimitador especificado na cadeia de caracteres original. |
| [startsWith](control-flow-expression-language-functions.md#startswith) | Verifique se uma cadeia de caracteres começa com uma subcadeia de caracteres específica. |
| [Subcadeia](control-flow-expression-language-functions.md#substring) | Retornar caracteres de uma cadeia de caracteres, começando da posição especificada. |
| [toLower](control-flow-expression-language-functions.md#toLower) | Retornar uma cadeia de caracteres em formato em minúsculas. |
| [toUpper](control-flow-expression-language-functions.md#toUpper) | Retornar uma cadeia de caracteres em formato maiúsculo. |
| [Trim](control-flow-expression-language-functions.md#trim) | Remova o espaço em branco à esquerda e à direita de uma cadeia de caracteres e retorne a cadeia de caracteres atualizada. |

## <a name="collection-functions"></a>Funções de coleção

Para trabalhar com coleções, geralmente matrizes, cadeias de caracteres e, às vezes, dicionários, você pode usar essas funções de coleção.

| Função de coleção | Tarefa |
| ------------------- | ---- |
| [terá](control-flow-expression-language-functions.md#contains) | Verifique se uma coleção tem um item específico. |
| [esvaziá](control-flow-expression-language-functions.md#empty) | Verifique se uma coleção está vazia. |
| [primeiro](control-flow-expression-language-functions.md#first) | Retornar o primeiro item de uma coleção. |
| [interseção](control-flow-expression-language-functions.md#intersection) | Retorna uma coleção que tem *apenas* os itens comuns nas coleções especificadas. |
| [aderir](control-flow-expression-language-functions.md#join) | Retornar uma cadeia de caracteres que tenha *todos* os itens de uma matriz, separados pelo caractere especificado. |
| [última](control-flow-expression-language-functions.md#last) | Retorna o último item de uma coleção. |
| [muito](control-flow-expression-language-functions.md#length) | Retornar o número de itens em uma cadeia de caracteres ou matriz. |
| [saltar](control-flow-expression-language-functions.md#skip) | Remover itens da frente de uma coleção e retornar todos os *outros* itens. |
| [ter](control-flow-expression-language-functions.md#take) | Retornar itens da frente de uma coleção. |
| [unida](control-flow-expression-language-functions.md#union) | Retorna uma coleção que tem *todos* os itens das coleções especificadas. | 

## <a name="logical-functions"></a>Funções lógicas  

Essas funções são úteis dentro das condições, elas podem ser usadas para avaliar qualquer tipo de lógica.  
  
| Função de comparação lógica | Tarefa |
| --------------------------- | ---- |
| [e](control-flow-expression-language-functions.md#and) | Verifique se todas as expressões são verdadeiras. |
| [seja](control-flow-expression-language-functions.md#equals) | Verifique se ambos os valores são equivalentes. |
| [grande](control-flow-expression-language-functions.md#greater) | Verifique se o primeiro valor é maior que o segundo valor. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | Verifique se o primeiro valor é maior ou igual ao segundo valor. |
| [que](control-flow-expression-language-functions.md#if) | Verifique se uma expressão é verdadeira ou falsa. Com base no resultado, retorna um valor especificado. |
| [inferiores](control-flow-expression-language-functions.md#less) | Verifique se o primeiro valor é menor que o segundo valor. |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | Verifique se o primeiro valor é menor ou igual ao segundo valor. |
| [válido](control-flow-expression-language-functions.md#not) | Verifique se uma expressão é falsa. |
| [or](control-flow-expression-language-functions.md#or) | Verifique se pelo menos uma expressão é verdadeira. |
  
## <a name="conversion-functions"></a>Funções de conversão  

 Essas funções são usadas para converter entre cada um dos tipos nativos no idioma:  
-   string
-   número inteiro
-   float
-   boolean
-   Storage
-   OldValues

| Função de conversão | Tarefa |
| ------------------- | ---- |
| [variedade](control-flow-expression-language-functions.md#array) | Retornar uma matriz de uma única entrada especificada. Para várias entradas, consulte [CreateArray](control-flow-expression-language-functions.md#createArray). |
| [Base64](control-flow-expression-language-functions.md#base64) | Retornar a versão codificada em base64 para uma cadeia de caracteres. |
| [base64ToBinary](control-flow-expression-language-functions.md#base64ToBinary) | Retornar a versão binária para uma cadeia de caracteres codificada em base64. |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | Retornar a versão de cadeia de caracteres para uma cadeia de caracteres codificada em base64. |
| [binário](control-flow-expression-language-functions.md#binary) | Retornar a versão binária para um valor de entrada. |
| [bool](control-flow-expression-language-functions.md#bool) | Retornar a versão booliana para um valor de entrada. |
| [COALESCE](control-flow-expression-language-functions.md#coalesce) | Retorna o primeiro valor não nulo de um ou mais parâmetros. |
| [createArray](control-flow-expression-language-functions.md#createArray) | Retornar uma matriz de várias entradas. |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | Retornar o URI de dados para um valor de entrada. |
| [dataUriToBinary](control-flow-expression-language-functions.md#dataUriToBinary) | Retornar a versão binária para um URI de dados. |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | Retornar a versão da cadeia de caracteres para um URI de dados. |
| [decodeBase64](control-flow-expression-language-functions.md#decodeBase64) | Retornar a versão de cadeia de caracteres para uma cadeia de caracteres codificada em base64. |
| [decodeDataUri](control-flow-expression-language-functions.md#decodeDataUri) | Retornar a versão binária para um URI de dados. |
| [decodeUriComponent](control-flow-expression-language-functions.md#decodeUriComponent) | Retorne uma cadeia de caracteres que substitui o caractere de escape por versões decodificadas. |
| [encodeUriComponent](control-flow-expression-language-functions.md#encodeUriComponent) | Retornar uma cadeia de caracteres que substitui caracteres não seguros de URL por caracteres de escape. |
| [barra](control-flow-expression-language-functions.md#float) | Retornar um número de ponto flutuante para um valor de entrada. |
| [inteiro](control-flow-expression-language-functions.md#int) | Retornar a versão de inteiro para uma cadeia de caracteres. |
| [JSON](control-flow-expression-language-functions.md#json) | Retorna o valor ou o objeto do tipo JavaScript Object Notation (JSON) para uma cadeia de caracteres ou XML. |
| [Strings](control-flow-expression-language-functions.md#string) | Retornar a versão da cadeia de caracteres para um valor de entrada. |
| [uriComponent](control-flow-expression-language-functions.md#uriComponent) | Retorne a versão codificada em URI para um valor de entrada, substituindo caracteres não seguros por URL por caracteres de escape. |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | Retornar a versão binária para uma cadeia de caracteres codificada em URI. |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | Retornar a versão de cadeia de caracteres para uma cadeia de caracteres codificada em URI. |
| [xml](control-flow-expression-language-functions.md#xml) | Retornar a versão XML de uma cadeia de caracteres. |
| [XPath](control-flow-expression-language-functions.md#xpath) | Marque XML para nós ou valores que correspondam a uma expressão XPath (linguagem de caminho XML) e retorne os nós ou valores correspondentes. |

## <a name="math-functions"></a>Funções matemáticas  
 Essas funções podem ser usadas para qualquer um dos tipos de números: **inteiros** e **floats**.  

| Função Math | Tarefa |
| ------------- | ---- |
| [agrega](control-flow-expression-language-functions.md#add) | Retornar o resultado da adição de dois números. |
| [marca](control-flow-expression-language-functions.md#div) | Retornar o resultado da divisão de dois números. |
| [maximizar](control-flow-expression-language-functions.md#max) | Retorna o valor mais alto de um conjunto de números ou uma matriz. |
| [min](control-flow-expression-language-functions.md#min) | Retorna o valor mais baixo de um conjunto de números ou uma matriz. |
| [resto](control-flow-expression-language-functions.md#mod) | Retornar o resto da divisão de dois números. |
| [Mul](control-flow-expression-language-functions.md#mul) | Retorne o produto da multiplicação de dois números. |
| [Rand](control-flow-expression-language-functions.md#rand) | Retornar um inteiro aleatório de um intervalo especificado. |
| [amplitude](control-flow-expression-language-functions.md#range) | Retornar uma matriz de inteiros que inicia de um número inteiro especificado. |
| [projeto](control-flow-expression-language-functions.md#sub) | Retorne o resultado da subtração do segundo número do primeiro número. |
  
## <a name="date-functions"></a>Funções de data  

| Função de data ou hora | Tarefa |
| --------------------- | ---- |
| [addDays](control-flow-expression-language-functions.md#addDays) | Adicione um número de dias a um carimbo de data/hora. |
| [addHours](control-flow-expression-language-functions.md#addHours) | Adicione um número de horas a um carimbo de data/hora. |
| [addminutos](control-flow-expression-language-functions.md#addMinutes) | Adicione um número de minutos a um carimbo de data/hora. |
| [hipersegundos](control-flow-expression-language-functions.md#addSeconds) | Adicione um número de segundos a um carimbo de data/hora. |
| [addToTime](control-flow-expression-language-functions.md#addToTime) | Adicione um número de unidades de tempo a um carimbo de data/hora. Consulte também [Getfuturtime](control-flow-expression-language-functions.md#getFutureTime). |
| [convertFromUtc](control-flow-expression-language-functions.md#convertFromUtc) | Converta um carimbo de data/hora de UTC (tempo Universal Coordenado) para o fuso horário de destino. |
| [convertTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | Converta um carimbo de data/hora do fuso horário de origem para o fuso horário de destino. |
| [convertToUtc](control-flow-expression-language-functions.md#convertToUtc) | Converta um carimbo de data/hora do fuso horário de origem em UTC (tempo Universal Coordenado). |
| [dayOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | Retorna o dia do componente do mês de um carimbo de data/hora. |
| [dayOfWeek](control-flow-expression-language-functions.md#dayOfWeek) | Retorna o dia do componente da semana de um carimbo de data/hora. |
| [dayOfYear](control-flow-expression-language-functions.md#dayOfYear) | Retorna o dia do componente de ano de um carimbo de data/hora. |
| [formatDateTime](control-flow-expression-language-functions.md#formatDateTime) | Retornar a data de um carimbo de hora. |
| [getfuturtime](control-flow-expression-language-functions.md#getFutureTime) | Retornar o carimbo de data/hora atual mais as unidades de tempo especificadas. Consulte também [addToTime](control-flow-expression-language-functions.md#addToTime). |
| [getpastetime](control-flow-expression-language-functions.md#getPastTime) | Retorna o carimbo de data/hora atual menos as unidades de tempo especificadas. Consulte também [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime). |
| [startOfDay](control-flow-expression-language-functions.md#startOfDay) | Retorna o início do dia para um carimbo de data/hora. |
| [startOfHour](control-flow-expression-language-functions.md#startOfHour) | Retornar o início da hora para um carimbo de data/hora. |
| [startOfMonth](control-flow-expression-language-functions.md#startOfMonth) | Retornar o início do mês para um carimbo de data/hora. |
| [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime) | Subtraia um número de unidades de tempo de um carimbo de data/hora. Veja também [Getpastetime](control-flow-expression-language-functions.md#getPastTime). |
| [tiques](control-flow-expression-language-functions.md#ticks) | Retorna o valor da propriedade de `ticks` para um carimbo de data/hora especificado. |
| [utcNow](control-flow-expression-language-functions.md#utcNow) | Retornar o carimbo de data/hora atual como uma cadeia de caracteres. |

## <a name="function-reference"></a>Referência de função

Esta seção lista todas as funções disponíveis em ordem alfabética.

<a name="add"></a>

### <a name="add"></a>adicionar

Retornar o resultado da adição de dois números.

```
add(<summand_1>, <summand_2>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | Sim | Inteiro, float ou misto | Os números a serem adicionados |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | -----| ----------- |
| > *de soma de resultados de* < | Inteiro ou float | O resultado da adição dos números especificados |
||||

*Exemplo*

Este exemplo adiciona os números especificados:

```
add(1, 1.5)
```

E retorna este resultado: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Adicione um número de dias a um carimbo de data/hora.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de data/hora*> | Sim | Cadeia | A cadeia de caracteres que contém o carimbo de data/hora |
| <*dias*> | Sim | Número inteiro | O número positivo ou negativo de dias para adicionar |
| *formato* de <> | Não | Cadeia | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*atualizado-carimbo de data/hora*> | Cadeia | O carimbo de data/hora mais o número de dias especificado  |
||||

*Exemplo 1*

Este exemplo adiciona 10 dias ao carimbo de data/hora especificado:

```
addDays('2018-03-15T13:00:00Z', 10)
```

E retorna este resultado: `"2018-03-25T00:00:0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco dias do carimbo de data/hora especificado:

```
addDays('2018-03-15T00:00:00Z', -5)
```

E retorna este resultado: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Adicione um número de horas a um carimbo de data/hora.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de data/hora*> | Sim | Cadeia | A cadeia de caracteres que contém o carimbo de data/hora |
| <*horas*> | Sim | Número inteiro | O número positivo ou negativo de horas para adicionar |
| *formato* de <> | Não | Cadeia | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*atualizado-carimbo de data/hora*> | Cadeia | O carimbo de data/hora mais o número especificado de horas  |
||||

*Exemplo 1*

Este exemplo adiciona 10 horas ao carimbo de data/hora especificado:

```
addHours('2018-03-15T00:00:00Z', 10)
```

E retorna este resultado: `"2018-03-15T10:00:0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco horas do carimbo de data/hora especificado:

```
addHours('2018-03-15T15:00:00Z', -5)
```

E retorna este resultado: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Adicione um número de minutos a um carimbo de data/hora.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de data/hora*> | Sim | Cadeia | A cadeia de caracteres que contém o carimbo de data/hora |
| <*minutos*> | Sim | Número inteiro | O número positivo ou negativo de minutos a serem adicionados |
| *formato* de <> | Não | Cadeia | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*atualizado-carimbo de data/hora*> | Cadeia | O carimbo de data/hora mais o número especificado de minutos |
||||

*Exemplo 1*

Este exemplo adiciona 10 minutos ao carimbo de data/hora especificado:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

E retorna este resultado: `"2018-03-15T00:20:00.0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco minutos do carimbo de data/hora especificado:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

E retorna este resultado: `"2018-03-15T00:15:00.0000000Z"`

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Adicione um número de segundos a um carimbo de data/hora.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de data/hora*> | Sim | Cadeia | A cadeia de caracteres que contém o carimbo de data/hora |
| <*segundos*> | Sim | Número inteiro | O número positivo ou negativo de segundos a serem adicionados |
| *formato* de <> | Não | Cadeia | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*atualizado-carimbo de data/hora*> | Cadeia | O carimbo de data/hora mais o número de segundos especificado  |
||||

*Exemplo 1*

Este exemplo adiciona 10 segundos ao carimbo de data/hora especificado:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

E retorna este resultado: `"2018-03-15T00:00:10.0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco segundos para o carimbo de data/hora especificado:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

E retorna este resultado: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Adicione um número de unidades de tempo a um carimbo de data/hora.
Consulte também [Getfuturtime ()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de data/hora*> | Sim | Cadeia | A cadeia de caracteres que contém o carimbo de data/hora |
| *intervalo* de <> | Sim | Número inteiro | O número de unidades de tempo especificadas a serem adicionadas |
| <de *unidade* time> | Sim | Cadeia | A unidade de tempo a ser usada com o *intervalo*: "segundo", "minuto", "hora", "dia", "semana", "mês", "ano" |
| *formato* de <> | Não | Cadeia | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*atualizado-carimbo de data/hora*> | Cadeia | O carimbo de data/hora mais o número especificado de unidades de tempo  |
||||

*Exemplo 1*

Este exemplo adiciona um dia ao carimbo de data/hora especificado:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

E retorna este resultado: `"2018-01-02T00:00:00.0000000Z"`

*Exemplo 2*

Este exemplo adiciona um dia ao carimbo de data/hora especificado:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

E retorna o resultado usando o formato "D" opcional: `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>e

Verifique se todas as expressões são verdadeiras.
Retornar true quando todas as expressões forem verdadeiras ou retornar false quando pelo menos uma expressão for false.

```
and(<expression1>, <expression2>, ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>,... | Sim | Booleano | As expressões a serem verificadas |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | -----| ----------- |
| true ou false | Booleano | Retorna true quando todas as expressões são true. Retorna false quando pelo menos uma expressão é false. |
||||

*Exemplo 1*

Esses exemplos verificam se os valores Boolianos especificados são todos verdadeiros:

```
and(true, true)
and(false, true)
and(false, false)
```

E retorna estes resultados:

* Primeiro exemplo: as duas expressões são true; portanto, retorna `true`.
* Segundo exemplo: uma expressão é false; portanto, retorna `false`.
* Terceiro exemplo: as duas expressões são false; portanto, retorna `false`.

*Exemplo 2*

Esses exemplos verificam se as expressões especificadas são todas verdadeiras:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

E retorna estes resultados:

* Primeiro exemplo: as duas expressões são true; portanto, retorna `true`.
* Segundo exemplo: uma expressão é false; portanto, retorna `false`.
* Terceiro exemplo: as duas expressões são false; portanto, retorna `false`.

<a name="array"></a>

### <a name="array"></a>array

Retornar uma matriz de uma única entrada especificada.
Para várias entradas, consulte [CreateArray ()](#createArray).

```
array('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A cadeia de caracteres para criar uma matriz |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<*valor*>] | Matriz | Uma matriz que contém a entrada especificada única |
||||

*Exemplo*

Este exemplo cria uma matriz da cadeia de caracteres "Olá":

```
array('hello')
```

E retorna este resultado: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Retornar a versão codificada em base64 para uma cadeia de caracteres.

```
base64('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A cadeia de caracteres de entrada |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*Base64-cadeia de caracteres*> | Cadeia | A versão codificada em base64 para a cadeia de caracteres de entrada |
||||

*Exemplo*

Este exemplo converte a cadeia de caracteres "Hello" em uma cadeia de caracteres codificada em base64:

```
base64('hello')
```

E retorna este resultado: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Retornar a versão binária para uma cadeia de caracteres codificada em base64.

```
base64ToBinary('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A cadeia de caracteres codificada em base64 a ser convertida |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*Binary-for-Base64-string*> | Cadeia | A versão binária para a cadeia de caracteres codificada em base64 |
||||

*Exemplo*

Este exemplo converte a cadeia de caracteres codificada em base64 "aGVsbG8 =" em uma cadeia de caracteres binária:

```
base64ToBinary('aGVsbG8=')
```

E retorna esse resultado:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Retorne a versão da cadeia de caracteres para uma cadeia de caracteres codificada em base64, decodificando efetivamente a cadeia de caracteres base64.
Use essa função em vez de [decodeBase64 ()](#decodeBase64).
Embora ambas as funções funcionem da mesma maneira, `base64ToString()` é preferida.

```
base64ToString('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A cadeia de caracteres codificada em base64 a ser decodificada |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*decodificado-Base64-cadeia de caracteres*> | Cadeia | A versão de cadeia de caracteres para uma cadeia de caracteres codificada em base64 |
||||

*Exemplo*

Este exemplo converte a cadeia de caracteres codificada em base64 "aGVsbG8 =" em apenas uma cadeia de caracteres:

```
base64ToString('aGVsbG8=')
```

E retorna este resultado: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

Retornar a versão binária de uma cadeia de caracteres.

```
binary('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A cadeia de caracteres a ser convertida |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*binário-para-valor-de-entrada*> | Cadeia | A versão binária da cadeia de caracteres especificada |
||||

*Exemplo*

Este exemplo converte a cadeia de caracteres "Hello" em uma cadeia de caracteres binária:

```
binary('hello')
```

E retorna esse resultado:

`"0110100001100101011011000110110001101111"`

<a name="bool"></a>

### <a name="bool"></a>booleano

Retornar a versão booliana de um valor.

```
bool(<value>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Qualquer | O valor a ser convertido |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | A versão booliana para o valor especificado |
||||

*Exemplo*

Esses exemplos convertem os valores especificados para valores Boolianos:

```
bool(1)
bool(0)
```

E retorna estes resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="coalesce"></a>

### <a name="coalesce"></a>COALESCE

Retorna o primeiro valor não nulo de um ou mais parâmetros.
Cadeias de caracteres vazias, matrizes vazias e objetos vazios não são nulos.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>,... | Sim | Any, pode misturar tipos | Um ou mais itens a serem verificados como nulos |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*primeiro-item não nulo*> | Qualquer | O primeiro item ou valor que não é nulo. Se todos os parâmetros forem nulos, essa função retornará NULL. |
||||

*Exemplo*

Esses exemplos retornam o primeiro valor não nulo dos valores especificados, ou NULL quando todos os valores são nulos:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

E retorna estes resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `"hello"`
* Terceiro exemplo: `null`

<a name="concat"></a>

### <a name="concat"></a>Concat

Combine duas ou mais cadeias de caracteres e retorne a cadeia combinada.

```
concat('<text1>', '<text2>', ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*texto1*>, <*Texto2*>,... | Sim | Cadeia | Pelo menos duas cadeias de caracteres para combinar |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*text1text2...* > | Cadeia | A cadeia de caracteres criada com base nas cadeias de entrada combinadas |
||||

*Exemplo*

Este exemplo combina as cadeias de caracteres "Olá" e "mundo":

```
concat('Hello', 'World')
```

E retorna este resultado: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>Contém

Verifique se uma coleção tem um item específico.
Retornar true quando o item for encontrado ou retornar false quando não for encontrado.
Essa função diferencia maiúsculas de minúsculas.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Especificamente, essa função funciona nesses tipos de coleção:

* Uma *cadeia de caracteres* para localizar uma *subcadeia de caracteres*
* Uma *matriz* para localizar um *valor*
* Um *dicionário* para localizar uma *chave*

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *coleção* de <> | Sim | Cadeia de caracteres, matriz ou dicionário | A coleção a ser verificada |
| <*valor*> | Sim | Cadeia de caracteres, matriz ou dicionário, respectivamente | O item a ser localizado |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retornar true quando o item for encontrado. Retorna falso quando não encontrado. |
||||

*Exemplo 1*

Este exemplo verifica a cadeia de caracteres "Olá, mundo" para a subcadeia de caracteres "World" e retorna true:

```
contains('hello world', 'world')
```

*Exemplo 2*

Este exemplo verifica a cadeia de caracteres "Olá, mundo" para a subcadeia de caracteres "universo" e retorna false:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Converta um carimbo de data/hora de UTC (tempo Universal Coordenado) para o fuso horário de destino.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de data/hora*> | Sim | Cadeia | A cadeia de caracteres que contém o carimbo de data/hora |
| <*destinationTimeZone*> | Sim | Cadeia | O nome do fuso horário de destino. Para nomes de fuso horário, consulte [valores de índice de fuso horário da Microsoft](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), mas talvez seja necessário remover qualquer Pontuação do nome do fuso horário. |
| *formato* de <> | Não | Cadeia | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*convertido-carimbo de data/hora*> | Cadeia | O carimbo de data/hora convertido no fuso horário de destino |
||||

*Exemplo 1*

Este exemplo converte um carimbo de data/hora no fuso horário especificado:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

E retorna este resultado: `"2018-01-01T00:00:00.0000000"`

*Exemplo 2*

Este exemplo converte um carimbo de data/hora no fuso horário e no formato especificados:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

E retorna este resultado: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Converta um carimbo de data/hora do fuso horário de origem para o fuso horário de destino.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de data/hora*> | Sim | Cadeia | A cadeia de caracteres que contém o carimbo de data/hora |
| <*sourceTimeZone*> | Sim | Cadeia | O nome do fuso horário de origem. Para nomes de fuso horário, consulte [valores de índice de fuso horário da Microsoft](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), mas talvez seja necessário remover qualquer Pontuação do nome do fuso horário. |
| <*destinationTimeZone*> | Sim | Cadeia | O nome do fuso horário de destino. Para nomes de fuso horário, consulte [valores de índice de fuso horário da Microsoft](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), mas talvez seja necessário remover qualquer Pontuação do nome do fuso horário. |
| *formato* de <> | Não | Cadeia | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*convertido-carimbo de data/hora*> | Cadeia | O carimbo de data/hora convertido no fuso horário de destino |
||||

*Exemplo 1*

Este exemplo converte o fuso horário de origem para o fuso horário de destino:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

E retorna este resultado: `"2018-01-01T00:00:00.0000000"`

*Exemplo 2*

Este exemplo converte um fuso horário para o fuso horário e o formato especificados:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

E retorna este resultado: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Converta um carimbo de data/hora do fuso horário de origem em UTC (tempo Universal Coordenado).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de data/hora*> | Sim | Cadeia | A cadeia de caracteres que contém o carimbo de data/hora |
| <*sourceTimeZone*> | Sim | Cadeia | O nome do fuso horário de origem. Para nomes de fuso horário, consulte [valores de índice de fuso horário da Microsoft](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), mas talvez seja necessário remover qualquer Pontuação do nome do fuso horário. |
| *formato* de <> | Não | Cadeia | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*convertido-carimbo de data/hora*> | Cadeia | O carimbo de data/hora convertido em UTC |
||||

*Exemplo 1*

Este exemplo converte um carimbo de data/hora em UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

E retorna este resultado: `"2018-01-01T08:00:00.0000000Z"`

*Exemplo 2*

Este exemplo converte um carimbo de data/hora em UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

E retorna este resultado: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Retornar uma matriz de várias entradas.
Para matrizes de entrada única, consulte [matriz ()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>,... | Sim | Any, mas não misto | Pelo menos dois itens para criar a matriz |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>,...] | Matriz | A matriz criada a partir de todos os itens de entrada |
||||

*Exemplo*

Este exemplo cria uma matriz com base nessas entradas:

```
createArray('h', 'e', 'l', 'l', 'o')
```

E retorna este resultado: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Retornar um URI (Uniform Resource Identifier) de dados para uma cadeia de caracteres.

```
dataUri('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A cadeia de caracteres a ser convertida |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*URI de dados*> | Cadeia | O URI de dados para a cadeia de caracteres de entrada |
||||

*Exemplo*

Este exemplo cria um URI de dados para a cadeia de caracteres "Olá":

```
dataUri('hello')
```

E retorna este resultado: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Retornar a versão binária para um URI (Uniform Resource Identifier) de dados.
Use essa função em vez de [decodeDataUri ()](#decodeDataUri).
Embora ambas as funções funcionem da mesma maneira, `dataUriBinary()` é preferida.

```
dataUriToBinary('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | O URI de dados a ser convertido |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*Binary-for-Data-uri*> | Cadeia | A versão binária para o URI de dados |
||||

*Exemplo*

Este exemplo cria uma versão binária para este URI de dados:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E retorna esse resultado:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Retornar a versão da cadeia de caracteres para um URI (Uniform Resource Identifier) de dados.

```
dataUriToString('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | O URI de dados a ser convertido |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| *cadeia de caracteres <para-data-uri*> | Cadeia | A versão da cadeia de caracteres para o URI de dados |
||||

*Exemplo*

Este exemplo cria uma cadeia de caracteres para este URI de dados:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E retorna este resultado: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Retorna o dia do mês de um carimbo de data/hora.

```
dayOfMonth('<timestamp>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de data/hora*> | Sim | Cadeia | A cadeia de caracteres que contém o carimbo de data/hora |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*dia do mês*> | Número inteiro | O dia do mês do carimbo de data/hora especificado |
||||

*Exemplo*

Este exemplo retorna o número para o dia do mês deste carimbo de data/hora:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

E retorna este resultado: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Retorna o dia da semana de um carimbo de data/hora.

```
dayOfWeek('<timestamp>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de data/hora*> | Sim | Cadeia | A cadeia de caracteres que contém o carimbo de data/hora |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*dia da semana*> | Número inteiro | O dia da semana do carimbo de data/hora especificado em que domingo é 0, segunda-feira é 1 e assim por diante |
||||

*Exemplo*

Este exemplo retorna o número para o dia da semana deste carimbo de data/hora:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

E retorna este resultado: `3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Retorna o dia do ano a partir de um carimbo de data/hora.

```
dayOfYear('<timestamp>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de data/hora*> | Sim | Cadeia | A cadeia de caracteres que contém o carimbo de data/hora |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*dia do ano*> | Número inteiro | O dia do ano a partir do carimbo de data/hora especificado |
||||

*Exemplo*

Este exemplo retorna o número do dia do ano deste carimbo de data/hora:

```
dayOfYear('2018-03-15T13:27:36Z')
```

E retorna este resultado: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64

Retorne a versão da cadeia de caracteres para uma cadeia de caracteres codificada em base64, decodificando efetivamente a cadeia de caracteres base64.
Considere o uso de [base64ToString ()](#base64ToString) em vez de `decodeBase64()`.
Embora ambas as funções funcionem da mesma maneira, `base64ToString()` é preferida.

```
decodeBase64('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A cadeia de caracteres codificada em base64 a ser decodificada |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*decodificado-Base64-cadeia de caracteres*> | Cadeia | A versão de cadeia de caracteres para uma cadeia de caracteres codificada em base64 |
||||

*Exemplo*

Este exemplo cria uma cadeia de caracteres para uma cadeia de caracteres codificada em base64:

```
decodeBase64('aGVsbG8=')
```

E retorna este resultado: `"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Retornar a versão binária para um URI (Uniform Resource Identifier) de dados.
Considere o uso de [dataUriToBinary ()](#dataUriToBinary)em vez de `decodeDataUri()`.
Embora ambas as funções funcionem da mesma maneira, `dataUriToBinary()` é preferida.

```
decodeDataUri('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A cadeia de caracteres de URI de dados para decodificação |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*Binary-for-Data-uri*> | Cadeia | A versão binária para uma cadeia de caracteres de URI de dados |
||||

*Exemplo*

Este exemplo retorna a versão binária para este URI de dados:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E retorna esse resultado:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Retorne uma cadeia de caracteres que substitui o caractere de escape por versões decodificadas.

```
decodeUriComponent('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A cadeia com os caracteres de escape a serem decodificados |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| < *-URI decodificado*> | Cadeia | A cadeia atualizada com os caracteres de escape decodificados |
||||

*Exemplo*

Este exemplo substitui os caracteres de escape nesta cadeia de caracteres por versões decodificadas:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

E retorna este resultado: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Retornar o resultado inteiro da divisão de dois números.
Para obter o resultado restante, consulte [mod ()](#mod).

```
div(<dividend>, <divisor>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*dividendo*> | Sim | Inteiro ou float | O número a ser dividido pelo *divisor* |
| > de <*divisor* | Sim | Inteiro ou float | O número que divide o *dividendo*, mas não pode ser 0 |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*quociente-resultado*> | Número inteiro | O resultado inteiro da divisão do primeiro número pelo segundo número |
||||

*Exemplo*

Ambos os exemplos dividem o primeiro número pelo segundo número:

```
div(10, 5)
div(11, 5)
```

E retornar esse resultado: `2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Retornar uma versão codificada do URI (Uniform Resource Identifier) para uma cadeia de caracteres, substituindo caracteres não seguros por URL por caracteres de escape.
Considere o uso de [URIComponent ()](#uriComponent)em vez de `encodeUriComponent()`.
Embora ambas as funções funcionem da mesma maneira, `uriComponent()` é preferida.

```
encodeUriComponent('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A cadeia de caracteres a ser convertida em formato codificado em URI |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*URI codificado*> | Cadeia | A cadeia de caracteres codificada em URI com caracteres de escape |
||||

*Exemplo*

Este exemplo cria uma versão codificada em URI para esta cadeia de caracteres:

```
encodeUriComponent('https://contoso.com')
```

E retorna este resultado: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>vazio

Verifique se uma coleção está vazia.
Retornar true quando a coleção estiver vazia ou retornar false quando não estiver vazio.

```
empty('<collection>')
empty([<collection>])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *coleção* de <> | Sim | Cadeia de caracteres, matriz ou objeto | A coleção a ser verificada |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retorna true quando a coleção está vazia. Retornar false quando não estiver vazio. |
||||

*Exemplo*

Esses exemplos verificam se as coleções especificadas estão vazias:

```
empty('')
empty('abc')
```

E retorna estes resultados:

* Primeiro exemplo: passa uma cadeia de caracteres vazia, portanto, a função retorna `true`.
* Segundo exemplo: passa a cadeia de caracteres "ABC", portanto, a função retorna `false`.

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Verifique se uma cadeia de caracteres termina com uma subcadeia de caracteres específica.
Retorna true quando a subcadeia de caracteres é encontrada ou retorna false quando não encontrada.
Essa função não diferencia maiúsculas de minúsculas.

```
endsWith('<text>', '<searchText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| > de *texto* < | Sim | Cadeia | A cadeia de caracteres a ser verificada |
| <*procurartexto*> | Sim | Cadeia | A subcadeia de caracteres final a ser localizada |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false  | Booleano | Retorna verdadeiro quando a subcadeia de caracteres final é encontrada. Retorna falso quando não encontrado. |
||||

*Exemplo 1*

Este exemplo verifica se a cadeia de caracteres "Olá, mundo" termina com a cadeia de caracteres "mundo":

```
endsWith('hello world', 'world')
```

E retorna este resultado: `true`

*Exemplo 2*

Este exemplo verifica se a cadeia de caracteres "Olá, mundo" termina com a cadeia de caracteres "universo":

```
endsWith('hello world', 'universe')
```

E retorna este resultado: `false`

<a name="equals"></a>

### <a name="equals"></a>é igual a

Verifique se os valores, as expressões ou os objetos são equivalentes.
Retornar true quando ambos forem equivalentes ou retornar false quando eles não forem equivalentes.

```
equals('<object1>', '<object2>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | Sim | Vários | Os valores, expressões ou objetos para comparar |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retornar true quando ambos forem equivalentes. Retornar false quando não for equivalente. |
||||

*Exemplo*

Esses exemplos verificam se as entradas especificadas são equivalentes.

```
equals(true, 1)
equals('abc', 'abcd')
```

E retorna estes resultados:

* Primeiro exemplo: ambos os valores são equivalentes, portanto, a função retorna `true`.
* Segundo exemplo: os dois valores não são equivalentes, portanto, a função retorna `false`.

<a name="first"></a>

### <a name="first"></a>primeiro

Retornar o primeiro item de uma cadeia de caracteres ou matriz.

```
first('<collection>')
first([<collection>])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *coleção* de <> | Sim | Cadeia de caracteres ou matriz | A coleção onde encontrar o primeiro item |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*primeira-coleção-item*> | Qualquer | O primeiro item na coleção |
||||

*Exemplo*

Estes exemplos localizam o primeiro item nessas coleções:

```
first('hello')
first(createArray(0, 1, 2))
```

E retornar esses resultados:

* Primeiro exemplo: `"h"`
* Segundo exemplo: `0`

<a name="float"></a>

### <a name="float"></a>float

Converta uma versão de cadeia de caracteres para um número de ponto flutuante em um número de ponto flutuante real.

```
float('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A cadeia de caracteres que tem um número de ponto flutuante válido para converter |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*float-value*> | Float | O número de ponto flutuante para a cadeia de caracteres especificada |
||||

*Exemplo*

Este exemplo cria uma versão de cadeia de caracteres para este número de ponto flutuante:

```
float('10.333')
```

E retorna este resultado: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Retorna um carimbo de data/hora no formato especificado.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de data/hora*> | Sim | Cadeia | A cadeia de caracteres que contém o carimbo de data/hora |
| *formato* de <> | Não | Cadeia | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*reformatado-carimbo de data/hora*> | Cadeia | O carimbo de data/hora atualizado no formato especificado |
||||

*Exemplo*

Este exemplo converte um carimbo de data/hora no formato especificado:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

E retorna este resultado: `"2018-03-15T12:00:00"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Retornar o carimbo de data/hora atual mais as unidades de tempo especificadas.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *intervalo* de <> | Sim | Número inteiro | O número de unidades de tempo especificadas a serem adicionadas |
| <de *unidade* time> | Sim | Cadeia | A unidade de tempo a ser usada com o *intervalo*: "segundo", "minuto", "hora", "dia", "semana", "mês", "ano" |
| *formato* de <> | Não | Cadeia | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*atualizado-carimbo de data/hora*> | Cadeia | O carimbo de data/hora atual mais o número especificado de unidades de tempo |
||||

*Exemplo 1*

Suponha que o carimbo de data/hora atual seja "2018-03-01T00:00:00.0000000 Z".
Este exemplo adiciona cinco dias ao carimbo de data/hora:

```
getFutureTime(5, 'Day')
```

E retorna este resultado: `"2018-03-06T00:00:00.0000000Z"`

*Exemplo 2*

Suponha que o carimbo de data/hora atual seja "2018-03-01T00:00:00.0000000 Z".
Este exemplo adiciona cinco dias e converte o resultado no formato "D":

```
getFutureTime(5, 'Day', 'D')
```

E retorna este resultado: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getpastetime

Retorna o carimbo de data/hora atual menos as unidades de tempo especificadas.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *intervalo* de <> | Sim | Número inteiro | O número de unidades de tempo especificadas para subtrair |
| <de *unidade* time> | Sim | Cadeia | A unidade de tempo a ser usada com o *intervalo*: "segundo", "minuto", "hora", "dia", "semana", "mês", "ano" |
| *formato* de <> | Não | Cadeia | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*atualizado-carimbo de data/hora*> | Cadeia | O carimbo de data/hora atual menos o número especificado de unidades de tempo |
||||

*Exemplo 1*

Suponha que o carimbo de data/hora atual seja "2018-02-01T00:00:00.0000000 Z".
Este exemplo subtrai cinco dias do carimbo de data/hora:

```
getPastTime(5, 'Day')
```

E retorna este resultado: `"2018-01-27T00:00:00.0000000Z"`

*Exemplo 2*

Suponha que o carimbo de data/hora atual seja "2018-02-01T00:00:00.0000000 Z".
Este exemplo subtrai cinco dias e converte o resultado para o formato "D":

```
getPastTime(5, 'Day', 'D')
```

E retorna este resultado: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>grande

Verifique se o primeiro valor é maior que o segundo valor.
Retornar true quando o primeiro valor for maior ou retornar false quando for menor.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, float ou cadeia de caracteres | O primeiro valor para verificar se é maior que o segundo valor |
| <*compareTo*> | Sim | Integer, float ou String, respectivamente | O valor de comparação |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retorna true quando o primeiro valor é maior que o segundo valor. Retorna false quando o primeiro valor é igual ou menor que o segundo valor. |
||||

*Exemplo*

Esses exemplos verificam se o primeiro valor é maior que o segundo valor:

```
greater(10, 5)
greater('apple', 'banana')
```

E retornar esses resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Verifique se o primeiro valor é maior ou igual ao segundo valor.
Retornar true quando o primeiro valor for maior ou igual ou retornar false quando o primeiro valor for menor.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, float ou cadeia de caracteres | O primeiro valor para verificar se é maior ou igual ao segundo valor |
| <*compareTo*> | Sim | Integer, float ou String, respectivamente | O valor de comparação |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retorna true quando o primeiro valor é maior ou igual ao segundo valor. Retorna false quando o primeiro valor é menor que o segundo valor. |
||||

*Exemplo*

Esses exemplos verificam se o primeiro valor é maior ou igual ao segundo valor:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

E retornar esses resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="guid"></a>

### <a name="guid"></a>guid

Gere um GUID (identificador global exclusivo) como uma cadeia de caracteres, por exemplo, "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Além disso, você pode especificar um formato diferente para o GUID que não seja o formato padrão "D", que é de 32 dígitos separados por hifens.

```
guid('<format>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *formato* de <> | Não | Cadeia | Um [especificador de formato](https://msdn.microsoft.com/library/97af8hh4) único para o GUID retornado. Por padrão, o formato é "D", mas você pode usar "N", "D", "B", "P" ou "X". |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*GUID-valor*> | Cadeia | Um GUID gerado aleatoriamente |
||||

*Exemplo*

Este exemplo gera o mesmo GUID, mas como 32 dígitos, separados por hifens e entre parênteses:

```
guid('P')
```

E retorna este resultado: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>que

Verifique se uma expressão é verdadeira ou falsa.
Com base no resultado, retorna um valor especificado.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *expressão* de <> | Sim | Booleano | A expressão a ser verificada |
| <*valueIfTrue*> | Sim | Qualquer | O valor a ser retornado quando a expressão for verdadeira |
| <*valueIfFalse*> | Sim | Qualquer | O valor a ser retornado quando a expressão é falsa |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*especificado-valor de retorno*> | Qualquer | O valor especificado que retorna com base em se a expressão é verdadeira ou falsa |
||||

*Exemplo*

Este exemplo retorna `"yes"` porque a expressão especificada retorna true.
Caso contrário, o exemplo retornará `"no"`:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Retorna a posição inicial ou o valor do índice de uma subcadeia de caracteres.
Essa função não diferencia maiúsculas de minúsculas e os índices começam com o número 0.

```
indexOf('<text>', '<searchText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| > de *texto* < | Sim | Cadeia | A cadeia de caracteres que tem a subcadeia de caracteres a ser localizada |
| <*procurartexto*> | Sim | Cadeia | A subcadeia de caracteres a ser localizada |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*de valor de índice*>| Número inteiro | A posição inicial ou o valor do índice para a subcadeia de caracteres especificada. <p>Se a cadeia de caracteres não for encontrada, retorne o número-1. |
||||

*Exemplo*

Este exemplo localiza o valor de índice inicial para a subcadeia de caracteres "World" na cadeia de caracteres "Olá, mundo":

```
indexOf('hello world', 'world')
```

E retorna este resultado: `6`

<a name="int"></a>

### <a name="int"></a>int

Retornar a versão de inteiro para uma cadeia de caracteres.

```
int('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A cadeia de caracteres a ser convertida |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*de resultado de inteiro*> | Número inteiro | A versão de inteiro para a cadeia de caracteres especificada |
||||

*Exemplo*

Este exemplo cria uma versão de inteiro para a cadeia de caracteres "10":

```
int('10')
```

E retorna este resultado: `10`

<a name="json"></a>

### <a name="json"></a>json

Retorna o valor ou o objeto do tipo JavaScript Object Notation (JSON) para uma cadeia de caracteres ou XML.

```
json('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia de caracteres ou XML | A cadeia de caracteres ou XML a ser convertido |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*JSON-resultado*> | Tipo ou objeto nativo JSON | O valor do tipo nativo JSON ou o objeto para a cadeia de caracteres ou o XML especificado. Se a cadeia de caracteres for nula, a função retornará um objeto vazio. |
||||

*Exemplo 1*

Este exemplo converte essa cadeia de caracteres para o valor JSON:

```
json('[1, 2, 3]')
```

E retorna este resultado: `[1, 2, 3]`

*Exemplo 2*

Este exemplo converte esta cadeia de caracteres em JSON:

```
json('{"fullName": "Sophia Owen"}')
```

E retorna esse resultado:

```
{
  "fullName": "Sophia Owen"
}
```

*Exemplo 3*

Este exemplo converte este XML em JSON:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

E retorna esse resultado:

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>interseção

Retorna uma coleção que tem *apenas* os itens comuns nas coleções especificadas.
Para aparecer no resultado, um item deve aparecer em todas as coleções passadas para essa função.
Se um ou mais itens tiverem o mesmo nome, o último item com esse nome aparecerá no resultado.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*Collection2*>,... | Sim | Matriz ou objeto, mas não ambos | As coleções de onde você deseja *apenas* os itens comuns |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*itens comuns*> | Matriz ou objeto, respectivamente | Uma coleção que tem apenas os itens comuns nas coleções especificadas |
||||

*Exemplo*

Este exemplo localiza os itens comuns entre essas matrizes:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

E retorna uma matriz com *apenas* estes itens: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>aderir

Retornar uma cadeia de caracteres que tenha todos os itens de uma matriz e que tenha cada caractere separado por um *delimitador*.

```
join([<collection>], '<delimiter>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *coleção* de <> | Sim | Matriz | A matriz que tem os itens a serem ingressados |
| *delimitador* de <> | Sim | Cadeia | O separador que aparece entre cada caractere na cadeia de caracteres resultante |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*delimitador* de><*char1*><*char2*><*delimitador*>... | Cadeia | A cadeia de caracteres resultante criada a partir de todos os itens na matriz especificada |
||||

*Exemplo*

Este exemplo cria uma cadeia de caracteres de todos os itens nesta matriz com o caractere especificado como o delimitador:

```
join(createArray('a', 'b', 'c'), '.')
```

E retorna este resultado: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>última

Retorna o último item de uma coleção.

```
last('<collection>')
last([<collection>])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *coleção* de <> | Sim | Cadeia de caracteres ou matriz | A coleção onde encontrar o último item |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*última coleta de item*> | Cadeia de caracteres ou matriz, respectivamente | O último item na coleção |
||||

*Exemplo*

Estes exemplos localizam o último item nestas coleções:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

E retorna estes resultados:

* Primeiro exemplo: `"d"`
* Segundo exemplo: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Retorna a posição inicial ou o valor do índice para a última ocorrência de uma subcadeia de caracteres.
Essa função não diferencia maiúsculas de minúsculas e os índices começam com o número 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| > de *texto* < | Sim | Cadeia | A cadeia de caracteres que tem a subcadeia de caracteres a ser localizada |
| <*procurartexto*> | Sim | Cadeia | A subcadeia de caracteres a ser localizada |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*end-index-value*> | Número inteiro | A posição inicial ou o valor do índice para a última ocorrência da subcadeia de caracteres especificada. <p>Se a cadeia de caracteres não for encontrada, retorne o número-1. |
||||

*Exemplo*

Este exemplo localiza o valor de índice inicial para a última ocorrência da subcadeia de caracteres "World" na cadeia de caracteres "Olá, mundo":

```
lastIndexOf('hello world', 'world')
```

E retorna este resultado: `6`

<a name="length"></a>

### <a name="length"></a>length

Retorna o número de itens em uma coleção.

```
length('<collection>')
length([<collection>])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *coleção* de <> | Sim | Cadeia de caracteres ou matriz | A coleção com os itens a serem contados |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <> *de comprimento ou contagem* | Número inteiro | O número de itens na coleção |
||||

*Exemplo*

Esses exemplos contam o número de itens nessas coleções:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

E retornar esse resultado: `4`

<a name="less"></a>

### <a name="less"></a>inferiores

Verifique se o primeiro valor é menor que o segundo valor.
Retornar true quando o primeiro valor for menor ou retornar false quando o primeiro valor for maior.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, float ou cadeia de caracteres | O primeiro valor para verificar se é menor que o segundo valor |
| <*compareTo*> | Sim | Integer, float ou String, respectivamente | O item de comparação |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retorna true quando o primeiro valor é menor que o segundo valor. Retorna false quando o primeiro valor é igual ou maior que o segundo valor. |
||||

*Exemplo*

Esses exemplos verificam se o primeiro valor é menor que o segundo valor.

```
less(5, 10)
less('banana', 'apple')
```

E retornar esses resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Verifique se o primeiro valor é menor ou igual ao segundo valor.
Retornar true quando o primeiro valor for menor ou igual ou retornar false quando o primeiro valor for maior.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, float ou cadeia de caracteres | O primeiro valor para verificar se é menor ou igual ao segundo valor |
| <*compareTo*> | Sim | Integer, float ou String, respectivamente | O item de comparação |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false  | Booleano | Retorna true quando o primeiro valor é menor ou igual ao segundo valor. Retorna false quando o primeiro valor é maior que o segundo valor. |
||||

*Exemplo*

Esses exemplos verificam se o primeiro valor é menor ou igual ao segundo valor.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

E retornar esses resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="max"></a>

### <a name="max"></a>maximizar

Retorne o valor mais alto de uma lista ou matriz com números que são inclusivos em ambas as extremidades.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*número1*>, <*núm2*>,... | Sim | Integer, float ou Both | O conjunto de números do qual você deseja o valor mais alto |
| [<*número1*>, <*núm2*>,...] | Sim | Matriz-inteiro, float ou ambos | A matriz de números da qual você deseja o valor mais alto |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| > *de valor máximo* < | Inteiro ou float | O valor mais alto na matriz ou conjunto de números especificado |
||||

*Exemplo*

Esses exemplos obtêm o maior valor do conjunto de números e da matriz:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

E retornar esse resultado: `3`

<a name="min"></a>

### <a name="min"></a>min.

Retorna o valor mais baixo de um conjunto de números ou uma matriz.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*número1*>, <*núm2*>,... | Sim | Integer, float ou Both | O conjunto de números do qual você deseja o valor mais baixo |
| [<*número1*>, <*núm2*>,...] | Sim | Matriz-inteiro, float ou ambos | A matriz de números da qual você deseja o valor mais baixo |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| > *de valor mín* < | Inteiro ou float | O menor valor no conjunto especificado de números ou na matriz especificada |
||||

*Exemplo*

Esses exemplos obtêm o menor valor no conjunto de números e na matriz:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

E retornar esse resultado: `1`

<a name="mod"></a>

### <a name="mod"></a>resto

Retornar o resto da divisão de dois números.
Para obter o resultado inteiro, consulte [div ()](#div).

```
mod(<dividend>, <divisor>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*dividendo*> | Sim | Inteiro ou float | O número a ser dividido pelo *divisor* |
| > de <*divisor* | Sim | Inteiro ou float | O número que divide o *dividendo*, mas não pode ser 0. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*de resultados de módulo*> | Inteiro ou float | O restante da divisão do primeiro número pelo segundo número |
||||

*Exemplo*

Este exemplo divide o primeiro número pelo segundo número:

```
mod(3, 2)
```

E retornar esse resultado: `1`

<a name="mul"></a>

### <a name="mul"></a>Mul

Retorne o produto da multiplicação de dois números.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Sim | Inteiro ou float | O número a ser multiplicado por *multiplicand2* |
| <*multiplicand2*> | Sim | Inteiro ou float | O número que múltiplos *multiplicand1* |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <> *de resultados do produto* | Inteiro ou float | O produto da multiplicação do primeiro número pelo segundo número |
||||

*Exemplo*

Esses exemplos são múltiplos pelo primeiro número pelo segundo número:

```
mul(1, 2)
mul(1.5, 2)
```

E retornar esses resultados:

* Primeiro exemplo: `2`
* Segundo exemplo `3`

<a name="not"></a>

### <a name="not"></a>válido

Verifique se uma expressão é falsa.
Retorna true quando a expressão é false ou retorna false quando true.

```json
not(<expression>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *expressão* de <> | Sim | Booleano | A expressão a ser verificada |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retorna verdadeiro quando a expressão é falsa. Retorna falso quando a expressão é verdadeira. |
||||

*Exemplo 1*

Esses exemplos verificam se as expressões especificadas são falsas:

```json
not(false)
not(true)
```

E retornar esses resultados:

* Primeiro exemplo: a expressão é false, portanto, a função retorna `true`.
* Segundo exemplo: a expressão é true, portanto, a função retorna `false`.

*Exemplo 2*

Esses exemplos verificam se as expressões especificadas são falsas:

```json
not(equals(1, 2))
not(equals(1, 1))
```

E retornar esses resultados:

* Primeiro exemplo: a expressão é false, portanto, a função retorna `true`.
* Segundo exemplo: a expressão é true, portanto, a função retorna `false`.

<a name="or"></a>

### <a name="or"></a>ou

Verifique se pelo menos uma expressão é verdadeira.
Retornar true quando pelo menos uma expressão for true ou retornar false quando todos forem false.

```
or(<expression1>, <expression2>, ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>,... | Sim | Booleano | As expressões a serem verificadas |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retorna true quando pelo menos uma expressão for true. Retorna false quando todas as expressões são false. |
||||

*Exemplo 1*

Esses exemplos verificam se pelo menos uma expressão é verdadeira:

```json
or(true, false)
or(false, false)
```

E retornar esses resultados:

* Primeiro exemplo: pelo menos uma expressão é verdadeira, portanto, a função retorna `true`.
* Segundo exemplo: as duas expressões são false, portanto, a função retorna `false`.

*Exemplo 2*

Esses exemplos verificam se pelo menos uma expressão é verdadeira:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

E retornar esses resultados:

* Primeiro exemplo: pelo menos uma expressão é verdadeira, portanto, a função retorna `true`.
* Segundo exemplo: as duas expressões são false, portanto, a função retorna `false`.

<a name="rand"></a>

### <a name="rand"></a>Rand

Retornar um inteiro aleatório de um intervalo especificado, que é inclusivo apenas na extremidade inicial.

```
rand(<minValue>, <maxValue>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Sim | Número inteiro | O menor número inteiro no intervalo |
| <*maxValue*> | Sim | Número inteiro | O inteiro que segue o número inteiro mais alto no intervalo que a função pode retornar |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*de resultados aleatórios*> | Número inteiro | O inteiro aleatório retornado do intervalo especificado |
||||

*Exemplo*

Este exemplo obtém um inteiro aleatório do intervalo especificado, excluindo o valor máximo:

```
rand(1, 5)
```

E retorna um destes números como resultado: `1`, `2`, `3`ou `4`

<a name="range"></a>

### <a name="range"></a>amplitude

Retornar uma matriz de inteiros que inicia de um número inteiro especificado.

```
range(<startIndex>, <count>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | Sim | Número inteiro | Um valor inteiro que inicia a matriz como o primeiro item |
| *contagem* de <> | Sim | Número inteiro | O número de inteiros na matriz |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [*intervalo de <-> de resultados*] | Matriz | A matriz com inteiros começando do índice especificado |
||||

*Exemplo*

Este exemplo cria uma matriz de inteiros que inicia do índice especificado e tem o número especificado de inteiros:

```
range(1, 4)
```

E retorna este resultado: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>Substitua

Substitui uma subcadeia de caracteres pela cadeia de caracteres especificada e retorna a cadeia de caracteres de resultado. Essa função diferencia maiúsculas de minúsculas.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| > de *texto* < | Sim | Cadeia | A cadeia de caracteres que tem a subcadeia de caracteres a ser substituída |
| <> de *TextoAntigo* | Sim | Cadeia | A subcadeia de caracteres a ser substituída |
| <*textonovo*> | Sim | Cadeia | A cadeia de caracteres de substituição |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*atualizado-> de texto* | Cadeia | A cadeia de caracteres atualizada após substituir a subcadeia de caracteres <p>Se a subcadeia de caracteres não for encontrada, retorne a cadeia de caracteres original. |
||||

*Exemplo*

Este exemplo localiza a subcadeia de caracteres "Old" em "a cadeia de caracteres antiga" e substitui "Old" por "New":

```
replace('the old string', 'old', 'new')
```

E retorna este resultado: `"the new string"`

<a name="skip"></a>

### <a name="skip"></a>saltar

Remover itens da frente de uma coleção e retornar todos os *outros* itens.

```
skip([<collection>], <count>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *coleção* de <> | Sim | Matriz | A coleção cujos itens você deseja remover |
| *contagem* de <> | Sim | Número inteiro | Um inteiro positivo para o número de itens a serem removidos na frente |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<*atualizado-> de coleção*] | Matriz | A coleção atualizada após a remoção dos itens especificados |
||||

*Exemplo*

Este exemplo remove um item, o número 0, da parte frontal da matriz especificada:

```
skip(createArray(0, 1, 2, 3), 1)
```

E retorna essa matriz com os itens restantes: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>dividir

Retorna uma matriz que contém subcadeias de caracteres, separadas por vírgulas, com base no caractere delimitador especificado na cadeia de caracteres original.

```
split('<text>', '<delimiter>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| > de *texto* < | Sim | Cadeia | A cadeia de caracteres a ser separada em subcadeias de caracteres com base no delimitador especificado na cadeia de caracteres original |
| *delimitador* de <> | Sim | Cadeia | O caractere na cadeia de caracteres original a ser usado como o delimitador |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<*subseqüência1*>, <*subseqüência2*>,...] | Matriz | Uma matriz que contém subcadeias da cadeia de caracteres original, separadas por vírgulas |
||||

*Exemplo*

Este exemplo cria uma matriz com subcadeias da cadeia de caracteres especificada com base no caractere especificado como o delimitador:

```
split('a_b_c', '_')
```

E retorna essa matriz como resultado: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Retorna o início do dia para um carimbo de data/hora.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de data/hora*> | Sim | Cadeia | A cadeia de caracteres que contém o carimbo de data/hora |
| *formato* de <> | Não | Cadeia | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*atualizado-carimbo de data/hora*> | Cadeia | O carimbo de data/hora especificado, mas começando no sinal de zero horas para o dia |
||||

*Exemplo*

Este exemplo localiza o início do dia para este carimbo de data/hora:

```
startOfDay('2018-03-15T13:30:30Z')
```

E retorna este resultado: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Retornar o início da hora para um carimbo de data/hora.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de data/hora*> | Sim | Cadeia | A cadeia de caracteres que contém o carimbo de data/hora |
| *formato* de <> | Não | Cadeia | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*atualizado-carimbo de data/hora*> | Cadeia | O carimbo de data/hora especificado, mas começando na marca de minuto zero para a hora |
||||

*Exemplo*

Este exemplo localiza o início da hora para este carimbo de data/hora:

```
startOfHour('2018-03-15T13:30:30Z')
```

E retorna este resultado: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Retornar o início do mês para um carimbo de data/hora.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de data/hora*> | Sim | Cadeia | A cadeia de caracteres que contém o carimbo de data/hora |
| *formato* de <> | Não | Cadeia | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*atualizado-carimbo de data/hora*> | Cadeia | O carimbo de data/hora especificado, mas começando no primeiro dia do mês no sinal de zero |
||||

*Exemplo*

Este exemplo retorna o início do mês para este carimbo de data/hora:

```
startOfMonth('2018-03-15T13:30:30Z')
```

E retorna este resultado: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Verifique se uma cadeia de caracteres começa com uma subcadeia de caracteres específica.
Retorna true quando a subcadeia de caracteres é encontrada ou retorna false quando não encontrada.
Essa função não diferencia maiúsculas de minúsculas.

```
startsWith('<text>', '<searchText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| > de *texto* < | Sim | Cadeia | A cadeia de caracteres a ser verificada |
| <*procurartexto*> | Sim | Cadeia | A cadeia de caracteres inicial a ser localizada |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false  | Booleano | Retorna true quando a subcadeia de caracteres inicial é encontrada. Retorna falso quando não encontrado. |
||||

*Exemplo 1*

Este exemplo verifica se a cadeia de caracteres "Olá, mundo" começa com a subcadeia "Olá":

```
startsWith('hello world', 'hello')
```

E retorna este resultado: `true`

*Exemplo 2*

Este exemplo verifica se a cadeia de caracteres "Olá, mundo" começa com a subcadeia "Greetings":

```
startsWith('hello world', 'greetings')
```

E retorna este resultado: `false`

<a name="string"></a>

### <a name="string"></a>string

Retornar a versão da cadeia de caracteres para um valor.

```
string(<value>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Qualquer | O valor a ser convertido |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <> *de valor da cadeia de caracteres* | Cadeia | A versão da cadeia de caracteres para o valor especificado |
||||

*Exemplo 1*

Este exemplo cria a versão de cadeia de caracteres para este número:

```
string(10)
```

E retorna este resultado: `"10"`

*Exemplo 2*

Este exemplo cria uma cadeia de caracteres para o objeto JSON especificado e usa o caractere de barra invertida (\\) como um caractere de escape para a aspa dupla (").

```
string( { "name": "Sophie Owen" } )
```

E retorna este resultado: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>projeto

Retorne o resultado da subtração do segundo número do primeiro número.

```
sub(<minuend>, <subtrahend>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*minuendo*> | Sim | Inteiro ou float | O número do qual subtrair o *subtraendo* |
| <*subtraendo*> | Sim | Inteiro ou float | O número a ser subtraído da *minuendo* |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado*> | Inteiro ou float | O resultado da subtração do segundo número do primeiro número |
||||

*Exemplo*

Este exemplo subtrai o segundo número do primeiro número:

```
sub(10.3, .3)
```

E retorna este resultado: `10`

<a name="substring"></a>

### <a name="substring"></a>Subcadeia

Retornar caracteres de uma cadeia de caracteres, começando da posição especificada ou índice.
Os valores de índice começam com o número 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| > de *texto* < | Sim | Cadeia | A cadeia cujos caracteres você deseja |
| <*startIndex*> | Sim | Número inteiro | Um número positivo igual ou maior que 0 que você deseja usar como a posição inicial ou o valor do índice |
| *comprimento* de <> | Sim | Número inteiro | Um número positivo de caracteres que você deseja na Subcadeia |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| *subcadeia de <-resultado*> | Cadeia | Uma Subcadeia com o número especificado de caracteres, começando na posição de índice especificada na cadeia de caracteres de origem |
||||

*Exemplo*

Este exemplo cria uma subcadeia de caracteres de cinco caracteres a partir da cadeia de caracteres especificada, começando do valor de índice 6:

```
substring('hello world', 6, 5)
```

E retorna este resultado: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Subtraia um número de unidades de tempo de um carimbo de data/hora.
Veja também [Getpastetime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de data/hora*> | Sim | Cadeia | A cadeia de caracteres que contém o carimbo de data/hora |
| *intervalo* de <> | Sim | Número inteiro | O número de unidades de tempo especificadas para subtrair |
| <de *unidade* time> | Sim | Cadeia | A unidade de tempo a ser usada com o *intervalo*: "segundo", "minuto", "hora", "dia", "semana", "mês", "ano" |
| *formato* de <> | Não | Cadeia | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*atualizado-carimbo de data/hora*> | Cadeia | O carimbo de data/hora menos o número especificado de unidades de tempo |
||||

*Exemplo 1*

Este exemplo subtrai um dia deste carimbo de data/hora:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

E retorna este resultado: `"2018-01-01T00:00:00:0000000Z"`

*Exemplo 2*

Este exemplo subtrai um dia deste carimbo de data/hora:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

E retorna esse resultado usando o formato "D" opcional: `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>ter

Retornar itens da frente de uma coleção.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *coleção* de <> | Sim | Cadeia de caracteres ou matriz | A coleção cujos itens você deseja |
| *contagem* de <> | Sim | Número inteiro | Um inteiro positivo para o número de itens que você deseja da frente |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*subconjunto*> ou [< > do*subconjunto*] | Cadeia de caracteres ou matriz, respectivamente | Uma cadeia de caracteres ou matriz que tem o número especificado de itens extraídos da frente da coleção original |
||||

*Exemplo*

Esses exemplos obtêm o número especificado de itens da frente dessas coleções:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

E retornar esses resultados:

* Primeiro exemplo: `"abc"`
* Segundo exemplo: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>tiques

Retorna o valor da propriedade de `ticks` para um carimbo de data/hora especificado.
Um *tique* é um intervalo de 100 a nanossegundos.

```
ticks('<timestamp>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de data/hora*> | Sim | Cadeia | A cadeia de caracteres de um carimbo de data/hora |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*tiques-número*> | Número inteiro | O número de tiques desde o carimbo de data/hora especificado |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Retornar uma cadeia de caracteres em formato em minúsculas. Se um caractere na cadeia de caracteres não tiver uma versão em minúsculas, esse caractere permanecerá inalterado na cadeia de caracteres retornada.

```
toLower('<text>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| > de *texto* < | Sim | Cadeia | A cadeia de caracteres a ser retornada em formato de minúsculas |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <> *de texto em minúsculas* | Cadeia | A cadeia de caracteres original em formato em minúsculas |
||||

*Exemplo*

Este exemplo converte essa cadeia de caracteres em minúsculas:

```
toLower('Hello World')
```

E retorna este resultado: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Retornar uma cadeia de caracteres em formato maiúsculo. Se um caractere na cadeia de caracteres não tiver uma versão em maiúsculas, esse caractere permanecerá inalterado na cadeia de caracteres retornada.

```
toUpper('<text>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| > de *texto* < | Sim | Cadeia | A cadeia de caracteres a ser retornada em formato maiúsculo |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <> *de texto em maiúsculas* | Cadeia | A cadeia de caracteres original em formato maiúsculo |
||||

*Exemplo*

Este exemplo converte essa cadeia de caracteres em maiúsculas:

```
toUpper('Hello World')
```

E retorna este resultado: `"HELLO WORLD"`

<a name="trim"></a>

### <a name="trim"></a>Trim

Remova o espaço em branco à esquerda e à direita de uma cadeia de caracteres e retorne a cadeia de caracteres atualizada.

```
trim('<text>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| > de *texto* < | Sim | Cadeia | A cadeia de caracteres que tem o espaço em branco à esquerda e à direita para remover |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*updatedText*> | Cadeia | Uma versão atualizada para a cadeia de caracteres original sem espaço em branco à esquerda ou à direita |
||||

*Exemplo*

Este exemplo remove o espaço em branco à esquerda e à direita da cadeia de caracteres "Olá, Mundo":

```
trim(' Hello World  ')
```

E retorna este resultado: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>union

Retorna uma coleção que tem *todos* os itens das coleções especificadas.
Para aparecer no resultado, um item pode aparecer em qualquer coleção passada para essa função. Se um ou mais itens tiverem o mesmo nome, o último item com esse nome aparecerá no resultado.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*Collection2*>,...  | Sim | Matriz ou objeto, mas não ambos | As coleções de onde você deseja *todos* os itens |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*atualizadocollection*> | Matriz ou objeto, respectivamente | Uma coleção com todos os itens das coleções especificadas-sem duplicatas |
||||

*Exemplo*

Este exemplo obtém *todos* os itens dessas coleções:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

E retorna este resultado: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Retornar uma versão codificada do URI (Uniform Resource Identifier) para uma cadeia de caracteres, substituindo caracteres não seguros por URL por caracteres de escape.
Use essa função em vez de [encodeUriComponent ()](#encodeUriComponent).
Embora ambas as funções funcionem da mesma maneira, `uriComponent()` é preferida.

```
uriComponent('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A cadeia de caracteres a ser convertida em formato codificado em URI |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*URI codificado*> | Cadeia | A cadeia de caracteres codificada em URI com caracteres de escape |
||||

*Exemplo*

Este exemplo cria uma versão codificada em URI para esta cadeia de caracteres:

```
uriComponent('https://contoso.com')
```

E retorna este resultado: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Retornar a versão binária para um componente URI (Uniform Resource Identifier).

```
uriComponentToBinary('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A cadeia de caracteres codificada em URI a ser convertida |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| *URI de <binário para codificação*> | Cadeia | A versão binária para a cadeia de caracteres codificada em URI. O conteúdo binário é codificado em Base64 e representado por `$content`. |
||||

*Exemplo*

Este exemplo cria a versão binária para esta cadeia de caracteres codificada em URI:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

E retorna esse resultado:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Retornar a versão da cadeia de caracteres para uma cadeia de caracteres codificada por URI (Uniform Resource Identifier), decodificando efetivamente a cadeia de caracteres codificada em URI.

```
uriComponentToString('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A cadeia de caracteres codificada em URI a ser decodificada |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| < *-URI decodificado*> | Cadeia | A versão decodificada da cadeia de caracteres codificada em URI |
||||

*Exemplo*

Este exemplo cria a versão de cadeia de caracteres decodificada para esta cadeia de caracteres codificada em URI:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

E retorna este resultado: `"https://contoso.com"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Retornar o carimbo de data/hora atual.

```
utcNow('<format>')
```

Opcionalmente, você pode especificar um formato diferente com o parâmetro <*format*>.

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *formato* de <> | Não | Cadeia | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de data/hora atual*> | Cadeia | A data e a hora atuais |
||||

*Exemplo 1*

Suponha que hoje seja 15 de abril de 2018 às 1:00:00 PM.
Este exemplo obtém o carimbo de data/hora atual:

```
utcNow()
```

E retorna este resultado: `"2018-04-15T13:00:00.0000000Z"`

*Exemplo 2*

Suponha que hoje seja 15 de abril de 2018 às 1:00:00 PM.
Este exemplo obtém o carimbo de data/hora atual usando o formato "D" opcional:

```
utcNow('D')
```

E retorna este resultado: `"Sunday, April 15, 2018"`

<a name="xml"></a>

### <a name="xml"></a>xml

Retornar a versão XML para uma cadeia de caracteres que contém um objeto JSON.

```
xml('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A cadeia de caracteres com o objeto JSON a ser convertido <p>O objeto JSON deve ter apenas uma propriedade raiz, que não pode ser uma matriz. <br>Use o caractere de barra invertida (\\) como um caractere de escape para a aspa dupla ("). |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*XML-versão*> | Objeto | O XML codificado para a cadeia de caracteres ou objeto JSON especificado |
||||

*Exemplo 1*

Este exemplo cria a versão XML para esta cadeia de caracteres, que contém um objeto JSON:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

E retorna este XML de resultado:

```xml
<name>Sophia Owen</name>
```

*Exemplo 2*

Suponha que você tenha este objeto JSON:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

Este exemplo cria XML para uma cadeia de caracteres que contém este objeto JSON:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

E retorna este XML de resultado:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>xpath

Marque XML para nós ou valores que correspondam a uma expressão XPath (linguagem de caminho XML) e retorne os nós ou valores correspondentes. Uma expressão XPath, ou apenas "XPath", ajuda a navegar por uma estrutura de documento XML para que você possa selecionar nós ou valores de computação no conteúdo XML.

```
xpath('<xml>', '<xpath>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| > *xml* < | Sim | Qualquer | A cadeia de caracteres XML para pesquisar nós ou valores que correspondam a um valor de expressão XPath |
| <*xpath*> | Sim | Qualquer | A expressão XPath usada para localizar os valores ou nós XML correspondentes |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <> *de nó XML* | XML | Um nó XML quando apenas um único nó corresponde à expressão XPath especificada |
| <*valor*> | Qualquer | O valor de um nó XML quando apenas um único valor corresponde à expressão XPath especificada |
| [<*XML-node1*>, <*xml-NODE2*>,...] </br>-ou- </br>[<*value1*>, <*value2*>,...] | Matriz | Uma matriz com nós XML ou valores que correspondem à expressão XPath especificada |
||||

*Exemplo 1*

A seguir, no exemplo 1, este exemplo localiza os nós que correspondem ao nó `<count></count>` e adiciona esses valores de nó com a função `sum()`:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

E retorna este resultado: `30`

*Exemplo 2*

Para este exemplo, as duas expressões localizam nós que correspondem ao nó `<location></location>`, nos argumentos especificados, que incluem XML com um namespace. As expressões usam o caractere de barra invertida (\\) como um caractere de escape para a aspa dupla (").

* *Expressão 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Expressão 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

Aqui estão os argumentos:

* Esse XML, que inclui o namespace do documento XML, `xmlns="http://contoso.com"`:

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Uma expressão XPath aqui:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

Este é o nó de resultado que corresponde ao nó de `<location></location>`:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Exemplo 3*

A seguir, no exemplo 3, este exemplo localiza o valor no nó `<location></location>`:

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

E retorna este resultado: `"Paris"`

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de variáveis de sistema que você pode usar em expressões, consulte [variáveis do sistema](control-flow-system-variables.md).
