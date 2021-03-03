---
title: Transformação de dados de parse no fluxo de dados de mapeamento
description: Documentos de colunas embutidos em parse
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 4db9503ea84ae13148a89a03048c73399413e5cc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710197"
---
# <a name="parse-transformation-in-mapping-data-flow"></a>Transformação de parse no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilize a transformação parse para analisar colunas nos seus dados que estão em forma de documento. Os tipos atuais de documentos incorporados que podem ser analisados são JSON e texto delimitado.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWykdO]

## <a name="configuration"></a>Configuração

No painel de configuração de transformação de parse, irá primeiro escolher o tipo de dados contidos nas colunas que deseja analisar inline. A transformação da parse também contém as seguintes definições de configuração.

![Definições de parse](media/data-flow/data-flow-parse-1.png "Analisar")

### <a name="column"></a>Coluna

Semelhante a colunas e agregados derivados, é aqui que irá modificar uma coluna de saída selecionando-a a partir do apanhador de queda. Ou pode escrever o nome de uma nova coluna aqui. A ADF armazenará os dados de origem analisados nesta coluna.

### <a name="expression"></a>Expression

Utilize o construtor de expressão para definir a fonte para a sua análise. Isto pode ser tão simples como apenas selecionar a coluna de origem com os dados autossuficientes que deseja analisar, ou pode criar expressões complexas para analisar.

### <a name="output-column-type"></a>Tipo de coluna de saída

Aqui é onde irá configurar o esquema de saída do alvo a partir da análise que será escrita numa única coluna.

![Exemplo de parse](media/data-flow/data-flow-parse-2.png "Exemplo de parse")

Neste exemplo, definimos a análise do campo de entrada "jsonString" que é texto simples, mas formatado como uma estrutura JSON. Vamos armazenar os resultados analisados como JSON numa nova coluna chamada "json" com este esquema:

```(trade as boolean, customers as string[])```

Consulte o separador de inspeção e a pré-visualização de dados para verificar se a sua saída está corretamente mapeada.

## <a name="examples"></a>Exemplos

```
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    documentForm: 'documentPerLine') ~> JsonSource
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false) ~> CsvSource
JsonSource derive(jsonString = toString(goods)) ~> StringifyJson
StringifyJson parse(json = jsonString ? (trade as boolean,
        customers as string[]),
    format: 'json',
    documentForm: 'arrayOfDocuments') ~> ParseJson
CsvSource derive(csvString = 'Id|name|year\n\'1\'|\'test1\'|\'1999\'') ~> CsvString
CsvString parse(csv = csvString ? (id as integer,
        name as string,
        year as string),
    format: 'delimited',
    columnNamesAsHeader: true,
    columnDelimiter: '|',
    nullValue: '',
    documentForm: 'documentPerLine') ~> ParseCsv
ParseJson select(mapColumn(
        jsonString,
        json
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedJson
ParseCsv select(mapColumn(
        csvString,
        csv
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedCsv
```

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Syntax

### <a name="examples"></a>Exemplos

```
parse(json = jsonString ? (trade as boolean,
                                customers as string[]),
                format: 'json',
                documentForm: 'singleDocument') ~> ParseJson

parse(csv = csvString ? (id as integer,
                                name as string,
                                year as string),
                format: 'delimited',
                columnNamesAsHeader: true,
                columnDelimiter: '|',
                nullValue: '',
                documentForm: 'documentPerLine') ~> ParseCsv
```    

## <a name="next-steps"></a>Passos seguintes

* Utilize a [transformação de Flatten](data-flow-flatten.md) para pisar linhas em colunas.
* Utilize a [transformação da coluna derivada](data-flow-derived-column.md) para girar colunas em linhas.
