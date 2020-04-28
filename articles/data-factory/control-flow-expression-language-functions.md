---
title: Expressão e funções na Fábrica de Dados Azure
description: Este artigo fornece informações sobre expressões e funções que pode utilizar na criação de entidades de fábrica de dados.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: 1ce6da555bc8777bdb9671df1567f06227b74b6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192804"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Expressões e funções no Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-functions-variables.md)
> * [Versão atual](control-flow-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo fornece detalhes sobre expressões e funções suportadas pela Azure Data Factory. 

## <a name="expressions"></a>Expressões

Os valores jSON na definição podem ser literais ou expressões que são avaliadas no tempo de execução. Por exemplo:  
  
```json
"name": "value"
```

 ou  
  
```json
"name": "@pipeline().parameters.password"
```

Expressões podem aparecer em qualquer lugar em um valor de corda JSON e sempre resultar em outro valor JSON. Se um valor JSON for uma expressão, o corpo da expressão\@é extraído removendo o sinal de at-sign ( ). Se for necessária uma corda \@literal que comece com, deve ser escapada utilizando \@ \@. Os exemplos que se seguem mostram como as expressões são avaliadas.  
  
|Valor JSON|Resultado|  
|----------------|------------|  
|"Parâmetros"|Os "parâmetros" dos caracteres são devolvidos.|  
|"Parâmetros[1]"|Os caracteres 'parâmetros[1]' são devolvidos.|  
|"\@\@"|Uma corda de 1\@caracteres que contém ' é devolvida.|  
|" \@"|Uma corda de 2 \@caracteres que contém ' é devolvida.|  
  
 Expressões também podem aparecer dentro de cordas, usando uma característica chamada `@{ ... }` *interpolação* de cordas onde as expressões são embrulhadas em . Por exemplo: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Usando a interpolação de cordas, o resultado é sempre uma corda. Diga que `myNumber` defini `42` `myString` como `foo`e como:  
  
|Valor JSON|Resultado|  
|----------------|------------|  
|"\@pipeline().parâmetros.myString"| Regressa `foo` como uma corda.|  
|"\@{pipeline().parâmetros.myString}"| Regressa `foo` como uma corda.|  
|"\@pipeline().parâmetros.myNumber"| Devoluções `42` como *número.*|  
|"\@{pipeline().parâmetros.myNumber}"| Regressa `42` como uma *corda.*|  
|"Resposta é: @{pipeline().parâmetros.myNumber}"| Devolve a `Answer is: 42`corda.|  
|"\@concat('Resposta é: ', string (.parâmetros.myNumber)"| Devolve a corda`Answer is: 42`|  
|"Resposta é: \@ \@{pipeline().parâmetros.myNumber}"| Devolve a `Answer is: @{pipeline().parameters.myNumber}`corda.|  
  
## <a name="examples"></a>Exemplos

### <a name="a-dataset-with-a-parameter"></a>Um conjunto de dados com um parâmetro
No exemplo seguinte, o BlobDataset leva um parâmetro nomeado **caminho**. O seu valor é utilizado para definir um valor `dataset().path`para a **propriedade pastaPath** utilizando a expressão: . 

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

### <a name="a-pipeline-with-a-parameter"></a>Um oleoduto com parâmetro
No exemplo seguinte, o gasoduto tem parâmetros **inputPath** e **outputPath.** O **caminho** para o conjunto de dados de bolhas parametrizadas é definido utilizando valores destes parâmetros. A sintaxe utilizada `pipeline().parameters.parametername`aqui é: . 

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
Este [tutorial](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) acompanha-o através de como passar parâmetros entre um oleoduto e atividade, bem como entre as atividades.

  
## <a name="functions"></a>Funções

Pode chamar funções dentro de expressões. As seguintes secções fornecem informações sobre as funções que podem ser usadas numa expressão.  

## <a name="string-functions"></a>Funções de cadeia  

Para trabalhar com cordas, pode utilizar estas funções de cadeia e também [algumas funções](#collection-functions)de recolha.
As funções das cordas funcionam apenas em cordas.

| Função de corda | Tarefa |
| --------------- | ---- |
| [concat](control-flow-expression-language-functions.md#concat) | Combine duas ou mais cordas e devolva a corda combinada. |
| [terminaCom](control-flow-expression-language-functions.md#endswith) | Verifique se uma corda termina com a subcadeia especificada. |
| [guid](control-flow-expression-language-functions.md#guid) | Gere um identificador globalmente único (GUID) como uma corda. |
| [indexof](control-flow-expression-language-functions.md#indexof) | Volte a colocar a posição inicial para um substring. |
| [últimoIndexOf](control-flow-expression-language-functions.md#lastindexof) | Volte a colocar a posição inicial na última ocorrência de um substring. |
| [substituir](control-flow-expression-language-functions.md#replace) | Substitua uma subcadeia com a corda especificada e devolva a corda atualizada. |
| [dividir](control-flow-expression-language-functions.md#split) | Devolva uma matriz que contenha subcordas, separadas por vírgulas, de uma corda maior baseada num caracteres delimitadores especificados na cadeia original. |
| [começaCom](control-flow-expression-language-functions.md#startswith) | Verifique se uma corda começa com um substring específico. |
| [substring](control-flow-expression-language-functions.md#substring) | Volte a colocar caracteres de uma corda, a partir da posição especificada. |
| [toLower](control-flow-expression-language-functions.md#toLower) | Volte a colocar uma corda em formato minúsculo. |
| [toUpper](control-flow-expression-language-functions.md#toUpper) | Volte a colocar uma corda em formato superior. |
| [aparar](control-flow-expression-language-functions.md#trim) | Remova o espaço branco de liderar e arrastando a partir de uma corda e devolva a corda atualizada. |

## <a name="collection-functions"></a>Funções de recolha

Para trabalhar com coleções, geralmente matrizes, cordas e, por vezes, dicionários, pode utilizar estas funções de coleção.

| Função de recolha | Tarefa |
| ------------------- | ---- |
| [contém](control-flow-expression-language-functions.md#contains) | Verifique se uma coleção tem um item específico. |
| [vazio](control-flow-expression-language-functions.md#empty) | Verifique se uma coleção está vazia. |
| [primeiro](control-flow-expression-language-functions.md#first) | Devolva o primeiro item de uma coleção. |
| [cruzamento](control-flow-expression-language-functions.md#intersection) | Devolva uma coleção que tenha *apenas* os itens comuns nas coleções especificadas. |
| [juntar-se](control-flow-expression-language-functions.md#join) | Volte a colocar uma corda que tenha *todos os* itens de uma matriz, separadas pelo carácter especificado. |
| [última](control-flow-expression-language-functions.md#last) | Devolva o último item de uma coleção. |
| [comprimento](control-flow-expression-language-functions.md#length) | Volte a devolver o número de itens numa corda ou numa matriz. |
| [saltar](control-flow-expression-language-functions.md#skip) | Retire os itens da parte da frente de uma coleção e devolva *todos os outros* itens. |
| [tomar](control-flow-expression-language-functions.md#take) | Devolver artigos da frente de uma coleção. |
| [união](control-flow-expression-language-functions.md#union) | Devolva uma coleção que tenha *todos os* itens das coleções especificadas. | 

## <a name="logical-functions"></a>Funções lógicas  

Estas funções são úteis dentro de condições, podem ser usadas para avaliar qualquer tipo de lógica.  
  
| Função de comparação lógica | Tarefa |
| --------------------------- | ---- |
| [e](control-flow-expression-language-functions.md#and) | Verifique se todas as expressões são verdadeiras. |
| [iguais](control-flow-expression-language-functions.md#equals) | Verifique se ambos os valores são equivalentes. |
| [greater](control-flow-expression-language-functions.md#greater) | Verifique se o primeiro valor é superior ao segundo valor. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | Verifique se o primeiro valor é maior ou igual ao segundo valor. |
| [se](control-flow-expression-language-functions.md#if) | Verifique se uma expressão é verdadeira ou falsa. Com base no resultado, devolva um valor especificado. |
| [less](control-flow-expression-language-functions.md#less) | Verifique se o primeiro valor é inferior ao segundo valor. |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | Verifique se o primeiro valor é inferior ou igual ao segundo valor. |
| [não](control-flow-expression-language-functions.md#not) | Verifique se uma expressão é falsa. |
| [ou](control-flow-expression-language-functions.md#or) | Verifique se pelo menos uma expressão é verdadeira. |
  
## <a name="conversion-functions"></a>Funções de conversão  

 Estas funções são utilizadas para converter entre cada um dos tipos nativos da língua:  
-   string
-   número inteiro
-   float
-   boolean
-   matrizes
-   dicionários

| Função de conversão | Tarefa |
| ------------------- | ---- |
| [array](control-flow-expression-language-functions.md#array) | Devolva uma matriz de uma única entrada especificada. Para várias inputs, consulte [createArray](control-flow-expression-language-functions.md#createArray). |
| [base64](control-flow-expression-language-functions.md#base64) | Devolva a versão codificada base64 para uma corda. |
| [base64ToBinary](control-flow-expression-language-functions.md#base64ToBinary) | Volte a colocar a versão binária numa cadeia codificada com base64. |
| [base64Tostring](control-flow-expression-language-functions.md#base64ToString) | Volte a colocar a versão de corda numa cadeia codificada com base64. |
| [binário](control-flow-expression-language-functions.md#binary) | Devolva a versão binária por um valor de entrada. |
| [bool](control-flow-expression-language-functions.md#bool) | Devolva a versão Boolean por um valor de entrada. |
| [coalesce](control-flow-expression-language-functions.md#coalesce) | Devolva o primeiro valor não nulo de um ou mais parâmetros. |
| [criar Array](control-flow-expression-language-functions.md#createArray) | Devolva uma matriz de várias inputs. |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | Devolva os dados URI por um valor de entrada. |
| [dataUriToBinary](control-flow-expression-language-functions.md#dataUriToBinary) | Devolva a versão binária para obter um URI de dados. |
| [datauritostring](control-flow-expression-language-functions.md#dataUriToString) | Devolva a versão de cadeia para um URI de dados. |
| [descodificarBase64](control-flow-expression-language-functions.md#decodeBase64) | Volte a colocar a versão de corda numa cadeia codificada com base64. |
| [descodificarDataUri](control-flow-expression-language-functions.md#decodeDataUri) | Devolva a versão binária para obter um URI de dados. |
| [descodificar Componente Uri](control-flow-expression-language-functions.md#decodeUriComponent) | Volte a colocar uma corda que substitua caracteres de fuga por versões descodificadas. |
| [codificar Componente UriUri](control-flow-expression-language-functions.md#encodeUriComponent) | Volte a colocar uma corda que substitua caracteres inseguros de URL por caracteres de fuga. |
| [float](control-flow-expression-language-functions.md#float) | Devolva um número de ponto flutuante para obter um valor de entrada. |
| [int](control-flow-expression-language-functions.md#int) | Devolva a versão inteiro para uma corda. |
| [json](control-flow-expression-language-functions.md#json) | Devolva o valor ou objeto do tipo JavaScript Object (JSON) para uma corda ou XML. |
| [string](control-flow-expression-language-functions.md#string) | Devolva a versão de corda por um valor de entrada. |
| [uriComponent](control-flow-expression-language-functions.md#uriComponent) | Devolva a versão codificada por URI para obter um valor de entrada substituindo caracteres url-inseguros por caracteres de fuga. |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | Devolva a versão binária para uma corda codificada por URI. |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | Devolva a versão de corda para uma corda codificada por URI. |
| [xml](control-flow-expression-language-functions.md#xml) | Devolva a versão XML para uma corda. |
| [xpath](control-flow-expression-language-functions.md#xpath) | Verifique o XML para ver se há nós ou valores que correspondam a uma expressão XPath (XML Path Language) e devolva os nós ou valores correspondentes. |

## <a name="math-functions"></a>Funções matemáticas  
 Estas funções podem ser utilizadas para ambos os tipos de números: **inteiros** e **boias**.  

| Função matemática | Tarefa |
| ------------- | ---- |
| [adicionar](control-flow-expression-language-functions.md#add) | Devolva o resultado da adição de dois números. |
| [div](control-flow-expression-language-functions.md#div) | Devolva o resultado da divisão de dois números. |
| [máximo](control-flow-expression-language-functions.md#max) | Devolva o valor mais alto de um conjunto de números ou de uma matriz. |
| [min](control-flow-expression-language-functions.md#min) | Devolva o valor mais baixo de um conjunto de números ou de uma matriz. |
| [mod](control-flow-expression-language-functions.md#mod) | Devolva o restante da divisão de dois números. |
| [mul](control-flow-expression-language-functions.md#mul) | Devolva o produto de multiplicar dois números. |
| [rand](control-flow-expression-language-functions.md#rand) | Devolva um inteiro aleatório de um intervalo especificado. |
| [alcance](control-flow-expression-language-functions.md#range) | Devolva uma matriz inteiro que começa a partir de um inteiro especificado. |
| [sub](control-flow-expression-language-functions.md#sub) | Devolva o resultado de subtrair o segundo número do primeiro número. |
  
## <a name="date-functions"></a>Funções de data  

| Função de data ou hora | Tarefa |
| --------------------- | ---- |
| [addDays](control-flow-expression-language-functions.md#addDays) | Adicione alguns dias a uma marca de tempo. |
| [addHours](control-flow-expression-language-functions.md#addHours) | Adicione algumas horas a um carimbo temporal. |
| [addMinutes](control-flow-expression-language-functions.md#addMinutes) | Adicione alguns minutos a uma marca de tempo. |
| [adicionarSeconds](control-flow-expression-language-functions.md#addSeconds) | Adicione alguns segundos a uma marca de tempo. |
| [adicionarToTime](control-flow-expression-language-functions.md#addToTime) | Adicione várias unidades de tempo a uma marca de tempo. Ver também [getFutureTime](control-flow-expression-language-functions.md#getFutureTime). |
| [converterFromUtc](control-flow-expression-language-functions.md#convertFromUtc) | Converta uma marca de tempo da Universal Time Coordenada (UTC) para o fuso horário-alvo. |
| [converterTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | Converta uma marca de tempo do fuso horário de origem para o fuso horário alvo. |
| [converteToUtc](control-flow-expression-language-functions.md#convertToUtc) | Converter uma marca de tempo do fuso horário de origem para Universal Time Coordenado (UTC). |
| [dayOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | Volte o componente do dia do mês a partir de uma marca de tempo. |
| [dayOfWeek](control-flow-expression-language-functions.md#dayOfWeek) | Volte o componente do dia da semana a partir de uma marca de tempo. |
| [dia ofyear](control-flow-expression-language-functions.md#dayOfYear) | Volte o componente do dia do ano a partir de uma marca de tempo. |
| [formatoDateTime](control-flow-expression-language-functions.md#formatDateTime) | Devolva a marca de tempo como uma corda em formato opcional. |
| [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) | Volte a colocar a marca atual mais as unidades de tempo especificadas. Consulte também [adicionarToTime](control-flow-expression-language-functions.md#addToTime). |
| [getPastTime](control-flow-expression-language-functions.md#getPastTime) | Volte a colocar a marca atual menos as unidades de tempo especificadas. Consulte também [subtrairFromTime](control-flow-expression-language-functions.md#subtractFromTime). |
| [inícioOfDay](control-flow-expression-language-functions.md#startOfDay) | Volte o início do dia para um carimbo temporal. |
| [inícioOfHour](control-flow-expression-language-functions.md#startOfHour) | Volte o início da hora por uma marca de tempo. |
| [inícioDo mês](control-flow-expression-language-functions.md#startOfMonth) | Devolva o início do mês por um carimbo temporal. |
| [subtratoFromTime](control-flow-expression-language-functions.md#subtractFromTime) | Subtraia várias unidades temporais de uma marca temporal. Ver também [getPastTime](control-flow-expression-language-functions.md#getPastTime). |
| [tiques](control-flow-expression-language-functions.md#ticks) | Devolva o valor da `ticks` propriedade por um carimbo de tempo especificado. |
| [utcNow](control-flow-expression-language-functions.md#utcNow) | Devolva o carimbo de tempo atual como uma corda. |

## <a name="function-reference"></a>Referência de função

Esta secção lista todas as funções disponíveis por ordem alfabética.

<a name="add"></a>

### <a name="add"></a>adicionar

Devolva o resultado da adição de dois números.

```
add(<summand_1>, <summand_2>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, *<summand_2*> | Sim | Inteiro, Flutuador ou Misto | Os números a adicionar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | -----| ----------- |
| <*resultado-soma*> | Inteiro ou Flutuador | O resultado da adição dos números especificados |
||||

*Exemplo*

Este exemplo adiciona os números especificados:

```
add(1, 1.5)
```

E devolve este resultado:`2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Adicione alguns dias a uma marca de tempo.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | String | A corda que contém o carimbo de tempo |
| <*dias*> | Sim | Número inteiro | O número positivo ou negativo de dias a adicionar |
| <*formato*> | Não | String | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atualizado*> | String | A marca de tempo mais o número especificado de dias  |
||||

*Exemplo 1*

Este exemplo adiciona 10 dias ao carimbo de tempo especificado:

```
addDays('2018-03-15T13:00:00Z', 10)
```

E devolve este resultado:`"2018-03-25T00:00:0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco dias do carimbo de tempo especificado:

```
addDays('2018-03-15T00:00:00Z', -5)
```

E devolve este resultado:`"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Adicione algumas horas a um carimbo temporal.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | String | A corda que contém o carimbo de tempo |
| <*horas*> | Sim | Número inteiro | O número positivo ou negativo de horas a adicionar |
| <*formato*> | Não | String | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atualizado*> | String | A marca de tempo mais o número especificado de horas  |
||||

*Exemplo 1*

Este exemplo adiciona 10 horas ao carimbo de tempo especificado:

```
addHours('2018-03-15T00:00:00Z', 10)
```

E devolve este resultado:`"2018-03-15T10:00:0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco horas do carimbo de tempo especificado:

```
addHours('2018-03-15T15:00:00Z', -5)
```

E devolve este resultado:`"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Adicione alguns minutos a uma marca de tempo.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | String | A corda que contém o carimbo de tempo |
| <*minutos*> | Sim | Número inteiro | O número positivo ou negativo de minutos a adicionar |
| <*formato*> | Não | String | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atualizado*> | String | A marca de tempo mais o número especificado de minutos |
||||

*Exemplo 1*

Este exemplo adiciona 10 minutos à marca ção especificada:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

E devolve este resultado:`"2018-03-15T00:20:00.0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco minutos da marca ção especificada:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

E devolve este resultado:`"2018-03-15T00:15:00.0000000Z"`

<a name="addSeconds"></a>

### <a name="addseconds"></a>adicionarSeconds

Adicione alguns segundos a uma marca de tempo.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | String | A corda que contém o carimbo de tempo |
| <*segundos*> | Sim | Número inteiro | O número positivo ou negativo de segundos a adicionar |
| <*formato*> | Não | String | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atualizado*> | String | A marca de tempo mais o número especificado de segundos  |
||||

*Exemplo 1*

Este exemplo adiciona 10 segundos à marca ção especificada:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

E devolve este resultado:`"2018-03-15T00:00:10.0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco segundos para a marca de tempo especificada:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

E devolve este resultado:`"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>adicionarToTime

Adicione várias unidades de tempo a uma marca de tempo.
Consulte também [o FutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | String | A corda que contém o carimbo de tempo |
| <*intervalo*> | Sim | Número inteiro | O número de unidades de tempo especificadas para adicionar |
| <*timeUnit*> | Sim | String | A unidade de tempo para usar com *intervalo*: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*formato*> | Não | String | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atualizado*> | String | A marca de tempo mais o número especificado de unidades de tempo  |
||||

*Exemplo 1*

Este exemplo adiciona um dia à marca de tempo especificada:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

E devolve este resultado:`"2018-01-02T00:00:00.0000000Z"`

*Exemplo 2*

Este exemplo adiciona um dia à marca de tempo especificada:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

E devolve o resultado utilizando o formato "D" opcional:`"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>e

Verifique se todas as expressões são verdadeiras.
Volte a ser verdade quando todas as expressões forem verdadeiras, ou volte a ser falsa quando pelo menos uma expressão é falsa.

```
and(<expression1>, <expression2>, ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expressão1*>, *expressão <2*>, ... | Sim | Booleano | As expressões para verificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | -----| ----------- |
| true ou false | Booleano | Volte a ser verdade quando todas as expressões forem verdadeiras. Volte falso quando pelo menos uma expressão é falsa. |
||||

*Exemplo 1*

Estes exemplos verificam se os valores booleanos especificados são todos verdadeiros:

```
and(true, true)
and(false, true)
and(false, false)
```

E devolve estes resultados:

* Primeiro exemplo: Ambas as expressões `true`são verdadeiras, por isso os retornos.
* Segundo exemplo: Uma expressão é `false`falsa, por isso devolve.
* Terceiro exemplo: Ambas as expressões `false`são falsas, por isso devoluções .

*Exemplo 2*

Estes exemplos verificam se as expressões especificadas são todas verdadeiras:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

E devolve estes resultados:

* Primeiro exemplo: Ambas as expressões `true`são verdadeiras, por isso os retornos.
* Segundo exemplo: Uma expressão é `false`falsa, por isso devolve.
* Terceiro exemplo: Ambas as expressões `false`são falsas, por isso devoluções .

<a name="array"></a>

### <a name="array"></a>array

Devolva uma matriz de uma única entrada especificada.
Para várias inputs, consulte [createArray()](#createArray).

```
array('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A corda para criar uma matriz |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<*valor*>] | Matriz | Uma matriz que contém a única entrada especificada |
||||

*Exemplo*

Este exemplo cria uma matriz a partir da corda "olá":

```
array('hello')
```

E devolve este resultado:`["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Devolva a versão codificada base64 para uma corda.

```
base64('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia de entrada |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*base64-string*> | String | A versão codificada base64 para a cadeia de entrada |
||||

*Exemplo*

Este exemplo converte a corda "olá" para uma cadeia codificada base64:

```
base64('hello')
```

E devolve este resultado:`"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Volte a colocar a versão binária numa cadeia codificada com base64.

```
base64ToBinary('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia codificada base64 para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*binário-para-base64-string*> | String | A versão binária para a cadeia codificada base64 |
||||

*Exemplo*

Este exemplo converte a cadeia "aGVsbG8=" base64-codificada numa cadeia binária:

```
base64ToBinary('aGVsbG8=')
```

E devolve este resultado:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64Tostring

Volte a colocar a versão de corda para uma cadeia codificada base64, descodificando eficazmente a cadeia base64.
Utilize esta função em vez [de descodificarBase64()](#decodeBase64).
Embora ambas as funções `base64ToString()` funcionem da mesma forma, é preferível.

```
base64ToString('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia codificada base64 para descodificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*descodificada-base64-string*> | String | A versão de corda para uma cadeia codificada base64 |
||||

*Exemplo*

Este exemplo converte a cadeia "aGVsbG8=" base64 codificada em apenas uma corda:

```
base64ToString('aGVsbG8=')
```

E devolve este resultado:`"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

Devolva a versão binária por uma corda.

```
binary('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A corda para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*binário-para-input-valor*> | String | A versão binária para a corda especificada |
||||

*Exemplo*

Este exemplo converte a corda "olá" para uma corda binária:

```
binary('hello')
```

E devolve este resultado:

`"0110100001100101011011000110110001101111"`

<a name="bool"></a>

### <a name="bool"></a>bool

Devolva a versão Boolean por um valor.

```
bool(<value>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Qualquer | O valor para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | A versão Boolean para o valor especificado |
||||

*Exemplo*

Estes exemplos convertem os valores especificados em valores booleanos:

```
bool(1)
bool(0)
```

E devolve estes resultados:

* Primeiro exemplo:`true`
* Segundo exemplo:`false`

<a name="coalesce"></a>

### <a name="coalesce"></a>coalesce

Devolva o primeiro valor não nulo de um ou mais parâmetros.
Cordas vazias, matrizes vazias e objetos vazios não são nulos.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object_1*>,> *de object_2* <, ... | Sim | Qualquer, pode misturar tipos | Um ou mais itens para verificar se há nulos |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*primeiro-não-nulo-item*> | Qualquer | O primeiro item ou valor que não é nulo. Se todos os parâmetros forem nulos, esta função devolve-se a nulas. |
||||

*Exemplo*

Estes exemplos devolvem o primeiro valor não nulo dos valores especificados, ou nulos quando todos os valores são nulos:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

E devolve estes resultados:

* Primeiro exemplo:`true`
* Segundo exemplo:`"hello"`
* Terceiro exemplo:`null`

<a name="concat"></a>

### <a name="concat"></a>concat

Combine duas ou mais cordas e devolva a corda combinada.

```
concat('<text1>', '<text2>', ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*> de texto1,* <*texto2*>, ... | Sim | String | Pelo menos duas cordas para combinar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*texto1texto2...*> | String | A cadeia criada a partir das cordas combinadas de entrada |
||||

*Exemplo*

Este exemplo combina as cordas "Olá" e "Mundo":

```
concat('Hello', 'World')
```

E devolve este resultado:`"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>contém

Verifique se uma coleção tem um item específico.
Volte a ser verdade quando o artigo for encontrado, ou volte a ser falso quando não for encontrado.
Esta função é sensível ao caso.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Especificamente, esta função funciona nestes tipos de recolha:

* Uma *corda* para encontrar uma *subcorda*
* Uma *matriz* para encontrar um *valor*
* Um *dicionário* para encontrar uma *chave*

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Corda, Matriz ou Dicionário | A coleção para verificar |
| <*valor*> | Sim | String, Array ou Dicionário, respectivamente | O item para encontrar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Volte a ser verdade quando o artigo for encontrado. Devolver falso quando não for encontrado. |
||||

*Exemplo 1*

Este exemplo verifica a corda "hello world" para o substring "mundo" e retorna verdadeira:

```
contains('hello world', 'world')
```

*Exemplo 2*

Este exemplo verifica a corda "hello world" para o substring "universo" e devolve falso:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>converterFromUtc

Converta uma marca de tempo da Universal Time Coordenada (UTC) para o fuso horário-alvo.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | String | A corda que contém o carimbo de tempo |
| <*destinoTimeZone*> | Sim | String | O nome do fuso horário alvo. Para obter nomes de fusos horários, consulte os valores do Índice do [Fuso Horário](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)do Microsoft, mas poderá ter de remover qualquer pontuação do nome do fuso horário. |
| <*formato*> | Não | String | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo convertido*> | String | O carimbo de tempo convertido para o fuso horário alvo |
||||

*Exemplo 1*

Este exemplo converte uma marca de tempo para o fuso horário especificado:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

E devolve este resultado:`"2018-01-01T00:00:00.0000000"`

*Exemplo 2*

Este exemplo converte uma marca de tempo para o fuso horário e formato especificados:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

E devolve este resultado:`"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>converterTimeZone

Converta uma marca de tempo do fuso horário de origem para o fuso horário alvo.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | String | A corda que contém o carimbo de tempo |
| <*fonteTimeZone*> | Sim | String | O nome do fuso horário de origem. Para obter nomes de fusos horários, consulte os valores do Índice do [Fuso Horário](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)do Microsoft, mas poderá ter de remover qualquer pontuação do nome do fuso horário. |
| <*destinoTimeZone*> | Sim | String | O nome do fuso horário alvo. Para obter nomes de fusos horários, consulte os valores do Índice do [Fuso Horário](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)do Microsoft, mas poderá ter de remover qualquer pontuação do nome do fuso horário. |
| <*formato*> | Não | String | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo convertido*> | String | O carimbo de tempo convertido para o fuso horário alvo |
||||

*Exemplo 1*

Este exemplo converte o fuso horário de origem para o fuso horário alvo:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

E devolve este resultado:`"2018-01-01T00:00:00.0000000"`

*Exemplo 2*

Este exemplo converte um fuso horário para o fuso horário e formato especificados:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

E devolve este resultado:`"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>converteToUtc

Converter uma marca de tempo do fuso horário de origem para Universal Time Coordenado (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | String | A corda que contém o carimbo de tempo |
| <*fonteTimeZone*> | Sim | String | O nome do fuso horário de origem. Para obter nomes de fusos horários, consulte os valores do Índice do [Fuso Horário](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)do Microsoft, mas poderá ter de remover qualquer pontuação do nome do fuso horário. |
| <*formato*> | Não | String | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo convertido*> | String | O carimbo de tempo convertido em UTC |
||||

*Exemplo 1*

Este exemplo converte uma marca de tempo para UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

E devolve este resultado:`"2018-01-01T08:00:00.0000000Z"`

*Exemplo 2*

Este exemplo converte uma marca de tempo para UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

E devolve este resultado:`"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>criar Array

Devolva uma matriz de várias inputs.
Para uma única dosmina de entrada, consulte [a matriz()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*objeto1*>, <*objeto2*>, ... | Sim | Qualquer, mas não misturado | Pelo menos dois itens para criar a matriz |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<*objeto1*>, <*objeto2*>, ...] | Matriz | A matriz criada a partir de todos os itens de entrada |
||||

*Exemplo*

Este exemplo cria uma matriz a partir destas inputs:

```
createArray('h', 'e', 'l', 'l', 'o')
```

E devolve este resultado:`["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Devolva um identificador de recursos uniforme de dados (URI) para uma cadeia.

```
dataUri('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A corda para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*data-uri*> | String | Os dados URI para a cadeia de entrada |
||||

*Exemplo*

Este exemplo cria um URI de dados para a cadeia "olá":

```
dataUri('hello')
```

E devolve este resultado:`"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Devolva a versão binária para um identificador de recursos uniforme de dados (URI).
Utilize esta função em vez [de descodificarDataUri()](#decodeDataUri).
Embora ambas as funções `dataUriBinary()` funcionem da mesma forma, é preferível.

```
dataUriToBinary('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | Os dados URI para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*binário-para-dados-uri*> | String | A versão binária para os dados URI |
||||

*Exemplo*

Este exemplo cria uma versão binária para estes dados URI:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E devolve este resultado:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>datauritostring

Devolva a versão de cadeia para um identificador de recursos uniforme de dados (URI).

```
dataUriToString('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | Os dados URI para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | String | A versão de cadeia para os dados URI |
||||

*Exemplo*

Este exemplo cria uma cadeia para estes dados URI:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E devolve este resultado:`"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Volte o dia do mês de uma marca de tempo.

```
dayOfMonth('<timestamp>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | String | A corda que contém o carimbo de tempo |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*dia do mês*> | Número inteiro | O dia do mês a partir do carimbo de tempo especificado |
||||

*Exemplo*

Este exemplo devolve o número do dia do mês a partir deste carimbo temporal:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

E devolve este resultado:`15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Volte o dia da semana de uma marca de tempo.

```
dayOfWeek('<timestamp>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | String | A corda que contém o carimbo de tempo |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*dia da semana*> | Número inteiro | O dia da semana a partir do carimbo de tempo especificado onde domingo é 0, segunda-feira é 1, e assim por diante |
||||

*Exemplo*

Este exemplo devolve o número para o dia da semana a partir deste carimbo temporal:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

E devolve este resultado:`3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dia ofyear

Volte o dia do ano de uma marca de tempo.

```
dayOfYear('<timestamp>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | String | A corda que contém o carimbo de tempo |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*dia do ano*> | Número inteiro | O dia do ano a partir do carimbo de tempo especificado |
||||

*Exemplo*

Este exemplo devolve o número do dia do ano a partir deste carimbo temporal:

```
dayOfYear('2018-03-15T13:27:36Z')
```

E devolve este resultado:`74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>descodificarBase64

Volte a colocar a versão de corda para uma cadeia codificada base64, descodificando eficazmente a cadeia base64.
Considere usar [base64ToString()](#base64ToString) em vez de `decodeBase64()`.
Embora ambas as funções `base64ToString()` funcionem da mesma forma, é preferível.

```
decodeBase64('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia codificada base64 para descodificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*descodificada-base64-string*> | String | A versão de corda para uma cadeia codificada base64 |
||||

*Exemplo*

Este exemplo cria uma cadeia para uma cadeia codificada base64:

```
decodeBase64('aGVsbG8=')
```

E devolve este resultado:`"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>descodificarDataUri

Devolva a versão binária para um identificador de recursos uniforme de dados (URI).
Considere utilizar [dadosUriToBinary() ()](#dataUriToBinary)em vez de `decodeDataUri()`.
Embora ambas as funções `dataUriToBinary()` funcionem da mesma forma, é preferível.

```
decodeDataUri('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia URI de dados para descodificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*binário-para-dados-uri*> | String | A versão binária para uma cadeia URI de dados |
||||

*Exemplo*

Este exemplo devolve a versão binária para estes dados URI:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E devolve este resultado:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>descodificar Componente Uri

Volte a colocar uma corda que substitua caracteres de fuga por versões descodificadas.

```
decodeUriComponent('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A corda com os personagens de fuga para descodificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*descodificado-uri*> | String | A cadeia atualizada com os caracteres de fuga descodificados |
||||

*Exemplo*

Este exemplo substitui os caracteres de fuga nesta cadeia por versões descodificadas:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

E devolve este resultado:`"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Devolva o resultado inteiro da divisão de dois números.
Para obter o resultado remanescente, consulte [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*dividendo*> | Sim | Inteiro ou Flutuador | O número a dividir pelo *divisor* |
| <*divisor*> | Sim | Inteiro ou Flutuador | O número que divide o *dividendo,* mas não pode ser 0 |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado quociente*> | Número inteiro | O resultado inteiro de dividir o primeiro número pelo segundo número |
||||

*Exemplo*

Ambos os exemplos dividem o primeiro número pelo segundo número:

```
div(10, 5)
div(11, 5)
```

E devolva este resultado:`2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>codificar Componente UriUri

Volte a colocar uma versão codificada por um identificador de recursos uniforme (URI) para uma cadeia, substituindo caracteres inseguros de URL por caracteres de fuga.
Considere utilizar [uriComponent()](#uriComponent) `encodeUriComponent()`em vez de .
Embora ambas as funções `uriComponent()` funcionem da mesma forma, é preferível.

```
encodeUriComponent('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A corda para converter em formato codificado por URI |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*codificado-uri*> | String | A corda codificada uri com caracteres de fuga |
||||

*Exemplo*

Este exemplo cria uma versão codificada por URI para esta cadeia:

```
encodeUriComponent('https://contoso.com')
```

E devolve este resultado:`"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>vazio

Verifique se uma coleção está vazia.
Volte a ser verdade quando a coleção estiver vazia, ou volte a ser falsa quando não estiver vazia.

```
empty('<collection>')
empty([<collection>])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Corda, Matriz ou Objeto | A coleção para verificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Volte a ser verdade quando a coleção estiver vazia. Volte falso quando não estiver vazio. |
||||

*Exemplo*

Estes exemplos verificam se as coleções especificadas estão vazias:

```
empty('')
empty('abc')
```

E devolve estes resultados:

* Primeiro exemplo: Passa uma corda vazia, `true`para que a função retorne .
* Segundo exemplo: passa a corda "abc", `false`para que a função retorne.

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Verifique se uma corda termina com um substring específico.
Volte a ser verdade quando o substring for encontrado, ou volte a ser falso quando não for encontrado.
Esta função não é sensível a casos.

```
endsWith('<text>', '<searchText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*texto*> | Sim | String | A corda para verificar |
| <*pesquisarTexto*> | Sim | String | O substring final para encontrar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false  | Booleano | Volte a ser verdade quando o substring final for encontrado. Devolver falso quando não for encontrado. |
||||

*Exemplo 1*

Este exemplo verifica se a corda "hello world" termina com a corda "mundo":

```
endsWith('hello world', 'world')
```

E devolve este resultado:`true`

*Exemplo 2*

Este exemplo verifica se a corda "hello world" termina com a corda "universo":

```
endsWith('hello world', 'universe')
```

E devolve este resultado:`false`

<a name="equals"></a>

### <a name="equals"></a>equals

Verifique se ambos os valores, expressões ou objetos são equivalentes.
Volte a ser verdade quando ambos são equivalentes, ou devolva falsoquando não são equivalentes.

```
equals('<object1>', '<object2>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*objeto1*>, objeto *<2*> | Sim | Vários | Os valores, expressões ou objetos para comparar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Volte a ser verdade quando ambos são equivalentes. Devolver falso quando não equivalente. |
||||

*Exemplo*

Estes exemplos verificam se as inputs especificadas são equivalentes.

```
equals(true, 1)
equals('abc', 'abcd')
```

E devolve estes resultados:

* Primeiro exemplo: Ambos os valores são `true`equivalentes, pelo que a função retorna .
* Segundo exemplo: Ambos os valores não são `false`equivalentes, pelo que a função retorna .

<a name="first"></a>

### <a name="first"></a>primeiro

Volte a devolver o primeiro item de uma corda ou matriz.

```
first('<collection>')
first([<collection>])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Corda ou Matriz | A coleção onde encontrar o primeiro item |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*primeiro-coleção-item*> | Qualquer | O primeiro item da coleção |
||||

*Exemplo*

Estes exemplos encontram o primeiro item nestas coleções:

```
first('hello')
first(createArray(0, 1, 2))
```

E devolver estes resultados:

* Primeiro exemplo:`"h"`
* Segundo exemplo:`0`

<a name="float"></a>

### <a name="float"></a>float

Converta uma versão de corda para um número de ponto flutuante para um número de ponto flutuante real.

```
float('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A corda que tem um número de ponto flutuante válido para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor flutuante*> | Float | O número de ponto flutuante para a corda especificada |
||||

*Exemplo*

Este exemplo cria uma versão de cadeia para este número de ponto flutuante:

```
float('10.333')
```

E devolve este resultado:`10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatoDateTime

Devolva uma marca de tempo no formato especificado.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | String | A corda que contém o carimbo de tempo |
| <*formato*> | Não | String | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*selo de tempo reformada*> | String | A marca de tempo atualizada no formato especificado |
||||

*Exemplo*

Este exemplo converte uma marca de tempo para o formato especificado:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

E devolve este resultado:`"2018-03-15T12:00:00"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Volte a colocar a marca atual mais as unidades de tempo especificadas.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*intervalo*> | Sim | Número inteiro | O número de unidades de tempo especificadas para adicionar |
| <*timeUnit*> | Sim | String | A unidade de tempo para usar com *intervalo*: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*formato*> | Não | String | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atualizado*> | String | O carimbo de tempo atual mais o número especificado de unidades de tempo |
||||

*Exemplo 1*

Suponha que o atual carimbo temporal é "2018-03-01T00:00.00.00000000Z".
Este exemplo adiciona cinco dias a essa marca de tempo:

```
getFutureTime(5, 'Day')
```

E devolve este resultado:`"2018-03-06T00:00:00.0000000Z"`

*Exemplo 2*

Suponha que o atual carimbo temporal é "2018-03-01T00:00.00.00000000Z".
Este exemplo adiciona cinco dias e converte o resultado em formato "D":

```
getFutureTime(5, 'Day', 'D')
```

E devolve este resultado:`"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Volte a colocar a marca atual menos as unidades de tempo especificadas.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*intervalo*> | Sim | Número inteiro | O número de unidades de tempo especificadas para subtrair |
| <*timeUnit*> | Sim | String | A unidade de tempo para usar com *intervalo*: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*formato*> | Não | String | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atualizado*> | String | O carimbo de tempo atual menos o número especificado de unidades de tempo |
||||

*Exemplo 1*

Suponha que o atual carimbo temporal é "2018-02-01T00:00.00.00000000Z".
Este exemplo subtrai cinco dias a partir desse carimbo temporal:

```
getPastTime(5, 'Day')
```

E devolve este resultado:`"2018-01-27T00:00:00.0000000Z"`

*Exemplo 2*

Suponha que o atual carimbo temporal é "2018-02-01T00:00.00.00000000Z".
Este exemplo subtrai cinco dias e converte o resultado em formato "D":

```
getPastTime(5, 'Day', 'D')
```

E devolve este resultado:`"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Verifique se o primeiro valor é superior ao segundo valor.
Volte a ser verdade quando o primeiro valor for mais, ou volte a ser falso quando menos.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, Flutuador ou Corda | O primeiro valor para verificar se maior do que o segundo valor |
| <*comparar*> | Sim | Inteiro, Flutuador ou Corda, respectivamente | O valor de comparação |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Volte a ser verdade quando o primeiro valor é maior que o segundo valor. Devolver falso quando o primeiro valor for igual ou inferior ao segundo valor. |
||||

*Exemplo*

Estes exemplos verificam se o primeiro valor é superior ao segundo valor:

```
greater(10, 5)
greater('apple', 'banana')
```

E devolver estes resultados:

* Primeiro exemplo:`true`
* Segundo exemplo:`false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Verifique se o primeiro valor é maior ou igual ao segundo valor.
Volte a ser verdade quando o primeiro valor for maior ou igual, ou volte a ser falso quando o primeiro valor for menor.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, Flutuador ou Corda | O primeiro valor para verificar se maior ou igual ao segundo valor |
| <*comparar*> | Sim | Inteiro, Flutuador ou Corda, respectivamente | O valor de comparação |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Volte a ser verdade quando o primeiro valor for maior ou igual ao segundo valor. Devolver falso quando o primeiro valor for inferior ao segundo valor. |
||||

*Exemplo*

Estes exemplos verificam se o primeiro valor é maior ou igual ao segundo valor:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

E devolver estes resultados:

* Primeiro exemplo:`true`
* Segundo exemplo:`false`

<a name="guid"></a>

### <a name="guid"></a>guid

Gerar um identificador globalmente único (GUID) como uma corda, por exemplo, "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Além disso, pode especificar um formato diferente para o GUID que não seja o formato predefinido, "D", que é de 32 dígitos separados por hífenes.

```
guid('<format>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*formato*> | Não | String | Um [único especificador](https://msdn.microsoft.com/library/97af8hh4) de formato para o GUID devolvido. Por padrão, o formato é "D", mas pode utilizar "N", "D", "B", "P" ou "X". |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*Valor-guia*> | String | Um GUID gerado aleatoriamente |
||||

*Exemplo*

Este exemplo gera o mesmo GUID, mas como 32 dígitos, separados por hífenes, e fechados em parênteses:

```
guid('P')
```

E devolve este resultado:`"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Verifique se uma expressão é verdadeira ou falsa.
Com base no resultado, devolva um valor especificado.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expressão*> | Sim | Booleano | A expressão para verificar |
| <*valorIfTrue*> | Sim | Qualquer | O valor a devolver quando a expressão é verdadeira |
| <*valorIfFalso*> | Sim | Qualquer | O valor a devolver quando a expressão é falsa |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor de retorno especificado*> | Qualquer | O valor especificado que retorna com base em se a expressão é verdadeira ou falsa |
||||

*Exemplo*

Este exemplo `"yes"` retorna porque a expressão especificada retorna verdadeira.
Caso contrário, o `"no"`exemplo retorna:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexof

Volte a colocar a posição inicial ou o valor do índice para um substring.
Esta função não é sensível a casos, e os índices começam com o número 0.

```
indexOf('<text>', '<searchText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*texto*> | Sim | String | A corda que tem o substring para encontrar |
| <*pesquisarTexto*> | Sim | String | O substring para encontrar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor de índice*>| Número inteiro | A posição inicial ou o valor do índice para o substring especificado. <p>Se a corda não for encontrada, devolva o número -1. |
||||

*Exemplo*

Este exemplo encontra o valor do índice inicial para o substring "mundo" na cadeia "hello world":

```
indexOf('hello world', 'world')
```

E devolve este resultado:`6`

<a name="int"></a>

### <a name="int"></a>int

Devolva a versão inteiro para uma corda.

```
int('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A corda para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado inteiro*> | Número inteiro | A versão inteiro para a corda especificada |
||||

*Exemplo*

Este exemplo cria uma versão inteiro para a corda "10":

```
int('10')
```

E devolve este resultado:`10`

<a name="json"></a>

### <a name="json"></a>json

Devolva o valor ou objeto do tipo JavaScript Object (JSON) para uma corda ou XML.

```
json('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Corda ou XML | A corda ou XML para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*Resultado da JSON*> | Tipo ou objeto nativo JSON | O valor ou objeto do tipo nativo JSON para a corda especificada ou XML. Se a corda for nula, a função devolve um objeto vazio. |
||||

*Exemplo 1*

Este exemplo converte esta cadeia para o valor JSON:

```
json('[1, 2, 3]')
```

E devolve este resultado:`[1, 2, 3]`

*Exemplo 2*

Este exemplo converte esta cadeia para JSON:

```
json('{"fullName": "Sophia Owen"}')
```

E devolve este resultado:

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

E devolve este resultado:

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

### <a name="intersection"></a>cruzamento

Devolva uma coleção que tenha *apenas* os itens comuns nas coleções especificadas.
Para aparecer no resultado, deve aparecer um item em todas as coleções passadas para esta função.
Se um ou mais itens tiverem o mesmo nome, o último item com esse nome aparece no resultado.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção1*>, *<coleção2*>, ... | Sim | Matriz ou Objeto, mas não ambos | As coleções de onde você quer *apenas* os itens comuns |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*itens comuns*> | Matriz ou Objeto, respectivamente | Uma coleção que tem apenas os itens comuns em todas as coleções especificadas |
||||

*Exemplo*

Este exemplo encontra os itens comuns através destas matrizes:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

E devolve uma matriz com *apenas* estes itens:`[1, 2]`

<a name="join"></a>

### <a name="join"></a>aderir

Volte a colocar uma corda que tenha todos os itens de uma matriz e tenha cada personagem separado por um *delimitador*.

```
join([<collection>], '<delimiter>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Matriz | A matriz que tem os itens para se juntar |
| <*delimitador*> | Sim | String | O separador que aparece entre cada personagem na corda resultante |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*char1*><*delimitador*><*char2*><*delimitador*>... | String | A cadeia resultante criada a partir de todos os itens na matriz especificada |
||||

*Exemplo*

Este exemplo cria uma cadeia de todos os itens desta matriz com o caráter especificado como delimitador:

```
join(createArray('a', 'b', 'c'), '.')
```

E devolve este resultado:`"a.b.c"`

<a name="last"></a>

### <a name="last"></a>última

Devolva o último item de uma coleção.

```
last('<collection>')
last([<collection>])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Corda ou Matriz | A coleção onde encontrar o último item |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*último-colecção-item*> | String ou Array, respectivamente | O último item da coleção |
||||

*Exemplo*

Estes exemplos encontram o último item nestas coleções:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

E devolve estes resultados:

* Primeiro exemplo:`"d"`
* Segundo exemplo:`3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>últimoIndexOf

Volte a colocar a posição inicial ou o valor do índice para a última ocorrência de um substring.
Esta função não é sensível a casos, e os índices começam com o número 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*texto*> | Sim | String | A corda que tem o substring para encontrar |
| <*pesquisarTexto*> | Sim | String | O substring para encontrar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor do índice final*> | Número inteiro | A posição inicial ou o valor do índice para a última ocorrência do substring especificado. <p>Se a corda não for encontrada, devolva o número -1. |
||||

*Exemplo*

Este exemplo encontra o valor do índice inicial para a última ocorrência do substring "mundo" na cadeia "hello world":

```
lastIndexOf('hello world', 'world')
```

E devolve este resultado:`6`

<a name="length"></a>

### <a name="length"></a>length

Devolva o número de artigos numa coleção.

```
length('<collection>')
length([<collection>])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Corda ou Matriz | A coleção com os itens para contar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*comprimento ou contagem*> | Número inteiro | O número de itens na coleção |
||||

*Exemplo*

Estes exemplos contam o número de itens nestas coleções:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

E devolva este resultado:`4`

<a name="less"></a>

### <a name="less"></a>less

Verifique se o primeiro valor é inferior ao segundo valor.
Volte a ser verdade quando o primeiro valor for menor, ou volte a ser falso quando o primeiro valor for mais.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, Flutuador ou Corda | O primeiro valor para verificar se menos do que o segundo valor |
| <*comparar*> | Sim | Inteiro, Flutuador ou Corda, respectivamente | O item de comparação |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Volte a ser verdade quando o primeiro valor for inferior ao segundo valor. Devolver falso quando o primeiro valor for igual ou superior ao segundo valor. |
||||

*Exemplo*

Estes exemplos verificam se o primeiro valor é inferior ao segundo valor.

```
less(5, 10)
less('banana', 'apple')
```

E devolver estes resultados:

* Primeiro exemplo:`true`
* Segundo exemplo:`false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Verifique se o primeiro valor é inferior ou igual ao segundo valor.
Volte a ser verdade quando o primeiro valor for inferior ou igual, ou volte a ser falso quando o primeiro valor for mais.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, Flutuador ou Corda | O primeiro valor para verificar se menos ou igual ao segundo valor |
| <*comparar*> | Sim | Inteiro, Flutuador ou Corda, respectivamente | O item de comparação |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false  | Booleano | Volte a ser verdade quando o primeiro valor for inferior ou igual ao segundo valor. Devolver falso quando o primeiro valor for maior do que o segundo valor. |
||||

*Exemplo*

Estes exemplos verificam se o primeiro valor é inferior ou igual ao segundo valor.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

E devolver estes resultados:

* Primeiro exemplo:`true`
* Segundo exemplo:`false`

<a name="max"></a>

### <a name="max"></a>máximo

Devolva o valor mais alto de uma lista ou matriz com números que são inclusivos em ambas as extremidades.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*número1*>, <*número2*>, ... | Sim | Inteiro, Float, ou ambos | O conjunto de números a partir do qual quer o maior valor |
| [<*> número 1,* <*número 2*>, ...] | Sim | Array - Inteiro, Flutuador, ou ambos | A variedade de números a partir dos quais quer o maior valor |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor máximo*> | Inteiro ou Flutuador | O valor mais elevado na matriz ou conjunto de números especificados |
||||

*Exemplo*

Estes exemplos obtêm o maior valor a partir do conjunto de números e da matriz:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

E devolva este resultado:`3`

<a name="min"></a>

### <a name="min"></a>min

Devolva o valor mais baixo de um conjunto de números ou de uma matriz.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*número1*>, <*número2*>, ... | Sim | Inteiro, Float, ou ambos | O conjunto de números a partir do qual quer o valor mais baixo |
| [<*> número 1,* <*número 2*>, ...] | Sim | Array - Inteiro, Flutuador, ou ambos | A variedade de números a partir dos quais quer o valor mais baixo |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*min-valor*> | Inteiro ou Flutuador | O valor mais baixo no conjunto especificado de números ou matriz especificada |
||||

*Exemplo*

Estes exemplos obtêm o valor mais baixo no conjunto de números e na matriz:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

E devolva este resultado:`1`

<a name="mod"></a>

### <a name="mod"></a>mod

Devolva o restante da divisão de dois números.
Para obter o resultado inteiro, consulte [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*dividendo*> | Sim | Inteiro ou Flutuador | O número a dividir pelo *divisor* |
| <*divisor*> | Sim | Inteiro ou Flutuador | O número que divide o *dividendo,* mas não pode ser 0. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*modulo-resultado*> | Inteiro ou Flutuador | O restante de dividir o primeiro número pelo segundo número |
||||

*Exemplo*

Este exemplo divide o primeiro número pelo segundo número:

```
mod(3, 2)
```

E devolva este resultado:`1`

<a name="mul"></a>

### <a name="mul"></a>mul

Devolva o produto de multiplicar dois números.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*multiplicador1*> | Sim | Inteiro ou Flutuador | O número a multiplicar por *multiplicador2* |
| <*multiplicador2*> | Sim | Inteiro ou Flutuador | O número que múltiplos *multiplicam1* |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado do produto*> | Inteiro ou Flutuador | O produto de multiplicar o primeiro número pelo segundo número |
||||

*Exemplo*

Estes exemplos múltiplos o primeiro número pelo segundo número:

```
mul(1, 2)
mul(1.5, 2)
```

E devolver estes resultados:

* Primeiro exemplo:`2`
* Segundo exemplo`3`

<a name="not"></a>

### <a name="not"></a>not

Verifique se uma expressão é falsa.
Volte a ser verdade quando a expressão é falsa, ou retorne falsa quando for verdadeira.

```json
not(<expression>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expressão*> | Sim | Booleano | A expressão para verificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Volte a ser verdade quando a expressão é falsa. Volte falso quando a expressão for verdadeira. |
||||

*Exemplo 1*

Estes exemplos verificam se as expressões especificadas são falsas:

```json
not(false)
not(true)
```

E devolver estes resultados:

* Primeiro exemplo: A expressão é falsa, `true`por isso a função retorna .
* Segundo exemplo: A expressão é verdadeira, `false`por isso a função retorna .

*Exemplo 2*

Estes exemplos verificam se as expressões especificadas são falsas:

```json
not(equals(1, 2))
not(equals(1, 1))
```

E devolver estes resultados:

* Primeiro exemplo: A expressão é falsa, `true`por isso a função retorna .
* Segundo exemplo: A expressão é verdadeira, `false`por isso a função retorna .

<a name="or"></a>

### <a name="or"></a>ou

Verifique se pelo menos uma expressão é verdadeira.
Volte a ser verdade quando pelo menos uma expressão for verdadeira, ou volte a ser falsa quando todas são falsas.

```
or(<expression1>, <expression2>, ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expressão1*>, *expressão <2*>, ... | Sim | Booleano | As expressões para verificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Volte a ser verdade quando pelo menos uma expressão é verdadeira. Volte falso quando todas as expressões forem falsas. |
||||

*Exemplo 1*

Estes exemplos verificam se pelo menos uma expressão é verdadeira:

```json
or(true, false)
or(false, false)
```

E devolver estes resultados:

* Primeiro exemplo: Pelo menos uma expressão é `true`verdadeira, pelo que a função retorna .
* Segundo exemplo: Ambas as expressões são `false`falsas, pelo que a função retorna .

*Exemplo 2*

Estes exemplos verificam se pelo menos uma expressão é verdadeira:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

E devolver estes resultados:

* Primeiro exemplo: Pelo menos uma expressão é `true`verdadeira, pelo que a função retorna .
* Segundo exemplo: Ambas as expressões são `false`falsas, pelo que a função retorna .

<a name="rand"></a>

### <a name="rand"></a>rand

Devolva um inteiro aleatório de uma gama especificada, que só é inclusiva no final inicial.

```
rand(<minValue>, <maxValue>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Sim | Número inteiro | O inteiro mais baixo da gama |
| <*maxValue*> | Sim | Número inteiro | O inteiro que segue o maior inteiro na gama que a função pode devolver |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado aleatório*> | Número inteiro | O inteiro aleatório voltou da gama especificada |
||||

*Exemplo*

Este exemplo obtém um inteiro aleatório da gama especificada, excluindo o valor máximo:

```
rand(1, 5)
```

E devolve um destes números `2` `3`como resultado: `1`, , ou`4`

<a name="range"></a>

### <a name="range"></a>alcance

Devolva uma matriz inteiro que começa a partir de um inteiro especificado.

```
range(<startIndex>, <count>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | Sim | Número inteiro | Um valor inteiro que começa a matriz como o primeiro item |
| <*contar*> | Sim | Número inteiro | O número de inteiros na matriz |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<> *de resultados de alcance]* | Matriz | A matriz com inteiros a partir do índice especificado |
||||

*Exemplo*

Este exemplo cria uma matriz inteiro que começa a partir do índice especificado e tem o número especificado de inteiros:

```
range(1, 4)
```

E devolve este resultado:`[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>substituir

Substitua uma subcadeia com a corda especificada e devolva a corda de resultado. Esta função é sensível ao caso.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*texto*> | Sim | String | A corda que tem a subcadeia para substituir |
| <*oldText*> | Sim | String | A subcadeia para substituir |
| <*novoTexto*> | Sim | String | A corda de substituição |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*texto atualizado*> | String | A cadeia atualizada após a substituição da subcadeia <p>Se o substring não for encontrado, devolva a corda original. |
||||

*Exemplo*

Este exemplo encontra o substring "velho" na "corda velha" e substitui "velho" por "novo":

```
replace('the old string', 'old', 'new')
```

E devolve este resultado:`"the new string"`

<a name="skip"></a>

### <a name="skip"></a>saltar

Retire os itens da parte da frente de uma coleção e devolva *todos os outros* itens.

```
skip([<collection>], <count>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Matriz | A coleção cujos itens pretende remover |
| <*contar*> | Sim | Número inteiro | Um inteiro positivo para o número de itens a remover na frente |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<> *de recolha atualizada]* | Matriz | A recolha atualizada após a remoção dos itens especificados |
||||

*Exemplo*

Este exemplo remove um item, o número 0, da frente da matriz especificada:

```
skip(createArray(0, 1, 2, 3), 1)
```

E devolve esta matriz com os restantes itens:`[1,2,3]`

<a name="split"></a>

### <a name="split"></a>dividir

Volte a colocar uma matriz que contenha subcordas, separadas por vírgulas, com base no carácter delimitador especificado na cadeia original.

```
split('<text>', '<delimiter>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*texto*> | Sim | String | A corda para separar em subcordas com base no delimitador especificado na corda original |
| <*delimitador*> | Sim | String | O personagem na corda original para usar como delimitador |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<*substring1*>,<*substring2*>,...] | Matriz | Uma matriz que contém subcordas da corda original, separada por vírgulas |
||||

*Exemplo*

Este exemplo cria uma matriz com subcordas da corda especificada com base no carácter especificado como delimitador:

```
split('a_b_c', '_')
```

E devolve esta matriz como resultado:`["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>inícioOfDay

Volte o início do dia para um carimbo temporal.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | String | A corda que contém o carimbo de tempo |
| <*formato*> | Não | String | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atualizado*> | String | A marca de tempo especificada, mas começando na marca de zero horas para o dia |
||||

*Exemplo*

Este exemplo encontra o início do dia para este carimbo temporal:

```
startOfDay('2018-03-15T13:30:30Z')
```

E devolve este resultado:`"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>inícioOfHour

Volte o início da hora por uma marca de tempo.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | String | A corda que contém o carimbo de tempo |
| <*formato*> | Não | String | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atualizado*> | String | A marca de tempo especificada, mas começando na marca de zero minutos para a hora |
||||

*Exemplo*

Este exemplo encontra o início da hora para este carimbo temporal:

```
startOfHour('2018-03-15T13:30:30Z')
```

E devolve este resultado:`"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>inícioDo mês

Devolva o início do mês por um carimbo temporal.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | String | A corda que contém o carimbo de tempo |
| <*formato*> | Não | String | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atualizado*> | String | A marca de tempo especificada, mas a partir do primeiro dia do mês na marca de zero horas |
||||

*Exemplo*

Este exemplo devolve o início do mês para este carimbo temporal:

```
startOfMonth('2018-03-15T13:30:30Z')
```

E devolve este resultado:`"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Verifique se uma corda começa com um substring específico.
Volte a ser verdade quando o substring for encontrado, ou volte a ser falso quando não for encontrado.
Esta função não é sensível a casos.

```
startsWith('<text>', '<searchText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*texto*> | Sim | String | A corda para verificar |
| <*pesquisarTexto*> | Sim | String | A corda de partida para encontrar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false  | Booleano | Volte a ser verdade quando o substring inicial for encontrado. Devolver falso quando não for encontrado. |
||||

*Exemplo 1*

Este exemplo verifica se a corda "hello world" começa com o substring "olá":

```
startsWith('hello world', 'hello')
```

E devolve este resultado:`true`

*Exemplo 2*

Este exemplo verifica se a corda "hello world" começa com o substring "saudações":

```
startsWith('hello world', 'greetings')
```

E devolve este resultado:`false`

<a name="string"></a>

### <a name="string"></a>string

Devolva a versão de corda por um valor.

```
string(<value>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Qualquer | O valor para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor de cadeia*> | String | A versão de corda para o valor especificado |
||||

*Exemplo 1*

Este exemplo cria a versão de cadeia para este número:

```
string(10)
```

E devolve este resultado:`"10"`

*Exemplo 2*

Este exemplo cria uma corda para o objeto JSON\\especificado e utiliza o carácter backslash () como um personagem de fuga para a marca de dupla citação (").

```
string( { "name": "Sophie Owen" } )
```

E devolve este resultado:`"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

Devolva o resultado de subtrair o segundo número do primeiro número.

```
sub(<minuend>, <subtrahend>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | Sim | Inteiro ou Flutuador | O número a partir do qual subtrair o *subsolo* |
| <*subtrahend*> | Sim | Inteiro ou Flutuador | O número para subtrair do *minúendo* |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado*> | Inteiro ou Flutuador | O resultado de subtrair o segundo número do primeiro número |
||||

*Exemplo*

Este exemplo subtrai o segundo número do primeiro número:

```
sub(10.3, .3)
```

E devolve este resultado:`10`

<a name="substring"></a>

### <a name="substring"></a>substring

Volte a colocar caracteres de uma corda, a partir da posição especificada, ou do índice.
Os valores do índice começam com o número 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*texto*> | Sim | String | A corda cujos personagens quer |
| <*startIndex*> | Sim | Número inteiro | Um número positivo igual ou superior a 0 que pretende utilizar como posição inicial ou valor de índice |
| <*comprimento*> | Sim | Número inteiro | Um número positivo de caracteres que você quer no substring |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado substring*> | String | Uma subcadeia com o número especificado de caracteres, a partir da posição de índice especificada na cadeia de origem |
||||

*Exemplo*

Este exemplo cria uma subcadeia de cinco caracteres a partir da corda especificada, a partir do valor do índice 6:

```
substring('hello world', 6, 5)
```

E devolve este resultado:`"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtratoFromTime

Subtraia várias unidades temporais de uma marca temporal.
Ver também [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | String | A corda que contém o carimbo de tempo |
| <*intervalo*> | Sim | Número inteiro | O número de unidades de tempo especificadas para subtrair |
| <*timeUnit*> | Sim | String | A unidade de tempo para usar com *intervalo*: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*formato*> | Não | String | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atualizado*> | String | O carimbo de tempo menos o número especificado de unidades de tempo |
||||

*Exemplo 1*

Este exemplo subtrai um dia a partir desta marca de tempo:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

E devolve este resultado:`"2018-01-01T00:00:00:0000000Z"`

*Exemplo 2*

Este exemplo subtrai um dia a partir desta marca de tempo:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

E devolve este resultado utilizando o formato "D" opcional:`"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>tomar

Devolver artigos da frente de uma coleção.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Corda ou Matriz | A coleção cujos itens você quer |
| <*contar*> | Sim | Número inteiro | Um inteiro positivo para o número de itens que você quer da frente |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <> de *subset* ou [<*> de subconjunto]* | String ou Array, respectivamente | Uma cadeia ou matriz que tenha o número especificado de itens retirados da parte da frente da coleção original |
||||

*Exemplo*

Estes exemplos obtêm o número especificado de itens da parte da frente destas coleções:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

E devolver estes resultados:

* Primeiro exemplo:`"abc"`
* Segundo exemplo:`[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>tiques

Devolva o valor da `ticks` propriedade por um carimbo de tempo especificado.
Um *tique* é um intervalo de 100 nanossegundos.

```
ticks('<timestamp>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | String | A corda para um carimbo temporal |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carraças-número*> | Número inteiro | O número de carrapatos desde o carimbo de tempo especificado |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Volte a colocar uma corda em formato minúsculo. Se um personagem na corda não tem uma versão minúscula, esse personagem permanece inalterado na corda devolvida.

```
toLower('<text>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*texto*> | Sim | String | A corda para voltar em formato minúsculo |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*minúscula-texto*> | String | A cadeia original em formato minúsculo |
||||

*Exemplo*

Este exemplo converte esta cadeia em minúscula:

```
toLower('Hello World')
```

E devolve este resultado:`"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Volte a colocar uma corda em formato superior. Se um personagem na corda não tem uma versão maiúscula, esse personagem permanece inalterado na corda devolvida.

```
toUpper('<text>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*texto*> | Sim | String | A corda para voltar em formato maiúsculo |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*texto maiúsculo*> | String | A cadeia original em formato maiúsculo |
||||

*Exemplo*

Este exemplo converte esta cadeia em maiúsculas:

```
toUpper('Hello World')
```

E devolve este resultado:`"HELLO WORLD"`

<a name="trim"></a>

### <a name="trim"></a>aparar

Remova o espaço branco de liderar e arrastando a partir de uma corda e devolva a corda atualizada.

```
trim('<text>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*texto*> | Sim | String | A corda que tem o espaço branco líder e trailing para remover |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*atualizadoTexto*> | String | Uma versão atualizada para a cadeia original sem liderar ou seguir o espaço branco |
||||

*Exemplo*

Este exemplo remove o espaço branco líder e trailing da corda " Hello World ":

```
trim(' Hello World  ')
```

E devolve este resultado:`"Hello World"`

<a name="union"></a>

### <a name="union"></a>união

Devolva uma coleção que tenha *todos os* itens das coleções especificadas.
Para aparecer no resultado, um item pode aparecer em qualquer coleção passada para esta função. Se um ou mais itens tiverem o mesmo nome, o último item com esse nome aparece no resultado.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção1*>, *<coleção2*>, ...  | Sim | Matriz ou Objeto, mas não ambos | As coleções de onde você quer *todos os* itens |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*atualizadoCollection*> | Matriz ou Objeto, respectivamente | Uma coleção com todos os itens das coleções especificadas - sem duplicados |
||||

*Exemplo*

Este exemplo obtém *todos os* itens destas coleções:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

E devolve este resultado:`[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Volte a colocar uma versão codificada por um identificador de recursos uniforme (URI) para uma cadeia, substituindo caracteres inseguros de URL por caracteres de fuga.
Utilize esta função em vez [de codificar o Componente Uri()](#encodeUriComponent).
Embora ambas as funções `uriComponent()` funcionem da mesma forma, é preferível.

```
uriComponent('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A corda para converter em formato codificado por URI |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*codificado-uri*> | String | A corda codificada uri com caracteres de fuga |
||||

*Exemplo*

Este exemplo cria uma versão codificada por URI para esta cadeia:

```
uriComponent('https://contoso.com')
```

E devolve este resultado:`"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Devolva a versão binária para um componente de identificador de recursos uniforme (URI).

```
uriComponentToBinary('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia codificada uri para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*binário-para-codificado-uri*> | String | A versão binária para a cadeia codificada por URI. O conteúdo binário é codificado por base64 `$content`e representado por . |
||||

*Exemplo*

Este exemplo cria a versão binária para esta cadeia codificada por URI:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

E devolve este resultado:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Devolva a versão de cadeia para um fio codificado de um identificador de recursos uniforme (URI), descodificando eficazmente a cadeia codificada pelo URI.

```
uriComponentToString('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia codificada uri para descodificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*descodificado-uri*> | String | A versão descodificada para a cadeia codificada uri |
||||

*Exemplo*

Este exemplo cria a versão de string descodificada para esta cadeia codificada por URI:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

E devolve este resultado:`"https://contoso.com"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Devolva o carimbo de tempo atual.

```
utcNow('<format>')
```

Opcionalmente, pode especificar um formato diferente com o *formato* <> parâmetro.

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*formato*> | Não | String | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atual*> | String | A data e hora atuais |
||||

*Exemplo 1*

Suponha que hoje é 15 de abril de 2018 às 13:00.
Este exemplo obtém o carimbo de tempo atual:

```
utcNow()
```

E devolve este resultado:`"2018-04-15T13:00:00.0000000Z"`

*Exemplo 2*

Suponha que hoje é 15 de abril de 2018 às 13:00.
Este exemplo obtém a marca de tempo atual utilizando o formato "D" opcional:

```
utcNow('D')
```

E devolve este resultado:`"Sunday, April 15, 2018"`

<a name="xml"></a>

### <a name="xml"></a>xml

Devolva a versão XML para uma corda que contenha um objeto JSON.

```
xml('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A corda com o objeto JSON para converter <p>O objeto JSON deve ter apenas uma propriedade de raiz, que não pode ser uma matriz. <br>Use o carácter\\backslash () como um personagem de fuga para a marca de citação dupla ("). |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*xml-versão*> | Objeto | O XML codificado para a corda especificada ou objeto JSON |
||||

*Exemplo 1*

Este exemplo cria a versão XML para esta cadeia, que contém um objeto JSON:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

E devolve este resultado XML:

```xml
<name>Sophia Owen</name>
```

*Exemplo 2*

Suponha que tenha este objeto JSON:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

Este exemplo cria XML para uma cadeia que contém este objeto JSON:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

E devolve este resultado XML:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>xpath

Verifique o XML para ver se há nós ou valores que correspondam a uma expressão XPath (XML Path Language) e devolva os nós ou valores correspondentes. Uma expressão XPath, ou apenas "XPath", ajuda-o a navegar numa estrutura de documentoXML para que possa selecionar nós ou calcular valores no conteúdo XML.

```
xpath('<xml>', '<xpath>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*xml*> | Sim | Qualquer | A cadeia XML para procurar por nóouos ou valores que correspondam a um valor de expressão XPath |
| <*xpath*> | Sim | Qualquer | A expressão XPath usada para encontrar nódosos ou valores XML correspondentes |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*xml-nó*> | XML | Um nó XML quando apenas um nó corresponde à expressão XPath especificada |
| <*valor*> | Qualquer | O valor de um nó XML quando apenas um único valor corresponde à expressão XPath especificada |
| [<*xml-nóde1*>, <*xml-node2*>, ...] </br>-ou- </br>[<*valor 1*>, <*valor2*>, ...] | Matriz | Uma matriz com nódosos ou valores XML que correspondem à expressão XPath especificada |
||||

*Exemplo 1*

Seguindo o Exemplo 1, este exemplo encontra `<count></count>` nós que combinam com o `sum()` nó e adiciona esses valores de nó com a função:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

E devolve este resultado:`30`

*Exemplo 2*

Para este exemplo, ambas as expressões `<location></location>` encontram nós que combinam com o nó, nos argumentos especificados, que incluem XML com um espaço de nome. As expressões usam o\\carácter backslash ( ) como um personagem de fuga para a marca de citação dupla (").

* *Expressão 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Expressão 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

Aqui estão os argumentos:

* Este XML, que inclui o espaço `xmlns="http://contoso.com"`de nome do documento XML,

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Ou expressão XPath aqui:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

Aqui está o nó de `<location></location>` resultados que combina com o nó:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Exemplo 3*

Seguindo o Exemplo 3, este exemplo `<location></location>` encontra o valor no nó:

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

E devolve este resultado:`"Paris"`

## <a name="next-steps"></a>Passos seguintes
Para uma lista de variáveis do sistema que pode utilizar em expressões, consulte [variáveis do Sistema](control-flow-system-variables.md).
