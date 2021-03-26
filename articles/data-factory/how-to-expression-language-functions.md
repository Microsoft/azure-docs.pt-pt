---
title: Como utilizar parâmetros e expressões na Azure Data Factory
description: Este artigo Como Artigo fornece informações sobre expressões e funções que pode usar na criação de entidades de fábrica de dados.
author: ssabat
ms.author: susabat
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/08/2020
ms.openlocfilehash: c9e1abc5fb6f66981f56bc262319587d9fc4265e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566664"
---
# <a name="how-to-use-parameters-expressions-and-functions-in-azure-data-factory"></a>Como utilizar parâmetros, expressões e funções na Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-functions-variables.md)
> * [Versão atual](how-to-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Neste documento, vamos focar-nos principalmente na aprendizagem de conceitos fundamentais com vários exemplos para explorar a capacidade de criar oleodutos de dados parametrizados dentro da Azure Data Factory. A parametrização e as expressões dinâmicas são adições tão notáveis à ADF porque podem poupar uma quantidade tremenda de tempo e permitir uma solução muito mais flexível de Extrato, Transformação, Carga (ETL) ou Extrato, Carga, Transformação (ELT), que reduzirá drasticamente o custo de manutenção da solução e acelerará a implementação de novas funcionalidades em oleodutos existentes. Estes ganhos são porque a parametrização minimiza a quantidade de codificação dura e aumenta o número de objetos e processos reutilizáveis numa solução.

## <a name="azure-data-factory-ui-and-parameters"></a>UI da fábrica de dados Azure e parâmetros

Se é novo na utilização de parâmetros de fábrica de dados Azure na interface de utilizador ADF, por favor reveja [a UI da fábrica de dados para serviços ligados com parâmetros](./parameterize-linked-services.md#data-factory-ui)  e [UI de fábrica de dados para pipeline conduzido por metadados com parâmetros](./how-to-use-trigger-parameterization.md#data-factory-ui) para explicação visual.

## <a name="parameter-and-expression-concepts"></a>Conceitos de parâmetro e expressão 

Pode utilizar parâmetros para passar valores externos em oleodutos, conjuntos de dados, serviços ligados e fluxos de dados. Uma vez que o parâmetro tenha sido passado para o recurso, não pode ser alterado. Ao parametrizar recursos, pode reutilizá-los com valores diferentes de cada vez. Os parâmetros podem ser usados individualmente ou como parte de expressões. Os valores de JSON na definição podem ser literais ou expressões que são avaliadas em tempo de execução.

Por exemplo:  
  
```json
"name": "value"
```

 ou  
  
```json
"name": "@pipeline().parameters.password"
```

As expressões podem aparecer em qualquer lugar num valor de corda JSON e resultam sempre em outro valor JSON. Aqui, *a palavra-passe* é um parâmetro de pipeline na expressão. Se um valor JSON for uma expressão, o corpo da expressão é extraído removendo o sinal de \@ at(). Se for necessária uma corda literal que comece \@ por, deve ser escapado utilizando \@ \@ . Os exemplos a seguir mostram como as expressões são avaliadas.  
  
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

## <a name="examples-of-using-parameters-in-expressions"></a>Exemplos de utilização de parâmetros em expressões 

### <a name="complex-expression-example"></a>Exemplo de expressão complexa
O exemplo abaixo mostra um exemplo complexo que faz referência a um sub-campo profundo de produção de atividade. Para fazer referência a um parâmetro de gasoduto que avalie para um sub-campo, utilize a sintaxe [] em vez do operador ponto(como no caso do subcampo1 e subcampo2)

`@activity('*activityName*').output.*subfield1*.*subfield2*[pipeline().parameters.*subfield3*].*subfield4*`

### <a name="dynamic-content-editor"></a>Editor de conteúdo dinâmico

O editor de conteúdo dinâmico escapa automaticamente aos caracteres do seu conteúdo quando termina a edição. Por exemplo, o seguinte conteúdo no editor de conteúdo é uma interpolação de cordas com duas funções de expressão. 

```json
{ 
  "type": "@{if(equals(1, 2), 'Blob', 'Table' )}",
  "name": "@{toUpper('myData')}"
}
```

Editor de conteúdo dinâmico converte conteúdo acima em `"{ \n  \"type\": \"@{if(equals(1, 2), 'Blob', 'Table' )}\",\n  \"name\": \"@{toUpper('myData')}\"\n}"` expressão. O resultado desta expressão é uma corda de formato JSON mostrada abaixo.

```json
{
  "type": "Table",
  "name": "MYDATA"
}
```

### <a name="a-dataset-with--parameters"></a>Um conjunto de dados com parâmetros

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

### <a name="a-pipeline-with--parameters"></a>Um oleoduto com parâmetros

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

  
## <a name="calling-functions-within-expressions"></a>Funções de chamada dentro de expressões 

Pode chamar funções dentro de expressões. As seguintes secções fornecem informações sobre as funções que podem ser usadas numa expressão.  

### <a name="string-functions"></a>Funções de cadeia  

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

### <a name="collection-functions"></a>Funções de coleção

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
| [tomar](control-flow-expression-language-functions.md#take) | Devolva os artigos da parte da frente de uma coleção. |
| [união](control-flow-expression-language-functions.md#union) | Devolva uma coleção que tenha *todos os* itens das coleções especificadas. | 

### <a name="logical-functions"></a>Funções lógicas  

Estas funções são úteis dentro de condições, podem ser usadas para avaliar qualquer tipo de lógica.  
  
| Função de comparação lógica | Tarefa |
| --------------------------- | ---- |
| [and](control-flow-expression-language-functions.md#and) | Verifique se todas as expressões são verdadeiras. |
| [é igual a](control-flow-expression-language-functions.md#equals) | Verifique se ambos os valores são equivalentes. |
| [greater](control-flow-expression-language-functions.md#greater) | Verifique se o primeiro valor é maior do que o segundo valor. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | Verifique se o primeiro valor é maior ou igual ao segundo valor. |
| [se](control-flow-expression-language-functions.md#if) | Verifique se uma expressão é verdadeira ou falsa. Com base no resultado, devolva um valor especificado. |
| [menos](control-flow-expression-language-functions.md#less) | Verifique se o primeiro valor é inferior ao segundo valor. |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | Verifique se o primeiro valor é inferior ou igual ao segundo valor. |
| [não](control-flow-expression-language-functions.md#not) | Verifique se uma expressão é falsa. |
| [ou](control-flow-expression-language-functions.md#or) | Verifique se pelo menos uma expressão é verdadeira. |
  
### <a name="conversion-functions"></a>Funções de conversão  

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
| [binário](control-flow-expression-language-functions.md#binary) | Devolva a versão binária por um valor de entrada. |
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

### <a name="math-functions"></a>Funções matemáticas  
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
  
### <a name="date-functions"></a>Funções de data  

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

## <a name="detailed-examples-for-practice"></a>Exemplos detalhados para a prática

### <a name="detailed-azure-data-factory-copy-pipeline-with-parameters"></a>Pipeline de cópia de fábrica de dados Azure detalhado com parâmetros 

Este parâmetro de [cópia da fábrica de dados Azure passando tutorial](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) acompanha-o através de como passar parâmetros entre um oleoduto e atividade, bem como entre as atividades.

### <a name="detailed--mapping-data-flow-pipeline-with-parameters"></a>Pipeline de fluxo de dados de mapeamento detalhado com parâmetros 

Por favor, siga [o fluxo de dados de mapeamento com parâmetros](./parameters-data-flow.md) para um exemplo abrangente sobre como usar parâmetros no fluxo de dados.

### <a name="detailed-metadata-driven-pipeline-with-parameters"></a>Pipeline detalhado com metadados com parâmetros

Siga o [pipeline conduzido por metadados com parâmetros](./how-to-use-trigger-parameterization.md) para saber mais sobre como usar parâmetros para desenhar pipelines guiados por metadados. Este é um caso de uso popular para parâmetros.


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de variáveis do sistema que pode utilizar em expressões, consulte [variáveis do Sistema.](control-flow-system-variables.md)