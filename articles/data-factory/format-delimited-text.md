---
title: Formato de texto delimitado no Azure Data Factory | Documentos da Microsoft
description: Este tópico descreve como lidar com o formato de texto delimitado no Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 4e365ef01b8c7a89d61efad25bd18943e7b2d1a4
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64877972"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Formato de texto delimitado no Azure Data Factory

Siga este artigo, quando deseja **analisar os ficheiros de texto delimitado ou escrever os dados em formato de texto delimitado**. 

Formato de texto delimitado é suportado para os conectores seguintes: [Amazon S3](connector-amazon-simple-storage-service.md), [BLOBs do Azure](connector-azure-blob-storage.md), [do Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [do Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [File Storage do Azure](connector-azure-file-storage.md), [Sistema de ficheiros](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)e [ SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com o conjunto de dados de texto delimitado.

| Propriedade         | Descrição                                                  | Necessário |
| ---------------- | ------------------------------------------------------------ | -------- |
| tipo             | A propriedade de tipo do conjunto de dados tem de ser definida **DelimitedText**. | Sim      |
| localização         | Definições de localização dos ficheiros pelo. Cada conector baseado em arquivo tem seu próprio tipo de localização e suportada propriedades em `location`. **Ver os detalhes no artigo de conector -> secção de propriedades do conjunto de dados**. | Sim      |
| columnDelimiter  | Os carateres utilizado para separar colunas num ficheiro. Atualmente, o delimitador de char multi só é suportado para mapeamento de fluxo de dados, mas não a atividade de cópia. <br>O valor predefinido é **vírgula `,`** , quando o delimitador de coluna é definido como cadeia de caracteres vazia, que significa que nenhum delimitador, toda a linha é considerada uma única coluna. | Não       |
| rowDelimiter     | O único caractere ou "\r\n" utilizado para separar linhas num ficheiro.<br>O valor predefinido é qualquer um dos seguintes valores **na leitura: ["\r\n", "\r", "\n"]**, e **"\n" ou "\r\n" na escrita** por dados de mapeamento de fluxo e atividade de cópia, respetivamente. <br>Quando `rowDelimiter` está definido como nenhum delimitador (cadeia vazia), o `columnDelimiter` tem de ser definido como nenhum delimitador (cadeia de caracteres vazia), como também, que significa que tratar de todo o conteúdo como um valor único. | Não       |
| quoteChar        | O caráter único citar valores de coluna se contiver delimitador de colunas. <br>O valor predefinido é **as aspas duplas** `"`. <br>Para o mapeamento de fluxo de dados, `quoteChar` não pode ser uma cadeia vazia. <br>Para a atividade de cópia, quando `quoteChar` está definida como cadeia de caracteres vazia, significa que não existe nenhum caractere de aspas e valor da coluna não é indicada, e `escapeChar` é utilizado para escapar delimitador de colunas e ela própria. | Não       |
| escapeChar       | O único caráter de escape aspas dentro de um valor entre aspas.<br>O valor predefinido é **barra invertida `\`** . <br>Para o mapeamento de fluxo de dados, `escapeChar` não pode ser uma cadeia vazia. <br/>Para a atividade de cópia, quando `escapeChar` é definido como cadeia de caracteres vazia, o `quoteChar` tem de ser definido como também uma cadeia de caracteres vazia, caso em que certifique-se de que todos os valores de coluna não contém delimitadores. | Não       |
| firstRowAsHeader | Especifica se deve tratar/fazer a primeira linha como uma linha de cabeçalho com nomes de colunas.<br>Valores permitidos são **true** e **falso** (predefinição). | Não       |
| nullValue        | Especifica a representação de cadeia de valor nulo. <br>O valor predefinido é **vazio cadeia**. | Não       |
| encodingName     | O tipo de codificação utilizado para ler/escrever ficheiros de teste. <br>Valores permitidos são os seguintes: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", " IBM852 ","IBM855","IBM857","IBM860","IBM861","IBM863","IBM864","IBM865","IBM869","IBM870","IBM01140","IBM01141","IBM01142","IBM01143","IBM01144","IBM01145","IBM01146","IBM01147","IBM01148","IBM01149","ISO-2022-JP","ISO-2022-KR ","ISO-8859-1","ISO-8859-2","ISO-8859-3","ISO-8859-4","ISO-8859-5","ISO-8859-6","ISO-8859-7","ISO-8859-8","ISO-8859-9","ISO-8859-13","ISO-8859-15","WINDOWS-874","WINDOWS-1250","WINDOWS 1251","WINDOWS-1252","WINDOWS-1253"," WINDOWS-1254 ","WINDOWS-1255","WINDOWS 1256","WINDOWS-1257","WINDOWS-1258".<br>Tenha em atenção de que mapeamento de fluxo de dados não suporta a codificação UTF-7. | Não       |
| compressionCodec | O codec de compressão utilizado para ler/escrever ficheiros de texto. <br>Valores permitidos são **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **snappy**, ou **lz4**. Para utilizar quando guardar o ficheiro. <br>Tenha em atenção atualmente não suporta a atividade de cópia "rápido" & "lz4" e mapeamento de fluxo de dados não suporta "ZipDeflate". | Não       |
| compressionLevel | Taxa de compressão. <br>Valores permitidos são **Optimal** ou **Fastest**.<br>- **Mais rápida:** A operação de compactação deve concluir mais depressa que possível, mesmo que o ficheiro resultante não será compactado ideal.<br>- **Ideal**: A operação de compactação deve ser comprimida ideal, mesmo que a operação demora mais tempo a concluir. Para obter mais informações, consulte [nível de compactação](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) tópico. | Não       |

Segue-se um exemplo de conjunto de dados de texto delimitado no armazenamento de Blobs do Azure:

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista das propriedades compatíveis com a origem de texto delimitados e de sink.

### <a name="delimited-text-as-source"></a>Texto delimitado como origem 

As seguintes propriedades são suportadas na atividade de cópia ***\*origem\**** secção.

| Propriedade       | Descrição                                                  | Necessário |
| -------------- | ------------------------------------------------------------ | -------- |
| tipo           | A propriedade de tipo de origem de atividade de cópia tem de ser definida **DelimitedTextSource**. | Sim      |
| formatSettings | Um grupo de propriedades. Consulte a **definições de leitura de texto delimitado** tabela abaixo. | Não       |
| storeSettings  | Um grupo de propriedades sobre como ler dados a partir de um arquivo de dados. Cada conector baseado em arquivo tem suas próprias definições de leitura suportadas em `storeSettings`. **Ver os detalhes no artigo de conector -> secção de propriedades de atividade de cópia**. | Não       |

Suportado **definições de leitura de texto delimitado** sob `formatSettings`:

| Propriedade      | Descrição                                                  | Necessário |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | O tipo de formatSettings deve ser definido como **DelimitedTextReadSetting**. | Sim      |
| skipLineCount | Indica o número de **nula** linhas a ignorar durante a leitura de dados de ficheiros de entrada. <br>Se as propriedades skipLineCount e firstRowAsHeader forem especificadas simultaneamente, as linhas são ignoradas primeiro e, em seguida, as informações de cabeçalho são lidas a partir do ficheiro de entrada. | Não       |

### <a name="delimited-text-as-sink"></a>Texto delimitado como sink

As seguintes propriedades são suportadas na atividade de cópia ***\*sink\**** secção.

| Propriedade       | Descrição                                                  | Necessário |
| -------------- | ------------------------------------------------------------ | -------- |
| tipo           | A propriedade de tipo de origem de atividade de cópia tem de ser definida **DelimitedTextSink**. | Sim      |
| formatSettings | Um grupo de propriedades. Consulte a **escrita de texto delimitado definições** tabela abaixo. |          |
| storeSettings  | Um grupo de propriedades sobre como escrever dados para um arquivo de dados. Cada conector baseado em arquivo tem suas próprias definições de escrita suportadas em `storeSettings`. **Ver os detalhes no artigo de conector -> secção de propriedades de atividade de cópia**. | Não       |

Suportado **definições de escrita de texto delimitado** sob `formatSettings`:

| Propriedade      | Descrição                                                  | Necessário                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| tipo          | O tipo de formatSettings deve ser definido como **DelimitedTextWriteSetting**. | Sim                                                   |
| fileExtension | A extensão de arquivo usada para nomear os ficheiros de saída, por exemplo, `.csv`, `.txt`. Tem de ser especificada quando o `fileName` não for especificado na saída DelimitedText conjunto de dados. | Sim quando o nome de ficheiro não está especificado no conjunto de dados de saída |

## <a name="mapping-data-flow-properties"></a>Propriedades de fluxo de dados de mapeamento

Conheça os detalhes da [transformação de origem](data-flow-source.md) e [sink transformação](data-flow-sink.md) no mapeamento de fluxo de dados.

## <a name="next-steps"></a>Passos Seguintes

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)