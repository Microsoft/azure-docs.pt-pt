---
title: Formatos de dados suportados pelo Azure Data Explorer para ingestão.
description: Conheça os vários formatos de dados e compressão suportados pelo Azure Data Explorer para ingestão.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 16ecdf7ac3f0062637e4bbea86d26e2560f38222
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235305"
---
# <a name="data-formats-supported-by-azure-data-explorer-for-ingestion"></a>Formatos de dados suportados pelo Azure Data Explorer para ingestão

A ingestão de dados é o processo pelo qual os dados são adicionados a uma tabela e são disponibilizados para consulta no Azure Data Explorer. Para todos os métodos de ingestão, para além de ingerirem-de-consulta, os dados devem estar num dos formatos suportados. A tabela seguinte lista e descreve os formatos que o Azure Data Explorer suporta para a ingestão de dados.

|Formato   |Extensão   |Descrição|
|---------|------------|-----------|
|Avro     |`.avro`     |Um ficheiro de [contentora Avro](https://avro.apache.org/docs/current/). Os seguintes códigos `null`são `deflate` `snappy` suportados: ( não é atualmente suportado).|
|CSV      |`.csv`      |Um ficheiro de texto com valores`,`separados pela vírem ( ). Ver [RFC 4180: _Formato comum e tipo MIME para Ficheiros De Valores Separados comma (CSV)_](https://www.ietf.org/rfc/rfc4180.txt).|
|JSON     |`.json`     |Um ficheiro de texto com objetos JSON delimitados por `\n` ou `\r\n`. Ver [linhas JSON (JSONL)](http://jsonlines.org/).|
|MultiJSON|`.multijson`|Um ficheiro de texto com um conjunto json de sacos de propriedade (cada um `\n` representando `\r\n`um recorde), ou qualquer número de sacos de propriedade delimitados pelo whitespace, ou . Cada saco de propriedade pode ser distribuído em várias linhas. Este formato `JSON`é preferido, a menos que os dados sejam sacos não propriedade.|
|ORC      |`.orc`      |Um [ficheiro Orc.](https://en.wikipedia.org/wiki/Apache_ORC)|
|Parquet  |`.parquet`  |Um [ficheiro Parquet.](https://en.wikipedia.org/wiki/Apache_Parquet)|
|PSV      |`.psv`      |Um ficheiro de texto com<code>&#124;</code>valores separados por tubos ().|
|CRU      |`.raw`      |Um ficheiro de texto cujo conteúdo completo é um único valor de cadeia.|
|SCSV     |`.scsv`     |Um ficheiro de texto com valores separados pelo ponto-e-vírgula ().`;`|
|SOHsv    |`.sohsv`    |Um ficheiro de texto com valores separados sOH. (SOH é o ponto de código ASCII 1; este formato é utilizado pela Hive em HDInsight.)|
|Rio TSV      |`.tsv`      |Um ficheiro de texto com`\t`valores separados por separados por separado ().|
|TSVE     |`.tsv`      |Um ficheiro de texto com`\t`valores separados por separados por separado (). Um personagem`\`de backslash é usado para escapar.|
|TXT      |`.txt`      |Um ficheiro de texto `\n`com linhas delimitadas por . As linhas vazias são ignoradas.|

## <a name="supported-data-compression-formats"></a>Formatos de compressão de dados suportados

Bolhas e ficheiros podem ser comprimidos através de qualquer um dos seguintes algoritmos de compressão:

|Compressão|Extensão|
|-----------|---------|
|GZip       |.gz      |
|Zip        |.zip     |

Indicar a compressão, adotando a extensão ao nome da bolha ou do ficheiro.

Por exemplo:
* `MyData.csv.zip`indica uma bolha ou um ficheiro formatado como CSV, comprimido com ZIP (arquivo ou um único ficheiro)
* `MyData.csv.gz`indica uma bolha ou um ficheiro formatado como CSV, comprimido com GZip

Os nomes blob ou ficheiros que não incluem as extensões do formato, mas apenas a compressão (por exemplo, ) também são suportados. Neste caso, o formato de ficheiro deve ser especificado como um imóvel de ingestão porque não pode ser inferido.

> [!NOTE]
> Alguns formatos de compressão acompanham a extensão original do ficheiro como parte da corrente comprimido. Esta extensão é geralmente ignorada para determinar o formato do ficheiro. Se o formato de ficheiro não puder ser determinado a partir da bolha (comprimido) ou nome de ficheiro, deve ser especificado através da `format` propriedade de ingestão.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a ingestão de dados](/azure/data-explorer/ingest-data-overview)
* Saiba mais sobre propriedades de [ingestão](ingestion-properties.md) de dados do Azure Data Explorer
