---
title: Expressão e funções na Azure Data Factory
description: Este artigo fornece informações sobre expressões e funções que pode utilizar na criação de entidades de fábrica de dados.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: 3c966f0efc51a3b2fa8908e060b4031ae1ad1e50
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500024"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Expressões e funções no Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-functions-variables.md)
> * [Versão atual](control-flow-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo fornece detalhes sobre expressões e funções apoiadas pela Azure Data Factory. 

## <a name="expressions"></a>Expressões

Os valores de JSON na definição podem ser literais ou expressões que são avaliadas em tempo de execução. Por exemplo:  
  
```json
"name": "value"
```

 ou  
  
```json
"name": "@pipeline().parameters.password"
```

As expressões podem aparecer em qualquer lugar num valor de corda JSON e resultam sempre em outro valor JSON. Se um valor JSON for uma expressão, o corpo da expressão é extraído removendo o sinal de \@ at(). Se for necessária uma corda literal que comece \@ por, deve ser escapado utilizando \@ \@ . Os exemplos a seguir mostram como as expressões são avaliadas.  
  
|Valor JSON|Resultado|  
|----------------|------------|  
|"Parâmetros"|Os caracteres 'parâmetros' são devolvidos.|  
|"Parâmetros[1]"|Os caracteres 'parâmetros[1]' são devolvidos.|  
|"\@\@"|Uma cadeia de caracteres de 1 que contém \@ ' ' é devolvida.|  
|" \@"|Uma corda de 2 caracteres que contém \@ ' ' é devolvida.|  
  
 As expressões também podem aparecer dentro das cordas, utilizando uma funcionalidade chamada *interpolação de cordas* onde as expressões são embrulhadas em `@{ ... }` . Por exemplo: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Usando a interpolação de cordas, o resultado é sempre uma corda. Digamos que defini `myNumber` `42` como e  `myString`  `foo` como:  
  
|Valor JSON|Resultado|  
|----------------|------------|  
|" \@ pipeline().parâmetros.myString"| Retorna `foo` como uma corda.|  
|" \@ {pipeline().parâmetros.myString}"| Retorna `foo` como uma corda.|  
|" \@ pipeline().parâmetros.myNumber"| Devoluções `42` como *número*.|  
|" \@ {pipeline().parâmetros.myNumber}"| Retorna `42` como uma *corda.*|  
|"Resposta é: @{pipeline().parâmetros.myNumber}"| Devolve a `Answer is: 42` corda.|  
|" \@ concat('Resposta é: ', string(pipeline().parâmetros.myNumber)"| Devolve a corda `Answer is: 42`|  
|"Resposta é: \@ \@ {pipeline().parâmetros.myNumber}"| Devolve a `Answer is: @{pipeline().parameters.myNumber}` corda.|  
  
## <a name="examples"></a>Exemplos

### <a name="complex-expression-example"></a>Exemplo de expressão complexa
O exemplo abaixo mostra um exemplo complexo que faz referência a um sub-campo profundo de produção de atividade. Para fazer referência a um parâmetro de gasoduto que avalie para um sub-campo, utilize a sintaxe [] em vez do operador ponto(como no caso do subcampo1 e subcampo2)

@activity('*atividade Nome*').saída. *subfield1*. *subfield2*[pipeline().parâmetros.*subfield3*]. *subfield4*

### <a name="a-dataset-with-a-parameter"></a>Um conjunto de dados com um parâmetro
No exemplo seguinte, o BlobDataset toma um **caminho** de parâmetro. O seu valor é utilizado para definir um valor para a propriedade **pastaPaop** utilizando a expressão: `dataset().path` . 

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

### <a name="a-pipeline-with-a-parameter"></a>Um oleoduto com um parâmetro
No exemplo seguinte, o pipeline leva os parâmetros **inputPath** e **outputPath.** O **caminho** para o conjunto de dados de bolhas parametrizadas é definido utilizando valores destes parâmetros. A sintaxe usada aqui é: `pipeline().parameters.parametername` . 

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
Este [tutorial](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) acompanha-o como passar parâmetros entre um oleoduto e atividade, bem como entre as atividades.

  
## <a name="functions"></a>Funções

Pode chamar funções dentro de expressões. As seguintes secções fornecem informações sobre as funções que podem ser usadas numa expressão.  

## <a name="string-functions"></a>Funções de cadeia  

Para trabalhar com cordas, pode utilizar estas funções de corda e também [algumas funções de recolha.](#collection-functions)
As funções de corda funcionam apenas em cordas.

| Função de corda | Tarefa |
| --------------- | ---- |
| [concat](control-flow-expression-language-functions.md#concat) | Combine duas ou mais cordas e devolva a corda combinada. |
| [terminaWith](control-flow-expression-language-functions.md#endswith) | Verifique se uma corda termina com o sub-adcção especificado. |
| [guid](control-flow-expression-language-functions.md#guid) | Gere um identificador globalmente único (GUID) como uma corda. |
| [indexOf](control-flow-expression-language-functions.md#indexof) | Volte a colocar a posição inicial para um sub-adiscão. |
| [lastIndexOf](control-flow-expression-language-functions.md#lastindexof) | Volte a colocar a posição inicial para a última ocorrência de um sub-adcões. |
| [substituir](control-flow-expression-language-functions.md#replace) | Substitua um sub-adc de sublagem com a cadeia especificada e devolva a cadeia atualizada. |
| [divisão](control-flow-expression-language-functions.md#split) | Devolva uma matriz que contenha sublutos, separados por vírgulas, de uma corda maior baseada num personagem delimiter especificado na cadeia original. |
| [começacom](control-flow-expression-language-functions.md#startswith) | Verifique se uma corda começa com um sub-adcção específico. |
| [substring](control-flow-expression-language-functions.md#substring) | Devolva os caracteres de uma corda, a partir da posição especificada. |
| [toLower](control-flow-expression-language-functions.md#toLower) | Devolva uma corda em formato minúsculo. |
| [toupper](control-flow-expression-language-functions.md#toUpper) | Devolva uma corda em formato maiúscula. |
| [guarnição](control-flow-expression-language-functions.md#trim) | Remova o espaço branco de uma corda e devolva a corda atualizada. |

## <a name="collection-functions"></a>Funções de coleção

Para trabalhar com coleções, geralmente matrizes, cordas e, por vezes, dicionários, você pode usar estas funções de coleção.

| Função de recolha | Tarefa |
| ------------------- | ---- |
| [contém](control-flow-expression-language-functions.md#contains) | Verifique se uma coleção tem um item específico. |
| [vazio](control-flow-expression-language-functions.md#empty) | Verifique se uma coleção está vazia. |
| [primeiro](control-flow-expression-language-functions.md#first) | Devolva o primeiro item de uma coleção. |
| [intersecção](control-flow-expression-language-functions.md#intersection) | Devolva uma coleção que tenha *apenas* os itens comuns em todas as coleções especificadas. |
| [juntar-se](control-flow-expression-language-functions.md#join) | Devolva uma corda que tenha *todos os* itens de uma matriz, separada pelo carácter especificado. |
| [último](control-flow-expression-language-functions.md#last) | Devolva o último item de uma coleção. |
| [length](control-flow-expression-language-functions.md#length) | Devolva o número de itens numa cadeia ou matriz. |
| [saltar](control-flow-expression-language-functions.md#skip) | Retire os itens da parte da frente de uma coleção e devolva *todos os outros* itens. |
| [take](control-flow-expression-language-functions.md#take) | Devolva os artigos da parte da frente de uma coleção. |
| [união](control-flow-expression-language-functions.md#union) | Devolva uma coleção que tenha *todos os* itens das coleções especificadas. | 

## <a name="logical-functions"></a>Funções lógicas  

Estas funções são úteis dentro de condições, podem ser usadas para avaliar qualquer tipo de lógica.  
  
| Função de comparação lógica | Tarefa |
| --------------------------- | ---- |
| [and](control-flow-expression-language-functions.md#and) | Verifique se todas as expressões são verdadeiras. |
| [equals](control-flow-expression-language-functions.md#equals) | Verifique se ambos os valores são equivalentes. |
| [greater](control-flow-expression-language-functions.md#greater) | Verifique se o primeiro valor é maior do que o segundo valor. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | Verifique se o primeiro valor é maior ou igual ao segundo valor. |
| [se](control-flow-expression-language-functions.md#if) | Verifique se uma expressão é verdadeira ou falsa. Com base no resultado, devolva um valor especificado. |
| [menos](control-flow-expression-language-functions.md#less) | Verifique se o primeiro valor é inferior ao segundo valor. |
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
| [matriz](control-flow-expression-language-functions.md#array) | Devolva uma matriz a partir de uma única entrada especificada. Para várias entradas, consulte [createArray](control-flow-expression-language-functions.md#createArray). |
| [base64](control-flow-expression-language-functions.md#base64) | Retornar a versão codificada base64 para uma cadeia. |
| [base64ToBinary](control-flow-expression-language-functions.md#base64ToBinary) | Devolva a versão binária para uma cadeia codificada base64. |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | Devolva a versão de corda para uma corda codificada base64. |
| [binary](control-flow-expression-language-functions.md#binary) | Devolva a versão binária por um valor de entrada. |
| [bool](control-flow-expression-language-functions.md#bool) | Devolva a versão Boolean por um valor de entrada. |
| [coalesce](control-flow-expression-language-functions.md#coalesce) | Devolva o primeiro valor não nulo de um ou mais parâmetros. |
| [criarArray](control-flow-expression-language-functions.md#createArray) | Retornar uma matriz de várias entradas. |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | Devolva os dados URI por um valor de entrada. |
| [dataUriToBinary](control-flow-expression-language-functions.md#dataUriToBinary) | Devolva a versão binária para um URI de dados. |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | Devolva a versão de cadeia para um URI de dados. |
| [descodificarBase64](control-flow-expression-language-functions.md#decodeBase64) | Devolva a versão de corda para uma corda codificada base64. |
| [descodificarDataUri](control-flow-expression-language-functions.md#decodeDataUri) | Devolva a versão binária para um URI de dados. |
| [decodeUriComponent](control-flow-expression-language-functions.md#decodeUriComponent) | Retornar uma corda que substitua os caracteres de fuga por versões descodificadas. |
| [encodeUriComponent](control-flow-expression-language-functions.md#encodeUriComponent) | Retorna uma corda que substitui caracteres inseguros por URL por caracteres de fuga. |
| [flutuante](control-flow-expression-language-functions.md#float) | Devolva um número de ponto flutuante para obter um valor de entrada. |
| [int](control-flow-expression-language-functions.md#int) | Devolva a versão inteiro por uma corda. |
| [json](control-flow-expression-language-functions.md#json) | Retornar o valor ou objeto do tipo JavaScript (JSON) para uma cadeia ou XML. |
| [string](control-flow-expression-language-functions.md#string) | Devolva a versão de corda para obter um valor de entrada. |
| [uriComponente](control-flow-expression-language-functions.md#uriComponent) | Retornar a versão codificada uri por um valor de entrada substituindo caracteres inseguros por URL por caracteres de fuga. |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | Devolva a versão binária para uma corda codificada uri. |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | Devolva a versão de corda para uma corda codificada uri. |
| [xml](control-flow-expression-language-functions.md#xml) | Devolva a versão XML para uma corda. |
| [xpath](control-flow-expression-language-functions.md#xpath) | Verifique se xML se trata de nós ou valores que correspondam a uma expressão XPath (XML Path Language) e devolva os nós ou valores correspondentes. |

## <a name="math-functions"></a>Funções matemáticas  
 Estas funções podem ser utilizadas para qualquer tipo de números: **inteiros** e **boias**.  

| Função matemática | Tarefa |
| ------------- | ---- |
| [adicionar](control-flow-expression-language-functions.md#add) | Devolva o resultado da adição de dois números. |
| [div](control-flow-expression-language-functions.md#div) | Devolva o resultado da divisão de dois números. |
| [máx](control-flow-expression-language-functions.md#max) | Devolva o valor mais alto de um conjunto de números ou de uma matriz. |
| [min](control-flow-expression-language-functions.md#min) | Devolva o valor mais baixo de um conjunto de números ou de uma matriz. |
| [mod](control-flow-expression-language-functions.md#mod) | Devolva o resto da divisão de dois números. |
| [mul](control-flow-expression-language-functions.md#mul) | Retire o produto da multiplicação de dois números. |
| [rand](control-flow-expression-language-functions.md#rand) | Devolva um inteiro aleatório de um alcance especificado. |
| [gama](control-flow-expression-language-functions.md#range) | Devolva uma matriz de inteiros que começa a partir de um inteiro especificado. |
| [sub](control-flow-expression-language-functions.md#sub) | Devolva o resultado da subtração do segundo número do primeiro número. |
  
## <a name="date-functions"></a>Funções de data  

| Função de data ou hora | Tarefa |
| --------------------- | ---- |
| [addDays](control-flow-expression-language-functions.md#addDays) | Adicione alguns dias a uma hora de tempo. |
| [addHours](control-flow-expression-language-functions.md#addHours) | Adicione algumas horas a um tempotando. |
| [addMinutes](control-flow-expression-language-functions.md#addMinutes) | Adicione alguns minutos a uma hora de tempo. |
| [addSeconds](control-flow-expression-language-functions.md#addSeconds) | Adicione alguns segundos a um tempotando. |
| [addToTime](control-flow-expression-language-functions.md#addToTime) | Adicione um número de unidades de tempo a um relógio de tempo. Ver também [getFutureTime](control-flow-expression-language-functions.md#getFutureTime). |
| [converterFromUtc](control-flow-expression-language-functions.md#convertFromUtc) | Converta um relógio de tempo da Universal Time Coordinated (UTC) para o fuso horário-alvo. |
| [converterTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | Converta um relógio do fuso horário de origem para o fuso horário alvo. |
| [converterToUtc](control-flow-expression-language-functions.md#convertToUtc) | Converter um tempotando do fuso horário de origem para o tempo universal coordenado (UTC). |
| [dayOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | Devolva o componente do dia do mês a partir de uma hora de tempo. |
| [dayOfWeek](control-flow-expression-language-functions.md#dayOfWeek) | Devolva o componente do dia da semana a partir de uma estamp de tempo. |
| [diaOfYear](control-flow-expression-language-functions.md#dayOfYear) | Devolva o componente do dia do ano a partir de um timetamp. |
| [formatoDa hora](control-flow-expression-language-functions.md#formatDateTime) | Devolva a estada de tempo como uma corda em formato opcional. |
| [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) | Retornar a temperatura atual mais as unidades de tempo especificadas. Consulte também [o AddToTime](control-flow-expression-language-functions.md#addToTime). |
| [getPastTime](control-flow-expression-language-functions.md#getPastTime) | Retornar o tempo de tempo atual menos as unidades de tempo especificadas. Consulte também [subtrairFromTime](control-flow-expression-language-functions.md#subtractFromTime). |
| [startOfDay](control-flow-expression-language-functions.md#startOfDay) | Volte o início do dia para uma hora de tempo. |
| [startOfHour](control-flow-expression-language-functions.md#startOfHour) | Volte a ligar o início da hora para uma hora marcada. |
| [startOfMonth](control-flow-expression-language-functions.md#startOfMonth) | Devolva o início do mês para uma hora de tempo. |
| [subtraindoFromTime](control-flow-expression-language-functions.md#subtractFromTime) | Subtraia uma série de unidades de tempo de um relógio. Consulte também [getPastTime](control-flow-expression-language-functions.md#getPastTime). |
| [carrapatos](control-flow-expression-language-functions.md#ticks) | Devolva o `ticks` valor da propriedade para um tempotando especificado. |
| [utcNow](control-flow-expression-language-functions.md#utcNow) | Devolva a atual estação de tempo como uma corda. |

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
| <*summand_1*>, *summand_2 <*> | Sim | Inteiro, Flutuante ou Misturado | Os números a adicionar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | -----| ----------- |
| <*resultado-soma*> | Inteiro ou Flutuante | O resultado da adição dos números especificados |
||||

*Exemplo*

Este exemplo adiciona os números especificados:

```
add(1, 1.5)
```

E devolve este resultado: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Adicione alguns dias a uma hora de tempo.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*Dias*> | Sim | Número inteiro | O número positivo ou negativo de dias a adicionar |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*hora atualizada*> | String | A hora da hora mais o número especificado de dias  |
||||

*Exemplo 1*

Este exemplo adiciona 10 dias à hora de tempo especificada:

```
addDays('2018-03-15T13:00:00Z', 10)
```

E devolve este resultado: `"2018-03-25T00:00:0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco dias da hora especificada:

```
addDays('2018-03-15T00:00:00Z', -5)
```

E devolve este resultado: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Adicione algumas horas a um tempotando.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*horas*> | Sim | Número inteiro | O número positivo ou negativo de horas a adicionar |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*hora atualizada*> | String | A hora da hora mais o número especificado de horas  |
||||

*Exemplo 1*

Este exemplo adiciona 10 horas à hora de tempo especificada:

```
addHours('2018-03-15T00:00:00Z', 10)
```

E devolve este resultado: `"2018-03-15T10:00:0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco horas da hora especificada:

```
addHours('2018-03-15T15:00:00Z', -5)
```

E devolve este resultado: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Adicione alguns minutos a uma hora de tempo.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*minutos*> | Sim | Número inteiro | O número positivo ou negativo de minutos a adicionar |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*hora atualizada*> | String | A hora da hora mais o número especificado de minutos |
||||

*Exemplo 1*

Este exemplo adiciona 10 minutos à hora de tempo especificada:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

E devolve este resultado: `"2018-03-15T00:20:00.0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco minutos da hora especificada:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

E devolve este resultado: `"2018-03-15T00:15:00.0000000Z"`

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Adicione alguns segundos a um tempotando.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*segundos*> | Sim | Número inteiro | O número positivo ou negativo de segundos a adicionar |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*hora atualizada*> | String | A hora da hora mais o número especificado de segundos  |
||||

*Exemplo 1*

Este exemplo adiciona 10 segundos à temperatura prevista:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

E devolve este resultado: `"2018-03-15T00:00:10.0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco segundos para a hora especificada:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

E devolve este resultado: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Adicione um número de unidades de tempo a um relógio de tempo.
Consulte também [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*intervalo*> | Sim | Número inteiro | O número de unidades de tempo especificadas para adicionar |
| <*timeUnit*> | Sim | String | A unidade de tempo a utilizar com *intervalo:*"Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*hora atualizada*> | String | A hora da hora mais o número especificado de unidades de tempo  |
||||

*Exemplo 1*

Este exemplo adiciona um dia à hora de tempo especificada:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

E devolve este resultado: `"2018-01-02T00:00:00.0000000Z"`

*Exemplo 2*

Este exemplo adiciona um dia à hora de tempo especificada:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

E devolve o resultado usando o formato "D" opcional: `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>e

Verifique se ambas as expressões são verdadeiras.
Devolva-se quando ambas as expressões são verdadeiras, ou retorna falsas quando pelo menos uma expressão é falsa.

```
and(<expression1>, <expression2>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expressão1*>, *expressão <2*> | Sim | Booleano | As expressões para verificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | -----| ----------- |
| true ou false | Booleano | Retornar verdadeiro quando ambas as expressões são verdadeiras. Devolva-se falso quando pelo menos uma expressão é falsa. |
||||

*Exemplo 1*

Estes exemplos verificam se os valores booleanos especificados são ambos verdadeiros:

```
and(true, true)
and(false, true)
and(false, false)
```

E devolve estes resultados:

* Primeiro exemplo: Ambas as expressões são verdadeiras, por isso `true` retorna.
* Segundo exemplo: Uma expressão é falsa, por isso `false` retorna.
* Terceiro exemplo: Ambas as expressões são falsas, por isso devoluções `false` .

*Exemplo 2*

Estes exemplos verificam se as expressões especificadas são ambas verdadeiras:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

E devolve estes resultados:

* Primeiro exemplo: Ambas as expressões são verdadeiras, por isso `true` retorna.
* Segundo exemplo: Uma expressão é falsa, por isso `false` retorna.
* Terceiro exemplo: Ambas as expressões são falsas, por isso devoluções `false` .

<a name="array"></a>

### <a name="array"></a>matriz

Devolva uma matriz a partir de uma única entrada especificada.
Para várias entradas, consulte [createArray()](#createArray).

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

Este exemplo cria uma matriz a partir da cadeia "olá":

```
array('hello')
```

E devolve este resultado: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Retornar a versão codificada base64 para uma cadeia.

```
base64('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia de entrada |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*base64-string*> | String | A versão codificada de base64 para a cadeia de entrada |
||||

*Exemplo*

Este exemplo converte a cadeia "olá" para uma cadeia codificada base64:

```
base64('hello')
```

E devolve este resultado: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Devolva a versão binária para uma cadeia codificada base64.

```
base64ToBinary('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia codificada base64 para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*binário-para-base64-corda*> | String | A versão binária para a cadeia codificada base64 |
||||

*Exemplo*

Este exemplo converte a cadeia codificada "aGVsbG8=" para uma cadeia binária:

```
base64ToBinary('aGVsbG8=')
```

E devolve este resultado:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Devolva a versão de corda para uma corda codificada base64, descodificando eficazmente a cadeia base64.
Utilize esta função em vez [de descodificar Base64()](#decodeBase64).
Embora ambas as funções funcionem da mesma forma, `base64ToString()` é preferível.

```
base64ToString('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia codificada de base64 para descodificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*descodificada base64-cadeia*> | String | A versão de corda para uma cadeia codificada base64 |
||||

*Exemplo*

Este exemplo converte a cadeia codificada "aGVsbG8=" para apenas uma corda:

```
base64ToString('aGVsbG8=')
```

E devolve este resultado: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

Devolva a versão binária para uma corda.

```
binary('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A corda para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor binário para a entrada*> | String | A versão binária para a cadeia especificada |
||||

*Exemplo*

Este exemplo converte a cadeia "olá" a uma corda binária:

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

Estes exemplos convertem os valores especificados para valores booleanos:

```
bool(1)
bool(0)
```

E devolve estes resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="coalesce"></a>

### <a name="coalesce"></a>coalesce

Devolva o primeiro valor não nulo de um ou mais parâmetros.
Cordas vazias, matrizes vazias e objetos vazios não são nulos.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2 object_2*>... | Sim | Qualquer, pode misturar tipos | Um ou mais itens para verificar se há nulo |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*primeiro não-náu ponto*> | Qualquer | O primeiro item ou valor que não é nulo. Se todos os parâmetros forem nulos, esta função retorna nulo. |
||||

*Exemplo*

Estes exemplos devolvem o primeiro valor não nulo dos valores especificados, ou nulos quando todos os valores são nulos:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

E devolve estes resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `"hello"`
* Terceiro exemplo: `null`

<a name="concat"></a>

### <a name="concat"></a>concat

Combine duas ou mais cordas e devolva a corda combinada.

```
concat('<text1>', '<text2>', ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*texto2*>, ... | Sim | String | Pelo menos duas cordas para combinar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*text1text2...*> | String | A corda criada a partir das cordas de entrada combinadas |
||||

*Exemplo*

Este exemplo combina as cordas "Olá" e "Mundo":

```
concat('Hello', 'World')
```

E devolve este resultado: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>contains

Verifique se uma coleção tem um item específico.
Devolva-se quando o item for encontrado, ou devolva falso quando não for encontrado.
Esta função é sensível a casos.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Especificamente, esta função funciona nestes tipos de coleção:

* Uma *corda* para encontrar um *sub-cordão*
* Uma *matriz* para encontrar um *valor*
* Um *dicionário* para encontrar uma *chave*

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | String, Array ou Dicionário | A coleção para verificar |
| <*valor*> | Sim | String, Array ou Dicionário, respectivamente | O item para encontrar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Devolva-se quando o item for encontrado. Devolva-se falso quando não for encontrado. |
||||

*Exemplo 1*

Este exemplo verifica a cadeia "olá mundo" para o substring "mundo" e retorna verdadeiro:

```
contains('hello world', 'world')
```

*Exemplo 2*

Este exemplo verifica a cadeia "olá mundo" para o substring "universo" e devolve falso:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>converterFromUtc

Converta um relógio de tempo da Universal Time Coordinated (UTC) para o fuso horário-alvo.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*destinationTimeZone*> | Sim | String | O nome do fuso horário do alvo. Para obter nomes de fuso horário, consulte [os Valores do Índice do Fuso Horário](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)da Microsoft, mas poderá ter de remover qualquer pontuação do nome do fuso horário. |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*tempotamp convertido*> | String | A hora convertida para o fuso horário alvo |
||||

*Exemplo 1*

Este exemplo converte um relógio de tempo para o fuso horário especificado:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

E devolve este resultado: `"2018-01-01T00:00:00Z"`

*Exemplo 2*

Este exemplo converte um relógio de tempo para o fuso horário especificado e formato:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

E devolve este resultado: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>converterTimeZone

Converta um relógio do fuso horário de origem para o fuso horário alvo.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*sourceTimeZone*> | Sim | String | O nome do fuso horário da fonte. Para obter nomes de fuso horário, consulte [os Valores do Índice do Fuso Horário](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)da Microsoft, mas poderá ter de remover qualquer pontuação do nome do fuso horário. |
| <*destinationTimeZone*> | Sim | String | O nome do fuso horário do alvo. Para obter nomes de fuso horário, consulte [os Valores do Índice do Fuso Horário](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)da Microsoft, mas poderá ter de remover qualquer pontuação do nome do fuso horário. |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*tempotamp convertido*> | String | A hora convertida para o fuso horário alvo |
||||

*Exemplo 1*

Este exemplo converte o fuso horário de origem para o fuso horário-alvo:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

E devolve este resultado: `"2018-01-01T00:00:00.0000000"`

*Exemplo 2*

Este exemplo converte um fuso horário para o fuso horário especificado e formato:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

E devolve este resultado: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>converterToUtc

Converter um tempotando do fuso horário de origem para o tempo universal coordenado (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*sourceTimeZone*> | Sim | String | O nome do fuso horário da fonte. Para obter nomes de fuso horário, consulte [os Valores do Índice do Fuso Horário](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)da Microsoft, mas poderá ter de remover qualquer pontuação do nome do fuso horário. |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*tempotamp convertido*> | String | A hora convertida para UTC |
||||

*Exemplo 1*

Este exemplo converte um tempotando para UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

E devolve este resultado: `"2018-01-01T08:00:00.0000000Z"`

*Exemplo 2*

Este exemplo converte um tempotando para UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

E devolve este resultado: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>criarArray

Retornar uma matriz de várias entradas.
Para matrizes de entrada únicas, consulte [matriz()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*objeto1*>, <*objeto2*>, ... | Sim | Qualquer, mas não misturado | Pelo menos dois itens para criar a matriz |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<*objeto1*>, <> de *objeto2,* ...] | Matriz | A matriz criada a partir de todos os itens de entrada |
||||

*Exemplo*

Este exemplo cria uma matriz a partir destas entradas:

```
createArray('h', 'e', 'l', 'l', 'o')
```

E devolve este resultado: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Devolva um identificador de recursos uniformes de dados (URI) para uma cadeia.

```
dataUri('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A corda para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*data-uri*> | String | O dado URI para a cadeia de entrada |
||||

*Exemplo*

Este exemplo cria um dado URI para a cadeia "olá":

```
dataUri('hello')
```

E devolve este resultado: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Devolva a versão binária para um identificador de recursos uniforme de dados (URI).
Utilize esta função em vez [de descodificarDataUri()](#decodeDataUri).
Embora ambas as funções funcionem da mesma forma, `dataUriBinary()` é preferível.

```
dataUriToBinary('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | Os dados URI para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*binário-para-dados-uri*> | String | A versão binária para o dado URI |
||||

*Exemplo*

Este exemplo cria uma versão binária para este dado URI:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E devolve este resultado:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

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
| <*string-for-data-uri*> | String | A versão de cadeia para o dado URI |
||||

*Exemplo*

Este exemplo cria uma cadeia para este dado URI:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E devolve este resultado: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Devolva o dia do mês de um tempotando.

```
dayOfMonth('<timestamp>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*dia-do-dia*> | Número inteiro | O dia do mês a partir da hora de tempo especificada |
||||

*Exemplo*

Este exemplo devolve o número para o dia do mês a partir desta hora:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

E devolve este resultado: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Volte o dia da semana de uma hora.

```
dayOfWeek('<timestamp>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*dia-de-semana*> | Número inteiro | O dia da semana a partir do horário especificado, onde domingo é 0, segunda-feira é 1, e assim por diante |
||||

*Exemplo*

Este exemplo devolve o número para o dia da semana a partir desta hora:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

E devolve este resultado: `3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>diaOfYear

Volte o dia do ano de um timetamp.

```
dayOfYear('<timestamp>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*dia-do-ano*> | Número inteiro | O dia do ano a partir da hora de tempo especificada |
||||

*Exemplo*

Este exemplo devolve o número do dia do ano a partir desta hora:

```
dayOfYear('2018-03-15T13:27:36Z')
```

E devolve este resultado: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>descodificarBase64

Devolva a versão de corda para uma corda codificada base64, descodificando eficazmente a cadeia base64.
Considere utilizar [a base64ToString()](#base64ToString) em vez de `decodeBase64()` .
Embora ambas as funções funcionem da mesma forma, `base64ToString()` é preferível.

```
decodeBase64('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia codificada de base64 para descodificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*descodificada base64-cadeia*> | String | A versão de corda para uma cadeia codificada base64 |
||||

*Exemplo*

Este exemplo cria uma cadeia para uma cadeia codificada base64:

```
decodeBase64('aGVsbG8=')
```

E devolve este resultado: `"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>descodificarDataUri

Devolva a versão binária para um identificador de recursos uniforme de dados (URI).
Considere a utilização de [dadosUriToBinary()](#dataUriToBinary)e não `decodeDataUri()` .
Embora ambas as funções funcionem da mesma forma, `dataUriToBinary()` é preferível.

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

Este exemplo devolve a versão binária para este dado URI:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E devolve este resultado:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Retornar uma corda que substitua os caracteres de fuga por versões descodificadas.

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

E devolve este resultado: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Devolva o resultado inteiro da divisão de dois números.
Para obter o resultado remanescente, consulte [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*dividendo*> | Sim | Inteiro ou Flutuante | O número a dividir pelo *divisor* |
| <*divisor*> | Sim | Inteiro ou Flutuante | O número que divide o *dividendo,* mas não pode ser 0 |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado do quociente*> | Número inteiro | O número inteiro resulta da divisão do primeiro número pelo segundo número |
||||

*Exemplo*

Ambos os exemplos dividem o primeiro número pelo segundo número:

```
div(10, 5)
div(11, 5)
```

E devolva este resultado: `2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Retornar uma versão codificada por um identificador de recursos uniforme (URI) para uma cadeia, substituindo caracteres inseguros por URL por caracteres de fuga.
Considere usar [uriComponent()](#uriComponent), em vez de `encodeUriComponent()` .
Embora ambas as funções funcionem da mesma forma, `uriComponent()` é preferível.

```
encodeUriComponent('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A corda para converter para formato codificado URI |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*codificado-uri*> | String | A cadeia codificada uri com caracteres de fuga |
||||

*Exemplo*

Este exemplo cria uma versão codificada uri para esta cadeia:

```
encodeUriComponent('https://contoso.com')
```

E devolve este resultado: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>vazio

Verifique se uma coleção está vazia.
Devolva-se quando a coleção estiver vazia, ou devolva falsa quando não estiver vazia.

```
empty('<collection>')
empty([<collection>])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | String, Array ou Objeto | A coleção para verificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Volte a ser verdadeiro quando a coleção estiver vazia. Devolva-se falso quando não estiver vazio. |
||||

*Exemplo*

Estes exemplos verificam se as coleções especificadas estão vazias:

```
empty('')
empty('abc')
```

E devolve estes resultados:

* Primeiro exemplo: Passa uma corda vazia, para que a função retorne `true` .
* Segundo exemplo: Passa a cadeia "abc", para que a função retorne `false` .

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Verifique se uma corda termina com um sublamamento específico.
Devolva-se quando o sub-adcção for encontrado, ou devolva falso quando não for encontrado.
Esta função não é sensível a casos.

```
endsWith('<text>', '<searchText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Texto*> | Sim | String | A corda para verificar |
| <*searchText*> | Sim | String | O final substring para encontrar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false  | Booleano | Volte a ser verdadeiro quando o sub-final for encontrado. Devolva-se falso quando não for encontrado. |
||||

*Exemplo 1*

Este exemplo verifica se a corda "Hello world" termina com a cadeia "mundo":

```
endsWith('hello world', 'world')
```

E devolve este resultado: `true`

*Exemplo 2*

Este exemplo verifica se a corda "Hello world" termina com a cadeia "universo":

```
endsWith('hello world', 'universe')
```

E devolve este resultado: `false`

<a name="equals"></a>

### <a name="equals"></a>equals

Verifique se ambos os valores, expressões ou objetos são equivalentes.
Devolva-se quando ambos são equivalentes, ou devolva falso quando não são equivalentes.

```
equals('<object1>', '<object2>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*objeto1*>, <*objeto2*> | Sim | Vários | Os valores, expressões ou objetos para comparar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retorno verdadeiro quando ambos são equivalentes. Devolva falso quando não é equivalente. |
||||

*Exemplo*

Estes exemplos verificam se as entradas especificadas são equivalentes.

```
equals(true, 1)
equals('abc', 'abcd')
```

E devolve estes resultados:

* Primeiro exemplo: Ambos os valores são equivalentes, pelo que a função retorna `true` .
* Segundo exemplo: Ambos os valores não são equivalentes, pelo que a função retorna `false` .

<a name="first"></a>

### <a name="first"></a>primeiro

Devolva o primeiro item de uma corda ou matriz.

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
| <*primeiro artigo de recolha*> | Qualquer | O primeiro item da coleção |
||||

*Exemplo*

Estes exemplos encontram o primeiro item nestas coleções:

```
first('hello')
first(createArray(0, 1, 2))
```

E devolva estes resultados:

* Primeiro exemplo: `"h"`
* Segundo exemplo: `0`

<a name="float"></a>

### <a name="float"></a>float

Converta uma versão de corda para um número de ponto flutuante para um número de ponto flutuante real.

```
float('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia que tem um número de ponto flutuante válido para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor flutuante*> | Float | O número do ponto flutuante para a cadeia especificada |
||||

*Exemplo*

Este exemplo cria uma versão de corda para este número de ponto flutuante:

```
float('10.333')
```

E devolve este resultado: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatoDa hora

Devolva uma estada de tempo no formato especificado.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*reformatizado-timestamp*> | String | A datatampada atualizada no formato especificado |
||||

*Exemplo*

Este exemplo converte um tempotamp ao formato especificado:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

E devolve este resultado: `"2018-03-15T12:00:00"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Retornar a temperatura atual mais as unidades de tempo especificadas.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*intervalo*> | Sim | Número inteiro | O número de unidades de tempo especificadas para adicionar |
| <*timeUnit*> | Sim | String | A unidade de tempo a utilizar com *intervalo:*"Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*hora atualizada*> | String | A datatamp atual mais o número especificado de unidades de tempo |
||||

*Exemplo 1*

Suponha que a atual hora é "2018-03-01T00:00:00.0000000Z".
Este exemplo adiciona cinco dias a essa hora de fixação:

```
getFutureTime(5, 'Day')
```

E devolve este resultado: `"2018-03-06T00:00:00.0000000Z"`

*Exemplo 2*

Suponha que a atual hora é "2018-03-01T00:00:00.0000000Z".
Este exemplo adiciona cinco dias e converte o resultado em formato "D":

```
getFutureTime(5, 'Day', 'D')
```

E devolve este resultado: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Retornar o tempo de tempo atual menos as unidades de tempo especificadas.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*intervalo*> | Sim | Número inteiro | O número de unidades de tempo especificadas para subtrair |
| <*timeUnit*> | Sim | String | A unidade de tempo a utilizar com *intervalo:*"Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*hora atualizada*> | String | O tempotando atual menos o número especificado de unidades de tempo |
||||

*Exemplo 1*

Suponha que a atual hora é "2018-02-01T00:00:00.0000000Z".
Este exemplo subtrai cinco dias a partir dessa hora:

```
getPastTime(5, 'Day')
```

E devolve este resultado: `"2018-01-27T00:00:00.0000000Z"`

*Exemplo 2*

Suponha que a atual hora é "2018-02-01T00:00:00.0000000Z".
Este exemplo subtrai cinco dias e converte o resultado em formato "D":

```
getPastTime(5, 'Day', 'D')
```

E devolve este resultado: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Verifique se o primeiro valor é maior do que o segundo valor.
Devolva-se quando o primeiro valor for mais, ou devolva falso quando menos.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, Flutuante ou String | O primeiro valor para verificar se é maior do que o segundo valor |
| <*compararTo*> | Sim | Inteiro, Float ou String, respectivamente | O valor de comparação |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retorno verdadeiro quando o primeiro valor é maior do que o segundo valor. Devolução falsa quando o primeiro valor é igual ou inferior ao segundo valor. |
||||

*Exemplo*

Estes exemplos verificam se o primeiro valor é maior do que o segundo valor:

```
greater(10, 5)
greater('apple', 'banana')
```

E devolva estes resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Verifique se o primeiro valor é maior ou igual ao segundo valor.
Devolva-se quando o primeiro valor for maior ou igual, ou devolva falso quando o primeiro valor é menor.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, Flutuante ou String | O primeiro valor para verificar se é maior ou igual ao segundo valor |
| <*compararTo*> | Sim | Inteiro, Float ou String, respectivamente | O valor de comparação |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retorno verdadeiro quando o primeiro valor é maior ou igual ao segundo valor. Devolução falsa quando o primeiro valor é inferior ao segundo valor. |
||||

*Exemplo*

Estes exemplos verificam se o primeiro valor é maior ou igual ao segundo valor:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

E devolva estes resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="guid"></a>

### <a name="guid"></a>guid

Gerar um identificador globalmente único (GUID) como uma corda, por exemplo, "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Além disso, pode especificar um formato diferente para o GUID que não seja o formato padrão, "D", que é de 32 dígitos separados por hífenes.

```
guid('<format>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*formato*> | Não | String | Um único [especificador de formato](/dotnet/api/system.guid.tostring) para o GUID devolvido. Por predefinição, o formato é "D", mas pode usar "N", "D", "B", "P" ou "X". |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*Valor GUID*> | String | Um GUID gerado aleatoriamente |
||||

*Exemplo*

Este exemplo gera o mesmo GUID, mas como 32 dígitos, separados por hífenes, e fechados em parênteses:

```
guid('P')
```

E devolve este resultado: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

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
| <*valorIfTrue*> | Sim | Qualquer | O valor para voltar quando a expressão é verdadeira |
| <*valorIfFalse*> | Sim | Qualquer | O valor a devolver quando a expressão é falsa |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor de devolução especificado*> | Qualquer | O valor especificado que retorna com base em se a expressão é verdadeira ou falsa |
||||

*Exemplo*

Este exemplo retorna `"yes"` porque a expressão especificada retorna verdadeiramente.
Caso contrário, o exemplo `"no"` regressa:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Devolva a posição inicial ou o valor do índice para um sub-adcção.
Esta função não é sensível a casos, e os índices começam com o número 0.

```
indexOf('<text>', '<searchText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Texto*> | Sim | String | A corda que tem o sub-adc de ser encontrado |
| <*searchText*> | Sim | String | O sub-cordão para encontrar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*índice-valor*>| Número inteiro | A posição inicial ou o valor do índice para o sublpeso especificado. <p>Se a corda não for encontrada, devolva o número -1. |
||||

*Exemplo*

Este exemplo encontra o valor inicial do índice para o substring "mundo" na cadeia "hello world":

```
indexOf('hello world', 'world')
```

E devolve este resultado: `6`

<a name="int"></a>

### <a name="int"></a>int

Devolva a versão inteiro por uma corda.

```
int('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A corda para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado inteiro*> | Número inteiro | A versão inteiro para a cadeia especificada |
||||

*Exemplo*

Este exemplo cria uma versão inteiro para a cadeia "10":

```
int('10')
```

E devolve este resultado: `10`

<a name="json"></a>

### <a name="json"></a>json

Retornar o valor ou objeto do tipo JavaScript (JSON) para uma cadeia ou XML.

```
json('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String ou XML | A corda ou XML para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*Resultado do JSON*> | Tipo ou objeto nativo JSON | O valor ou objeto de tipo nativo JSON para a cadeia especificada ou XML. Se a corda for nula, a função devolve um objeto vazio. |
||||

*Exemplo 1*

Este exemplo converte esta cadeia ao valor JSON:

```
json('[1, 2, 3]')
```

E devolve este resultado: `[1, 2, 3]`

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

Este exemplo converte este XML para JSON:

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

### <a name="intersection"></a>intersecção

Devolva uma coleção que tenha *apenas* os itens comuns em todas as coleções especificadas.
Para aparecer no resultado, deve aparecer um item em todas as coleções passadas a esta função.
Se um ou mais itens tiverem o mesmo nome, o último item com esse nome aparece no resultado.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção1*>, <*coleção2*>, ... | Sim | Matriz ou Objeto, mas não ambos | As coleções de onde você quer *apenas* os itens comuns |
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

E devolve uma matriz *apenas* com estes itens: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>aderir

Devolva uma corda que tenha todos os itens de uma matriz e tenha cada personagem separado por um *delimiter*.

```
join([<collection>], '<delimiter>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Matriz | A matriz que tem os itens para se juntar |
| <*delimiter*> | Sim | String | O separador que aparece entre cada personagem na cadeia resultante |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*char1* >< *delimiter* >< *char2* ><> *delimitador...* | String | A cadeia resultante criada a partir de todos os itens na matriz especificada |
||||

*Exemplo*

Este exemplo cria uma cadeia a partir de todos os itens desta matriz com o carácter especificado como olimiter:

```
join(createArray('a', 'b', 'c'), '.')
```

E devolve este resultado: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>último

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
| <*último artigo de recolha*> | String ou Array, respectivamente | O último item da coleção |
||||

*Exemplo*

Estes exemplos encontram o último item nestas coleções:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

E devolve estes resultados:

* Primeiro exemplo: `"d"`
* Segundo exemplo: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Retorna a posição inicial ou o valor do índice para a última ocorrência de um sub-adcção.
Esta função não é sensível a casos, e os índices começam com o número 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Texto*> | Sim | String | A corda que tem o sub-adc de ser encontrado |
| <*searchText*> | Sim | String | O sub-cordão para encontrar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor de índice final*> | Número inteiro | A posição inicial ou o valor do índice para a última ocorrência do sublpesto especificado. <p>Se a corda não for encontrada, devolva o número -1. |
||||

*Exemplo*

Este exemplo encontra o valor inicial do índice para a última ocorrência do sublamtivo "mundo" na cadeia "Hello world":

```
lastIndexOf('hello world', 'world')
```

E devolve este resultado: `6`

<a name="length"></a>

### <a name="length"></a>length

Devolva o número de artigos numa coleção.

```
length('<collection>')
length([<collection>])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Corda ou Matriz | A coleção com os itens a contar |
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

E devolva este resultado: `4`

<a name="less"></a>

### <a name="less"></a>less

Verifique se o primeiro valor é inferior ao segundo valor.
Devolva-se quando o primeiro valor é menor, ou devolva falso quando o primeiro valor é mais.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, Flutuante ou String | O primeiro valor para verificar se menos do que o segundo valor |
| <*compararTo*> | Sim | Inteiro, Float ou String, respectivamente | O item de comparação |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retorno verdadeiro quando o primeiro valor é inferior ao segundo valor. Devolução falsa quando o primeiro valor é igual ou superior ao segundo valor. |
||||

*Exemplo*

Estes exemplos verificam se o primeiro valor é inferior ao segundo valor.

```
less(5, 10)
less('banana', 'apple')
```

E devolva estes resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Verifique se o primeiro valor é inferior ou igual ao segundo valor.
Devolva-se quando o primeiro valor for inferior ou igual, ou devolva falso quando o primeiro valor for mais.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, Flutuante ou String | O primeiro valor para verificar se menos ou igual ao segundo valor |
| <*compararTo*> | Sim | Inteiro, Float ou String, respectivamente | O item de comparação |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false  | Booleano | Retorno verdadeiro quando o primeiro valor é inferior ou igual ao segundo valor. Devolução falsa quando o primeiro valor é maior do que o segundo valor. |
||||

*Exemplo*

Estes exemplos verificam se o primeiro valor é menor ou igual ao segundo valor.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

E devolva estes resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="max"></a>

### <a name="max"></a>max

Devolva o valor mais alto de uma lista ou matriz com números que são inclusivos em ambas as extremidades.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*número1*>, <*número2*>, ... | Sim | Inteiro, Float, ou ambos | O conjunto de números a partir dos quais você quer o valor mais alto |
| [<número *1*>, <> *número2,* ...] | Sim | Matriz - Inteiro, Float, ou ambos | A variedade de números a partir dos quais você quer o valor mais alto |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor máximo*> | Inteiro ou Flutuante | O valor mais elevado na matriz especificada ou conjunto de números |
||||

*Exemplo*

Estes exemplos obtêm o valor mais alto a partir do conjunto de números e da matriz:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

E devolva este resultado: `3`

<a name="min"></a>

### <a name="min"></a>min

Devolva o valor mais baixo de um conjunto de números ou de uma matriz.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*número1*>, <*número2*>, ... | Sim | Inteiro, Float, ou ambos | O conjunto de números a partir dos quais se pretende o valor mais baixo |
| [<número *1*>, <> *número2,* ...] | Sim | Matriz - Inteiro, Float, ou ambos | A variedade de números a partir dos quais se quer o valor mais baixo |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*min-valor*> | Inteiro ou Flutuante | O valor mais baixo no conjunto especificado de números ou matriz especificado |
||||

*Exemplo*

Estes exemplos obtêm o valor mais baixo no conjunto de números e na matriz:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

E devolva este resultado: `1`

<a name="mod"></a>

### <a name="mod"></a>mod

Devolva o resto da divisão de dois números.
Para obter o resultado inteiro, consulte [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*dividendo*> | Sim | Inteiro ou Flutuante | O número a dividir pelo *divisor* |
| <*divisor*> | Sim | Inteiro ou Flutuante | O número que divide o *dividendo,* mas não pode ser 0. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*modulo-resultado*> | Inteiro ou Flutuante | O restante de dividir o primeiro número pelo segundo número |
||||

*Exemplo*

Este exemplo divide o primeiro número pelo segundo número:

```
mod(3, 2)
```

E devolva este resultado: `1`

<a name="mul"></a>

### <a name="mul"></a>mul

Retire o produto da multiplicação de dois números.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Sim | Inteiro ou Flutuante | O número a multiplicar por *multiplicand2* |
| <*multiplicand2*> | Sim | Inteiro ou Flutuante | O número que múltiplos *multiplicand1* |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado do produto*> | Inteiro ou Flutuante | O produto de multiplicar o primeiro número pelo segundo número |
||||

*Exemplo*

Estes exemplos múltiplos o primeiro número pelo segundo número:

```
mul(1, 2)
mul(1.5, 2)
```

E devolva estes resultados:

* Primeiro exemplo: `2`
* Segundo exemplo `3`

<a name="not"></a>

### <a name="not"></a>not

Verifique se uma expressão é falsa.
Devolva-se quando a expressão é falsa, ou devolva falsa quando verdadeira.

```json
not(<expression>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expressão*> | Sim | Booleano | A expressão para verificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retornar verdadeiro quando a expressão é falsa. Devolva-se falsa quando a expressão é verdadeira. |
||||

*Exemplo 1*

Estes exemplos verificam se as expressões especificadas são falsas:

```json
not(false)
not(true)
```

E devolva estes resultados:

* Primeiro exemplo: A expressão é falsa, por isso a função retorna `true` .
* Segundo exemplo: A expressão é verdadeira, por isso a função retorna `false` .

*Exemplo 2*

Estes exemplos verificam se as expressões especificadas são falsas:

```json
not(equals(1, 2))
not(equals(1, 1))
```

E devolva estes resultados:

* Primeiro exemplo: A expressão é falsa, por isso a função retorna `true` .
* Segundo exemplo: A expressão é verdadeira, por isso a função retorna `false` .

<a name="or"></a>

### <a name="or"></a>ou

Verifique se pelo menos uma expressão é verdadeira.
Devolva-se quando pelo menos uma expressão é verdadeira, ou devolva falsa quando ambas são falsas.

```
or(<expression1>, <expression2>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expressão1*>, *expressão <2*> | Sim | Booleano | As expressões para verificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Voltar verdadeiro quando pelo menos uma expressão é verdadeira. Devolva-se falsa quando ambas as expressões são falsas. |
||||

*Exemplo 1*

Estes exemplos verificam se pelo menos uma expressão é verdadeira:

```json
or(true, false)
or(false, false)
```

E devolva estes resultados:

* Primeiro exemplo: Pelo menos uma expressão é verdadeira, por isso a função retorna `true` .
* Segundo exemplo: Ambas as expressões são falsas, pelo que a função retorna `false` .

*Exemplo 2*

Estes exemplos verificam se pelo menos uma expressão é verdadeira:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

E devolva estes resultados:

* Primeiro exemplo: Pelo menos uma expressão é verdadeira, por isso a função retorna `true` .
* Segundo exemplo: Ambas as expressões são falsas, pelo que a função retorna `false` .

<a name="rand"></a>

### <a name="rand"></a>rand

Devolva um inteiro aleatório de uma gama especificada, que é inclusiva apenas na extremidade inicial.

```
rand(<minValue>, <maxValue>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Sim | Número inteiro | O número mais baixo de inteiros na gama |
| <*maxValue*> | Sim | Número inteiro | O inteiro que segue o mais alto inteiro na gama que a função pode devolver |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado aleatório*> | Número inteiro | O número inteiro aleatório devolvido da gama especificada |
||||

*Exemplo*

Este exemplo obtém um número inteiro aleatório da gama especificada, excluindo o valor máximo:

```
rand(1, 5)
```

E devolve um destes números como resultado: `1` `2` , ou `3``4`

<a name="range"></a>

### <a name="range"></a>gama

Devolva uma matriz de inteiros que começa a partir de um inteiro especificado.

```
range(<startIndex>, <count>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | Sim | Número inteiro | Um valor inteiro que inicia a matriz como o primeiro item |
| <*contar*> | Sim | Número inteiro | O número de inteiros na matriz |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<> *de resultados de gama]* | Matriz | A matriz com inteiros a partir do índice especificado |
||||

*Exemplo*

Este exemplo cria uma matriz de inteiros que começa a partir do índice especificado e tem o número especificado de inteiros:

```
range(1, 4)
```

E devolve este resultado: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>substituir

Substitua um sub-adc de sublagem com a corda especificada e devolva a cadeia de resultados. Esta função é sensível a casos.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Texto*> | Sim | String | A corda que tem o sub-cordão para substituir |
| <*oldText*> | Sim | String | O sub-cordão para substituir |
| <*newText*> | Sim | String | A cadeia de substituição |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*texto atualizado*> | String | A cadeia atualizada após a substituição do sub-cordão <p>Se o sub-adc de sublagem não for encontrado, devolva a corda original. |
||||

*Exemplo*

Este exemplo encontra o sublpesante "velho" na "velha corda" e substitui "velho" por "novo":

```
replace('the old string', 'old', 'new')
```

E devolve este resultado: `"the new string"`

<a name="skip"></a>

### <a name="skip"></a>saltar

Retire os itens da parte da frente de uma coleção e devolva *todos os outros* itens.

```
skip([<collection>], <count>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Matriz | A coleção cujos itens você quer remover |
| <*contar*> | Sim | Número inteiro | Um número inteiro positivo para o número de itens a remover na frente |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<> *de recolha atualizada]* | Matriz | A recolha atualizada após a remoção dos itens especificados |
||||

*Exemplo*

Este exemplo remove um item, o número 0, da parte frontal da matriz especificada:

```
skip(createArray(0, 1, 2, 3), 1)
```

E devolve esta matriz com os restantes itens: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>dividir

Devolva uma matriz que contenha sublutos, separadas por vírgulas, com base no carácter delimiter especificado na cadeia original.

```
split('<text>', '<delimiter>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Texto*> | Sim | String | A corda para separar em sublamentos com base no limonado especificado na cadeia original |
| <*delimiter*> | Sim | String | O personagem na corda original para usar como o delimiter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<*substring1*><*substring2*>,...] | Matriz | Uma matriz que contém sublutos da corda original, separada por vírgulas |
||||

*Exemplo*

Este exemplo cria uma matriz com sublípedos a partir da cadeia especificada com base no carácter especificado como olimiter:

```
split('a_b_c', '_')
```

E devolve esta matriz como resultado: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Volte o início do dia para uma hora de tempo.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*hora atualizada*> | String | A marca de tempo especificada, mas a partir da marca de zero horas para o dia |
||||

*Exemplo*

Este exemplo encontra o início do dia para esta hora:

```
startOfDay('2018-03-15T13:30:30Z')
```

E devolve este resultado: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Volte a ligar o início da hora para uma hora marcada.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*hora atualizada*> | String | A marca de tempo especificada, mas a partir da marca de zero minutos para a hora |
||||

*Exemplo*

Este exemplo encontra o início da hora para esta hora:

```
startOfHour('2018-03-15T13:30:30Z')
```

E devolve este resultado: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Devolva o início do mês para uma hora de tempo.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*hora atualizada*> | String | A marca de tempo especificada, mas a partir do primeiro dia do mês na marca de zero horas |
||||

*Exemplo*

Este exemplo devolve o início do mês para esta hora:

```
startOfMonth('2018-03-15T13:30:30Z')
```

E devolve este resultado: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Verifique se uma corda começa com um sub-adcção específico.
Devolva-se quando o sub-adcção for encontrado, ou devolva falso quando não for encontrado.
Esta função não é sensível a casos.

```
startsWith('<text>', '<searchText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Texto*> | Sim | String | A corda para verificar |
| <*searchText*> | Sim | String | A cadeia inicial para encontrar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false  | Booleano | Volte a ser verdadeiro quando o sub-ataque inicial for encontrado. Devolva-se falso quando não for encontrado. |
||||

*Exemplo 1*

Este exemplo verifica se a cadeia "Hello world" começa com o sublpesamento "olá":

```
startsWith('hello world', 'hello')
```

E devolve este resultado: `true`

*Exemplo 2*

Este exemplo verifica se a cadeia "Hello world" começa com o sub-cordão das "saudações":

```
startsWith('hello world', 'greetings')
```

E devolve este resultado: `false`

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
| <*valor de cordas*> | String | A versão de corda para o valor especificado |
||||

*Exemplo 1*

Este exemplo cria a versão de corda para este número:

```
string(10)
```

E devolve este resultado: `"10"`

*Exemplo 2*

Este exemplo cria uma cadeia para o objeto JSON especificado e utiliza o carácter de backslash \\ () como um personagem de fuga para a marca de dupla citação (").

```
string( { "name": "Sophie Owen" } )
```

E devolve este resultado: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

Devolva o resultado da subtração do segundo número do primeiro número.

```
sub(<minuend>, <subtrahend>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | Sim | Inteiro ou Flutuante | O número a partir do qual subtrair o *subtraído* |
| <*subtrahend*> | Sim | Inteiro ou Flutuante | O número para subtrair do *minuend* |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado*> | Inteiro ou Flutuante | O resultado da subtração do segundo número do primeiro número |
||||

*Exemplo*

Este exemplo subtrai o segundo número do primeiro número:

```
sub(10.3, .3)
```

E devolve este resultado: `10`

<a name="substring"></a>

### <a name="substring"></a>substring

Devolva os caracteres de uma cadeia, a partir da posição especificada, ou índice.
Os valores do índice começam com o número 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Texto*> | Sim | String | A corda cujos personagens quer |
| <*startIndex*> | Sim | Número inteiro | Um número positivo igual ou superior a 0 que pretende utilizar como posição inicial ou valor de índice |
| <*comprimento*> | Sim | Número inteiro | Um número positivo de caracteres que você quer no substring |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*subdiscípe-resultado*> | String | Um sub-cordão com o número especificado de caracteres, a partir da posição de índice especificado na cadeia de origem |
||||

*Exemplo*

Este exemplo cria um sublamamento de cinco caracteres a partir da cadeia especificada, a partir do valor do índice 6:

```
substring('hello world', 6, 5)
```

E devolve este resultado: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtraindoFromTime

Subtraia uma série de unidades de tempo de um relógio.
Consulte também [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*intervalo*> | Sim | Número inteiro | O número de unidades de tempo especificadas para subtrair |
| <*timeUnit*> | Sim | String | A unidade de tempo a utilizar com *intervalo:*"Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*hora atualizada*> | String | O tempotamp menos o número especificado de unidades de tempo |
||||

*Exemplo 1*

Este exemplo subtrai um dia a partir desta hora:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

E devolve este resultado: `"2018-01-01T00:00:00:0000000Z"`

*Exemplo 2*

Este exemplo subtrai um dia a partir desta hora:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

E devolve este resultado usando o formato "D" opcional: `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take

Devolva os artigos da parte da frente de uma coleção.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Corda ou Matriz | A coleção cujos itens você quer |
| <*contar*> | Sim | Número inteiro | Um número inteiro positivo para o número de itens que você quer da frente |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*subconjunto*> ou [<*subconjunto*>] | String ou Array, respectivamente | Uma cadeia ou matriz que tem o número especificado de itens retirados da parte da frente da coleção original |
||||

*Exemplo*

Estes exemplos obtêm o número especificado de itens da parte da frente destas coleções:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

E devolva estes resultados:

* Primeiro exemplo: `"abc"`
* Segundo exemplo: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>carrapatos

Devolva o `ticks` valor da propriedade para um tempotando especificado.
Um *carrapato* é um intervalo de 100 nanosegundos.

```
ticks('<timestamp>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda para uma hora de tempo |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carrapatos-número*> | Número inteiro | O número de carrapatos desde a marca de tempo especificado |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Devolva uma corda em formato minúsculo. Se um personagem na corda não tiver uma versão minúscula, esse personagem permanece inalterado na corda devolvida.

```
toLower('<text>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Texto*> | Sim | String | A corda para voltar em formato minúsculo |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*texto minúsculo*> | String | A corda original em formato minúsculo |
||||

*Exemplo*

Este exemplo converte esta cadeia em minúsculas:

```
toLower('Hello World')
```

E devolve este resultado: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toupper

Devolva uma corda em formato maiúscula. Se um personagem na corda não tiver uma versão maiúscula, esse personagem permanece inalterado na corda devolvida.

```
toUpper('<text>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Texto*> | Sim | String | A corda para voltar em formato maiúscula |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*texto maiúscula*> | String | A corda original em formato maiúscula |
||||

*Exemplo*

Este exemplo converte esta cadeia em maiúsculas:

```
toUpper('Hello World')
```

E devolve este resultado: `"HELLO WORLD"`

<a name="trim"></a>

### <a name="trim"></a>guarnição

Remova o espaço branco de uma corda e devolva a corda atualizada.

```
trim('<text>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Texto*> | Sim | String | A corda que tem o espaço branco líder e trailing para remover |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*texto atualizado*> | String | Uma versão atualizada para a cadeia original sem liderar ou seguir espaço em branco |
||||

*Exemplo*

Este exemplo remove o espaço branco líder e trailing da cadeia " Hello World ":

```
trim(' Hello World  ')
```

E devolve este resultado: `"Hello World"`

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
| <*coleção1*>, <*coleção2*>, ...  | Sim | Matriz ou Objeto, mas não ambos | As coleções de onde você quer *todos os* itens |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*actualizaçãoCollection*> | Matriz ou Objeto, respectivamente | Uma coleção com todos os itens das coleções especificadas - sem duplicados |
||||

*Exemplo*

Este exemplo obtém *todos os* itens destas coleções:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

E devolve este resultado: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponente

Retornar uma versão codificada por um identificador de recursos uniforme (URI) para uma cadeia, substituindo caracteres inseguros por URL por caracteres de fuga.
Utilize esta função em vez de [codificar oUriComponent()](#encodeUriComponent).
Embora ambas as funções funcionem da mesma forma, `uriComponent()` é preferível.

```
uriComponent('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A corda para converter para formato codificado URI |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*codificado-uri*> | String | A cadeia codificada uri com caracteres de fuga |
||||

*Exemplo*

Este exemplo cria uma versão codificada uri para esta cadeia:

```
uriComponent('https://contoso.com')
```

E devolve este resultado: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Devolva a versão binária para um componente de identificação de recursos uniforme (URI).

```
uriComponentToBinary('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia codificada uri para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*binário-para-codificado-uri*> | String | A versão binária para a cadeia codificada uri. O conteúdo binário é codificado e representado por `$content` . |
||||

*Exemplo*

Este exemplo cria a versão binária para esta cadeia codificada uri:

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

Retorna a versão de corda para uma cadeia codificada de identificação de recursos uniforme (URI), descodificando eficazmente a cadeia codificada uri.

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

Este exemplo cria a versão de corda descodificada para esta cadeia codificada uri:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

E devolve este resultado: `"https://contoso.com"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Devolva a atual estação de tempo.

```
utcNow('<format>')
```

Opcionalmente, pode especificar um formato diferente com o *<formato*> parâmetro.

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*actual-timetamp*> | String | A data e hora atuais |
||||

*Exemplo 1*

Suponha que hoje é 15 de abril de 2018 às 13:00.
Este exemplo obtém a atual hora:

```
utcNow()
```

E devolve este resultado: `"2018-04-15T13:00:00.0000000Z"`

*Exemplo 2*

Suponha que hoje é 15 de abril de 2018 às 13:00.
Este exemplo obtém a atual timetamp utilizando o formato "D" opcional:

```
utcNow('D')
```

E devolve este resultado: `"Sunday, April 15, 2018"`

<a name="xml"></a>

### <a name="xml"></a>xml

Retornar a versão XML para uma cadeia que contenha um objeto JSON.

```
xml('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A corda com o objeto JSON para converter <p>O objeto JSON deve ter apenas uma propriedade raiz, que não pode ser uma matriz. <br>Utilize o carácter de backslash \\ () como um personagem de fuga para a marca de dupla citação ("). |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*versão xml*> | Objeto | O XML codificado para o string especificado ou objeto JSON |
||||

*Exemplo 1*

Este exemplo cria a versão XML para esta cadeia, que contém um objeto JSON:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

E devolve este resultado XML:

```xml
<name>Sophia Owen</name>
```

*Exemplo 2*

Suponha que tem este objeto JSON:

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

Verifique se xML se trata de nós ou valores que correspondam a uma expressão XPath (XML Path Language) e devolva os nós ou valores correspondentes. Uma expressão XPath, ou apenas "XPath", ajuda-o a navegar numa estrutura de documento XML para que possa selecionar nós ou valores de cálculo no conteúdo XML.

```
xpath('<xml>', '<xpath>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*xml*> | Sim | Qualquer | A cadeia XML para procurar nóles ou valores que correspondam a um valor de expressão XPath |
| <*xpath*> | Sim | Qualquer | A expressão XPath usada para encontrar nosdes ou valores XML correspondentes |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*xml-nó*> | XML | Um nó XML quando apenas um nó corresponde à expressão XPath especificada |
| <*valor*> | Qualquer | O valor de um nó XML quando apenas um valor corresponde à expressão XPath especificada |
| [<> *xml-node1,* <*> xml-node2,* ...] </br>-ou- </br>[<*valor1*>, <*valor2*>, ...] | Matriz | Uma matriz com nódoas xml ou valores que correspondem à expressão XPath especificada |
||||

*Exemplo 1*

Seguindo no Exemplo 1, este exemplo encontra nós que combinam com o `<count></count>` nó e adiciona esses valores de nó com a `sum()` função:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

E devolve este resultado: `30`

*Exemplo 2*

Para este exemplo, ambas as expressões encontram nós que combinam com o `<location></location>` nó, nos argumentos especificados, que incluem XML com um espaço de nome. As expressões usam o carácter de backslash \\ () como um personagem de fuga para a marca de dupla citação (").

* *Expressão 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Expressão 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

Aqui estão os argumentos:

* Este XML, que inclui o espaço de nomes de documentos XML, `xmlns="http://contoso.com"` :

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Ou a expressão XPath aqui:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

Aqui está o nó de resultado que combina com o `<location></location>` nó:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Exemplo 3*

Seguindo no Exemplo 3, este exemplo encontra o valor no `<location></location>` nó:

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

E devolve este resultado: `"Paris"`

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de variáveis do sistema que pode utilizar em expressões, consulte [variáveis do Sistema.](control-flow-system-variables.md)