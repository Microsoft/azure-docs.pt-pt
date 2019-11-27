---
title: Formato de texto delimitado no Azure Data Factory
description: Este tópico descreve como lidar com o formato de texto delimitado no Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 9a1a019e7dea2f0c71b40530baaffff79d003774
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74548287"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Formato de texto delimitado no Azure Data Factory

Siga este artigo quando desejar **analisar os arquivos de texto delimitados ou gravar os dados em um formato de texto delimitado**. 

O formato de texto delimitado tem suporte para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [armazenamento de arquivos do Azure](connector-azure-file-storage.md), [sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . Esta seção fornece uma lista das propriedades com suporte pelo conjunto de acordo de texto delimitado.

| Propriedade         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| tipo             | A propriedade Type do conjunto de conjuntos deve ser definida como **DelimitedText**. | Sim      |
| localização         | Configurações de local dos arquivos. Cada conector baseado em arquivo tem seu próprio tipo de local e propriedades com suporte em `location`.  | Sim      |
| columnDelimiter  | Os caracteres usados para separar colunas em um arquivo. Atualmente, o delimitador de vários caracteres só tem suporte para mapear o fluxo de dados, mas não para a atividade de cópia. <br>O valor padrão é **vírgula `,`** , quando o delimitador de coluna é definido como cadeia de caracteres vazia, o que significa que não há delimitador, a linha inteira é executada como uma única coluna. | Não       |
| rowDelimiter     | O caractere único ou "\r\n" usado para separar linhas em um arquivo.<br>O valor padrão é qualquer um dos seguintes valores **na leitura: ["\r\n", "\r", "\n"]** e **"\n" ou "\r\n" na gravação** mapeando o fluxo de dados e a atividade de cópia, respectivamente. <br>Quando `rowDelimiter` é definido como nenhum delimitador (cadeia de caracteres vazia), o `columnDelimiter` deve ser definido como nenhum delimitador (cadeia de caracteres vazia) também, o que significa tratar todo o conteúdo como um único valor. | Não       |
| quoteChar        | O caractere único para citar valores de coluna se ele contiver delimitador de coluna. <br>O valor padrão é **aspas duplas** `"`. <br>Para o mapeamento de fluxo de dados, `quoteChar` não pode ser uma cadeia de caracteres vazia. <br>Para a atividade de cópia, quando `quoteChar` é definida como cadeia de caracteres vazia, isso significa que não há nenhum caractere de aspas e o valor da coluna não está entre aspas e `escapeChar` é usado para escapar o delimitador de coluna e ele mesmo. | Não       |
| escapeChar       | O caractere único para escapar as aspas dentro de um valor entre aspas.<br>O valor padrão é **barra invertida `\`** . <br>Para o mapeamento de fluxo de dados, `escapeChar` não pode ser uma cadeia de caracteres vazia. <br/>Para a atividade de cópia, quando `escapeChar` é definido como uma cadeia de caracteres vazia, o `quoteChar` também deve ser definido como uma cadeia de caracteres vazia; nesse caso, verifique se todos os valores de coluna não contêm delimitadores. | Não       |
| firstRowAsHeader | Especifica se deve tratar/transformar a primeira linha como uma linha de cabeçalho com nomes de colunas.<br>Os valores permitidos são **true** e **false** (padrão). | Não       |
| nullValue        | Especifica a representação de cadeia de caracteres do valor nulo. <br>O valor padrão é uma **cadeia de caracteres vazia**. | Não       |
| encodingName     | O tipo de codificação usado para ler/gravar arquivos de teste. <br>Os valores permitidos são os seguintes: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", " IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149" , "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", " WINDOWS-1252 "," WINDOWS-1253 "," WINDOWS-1254 "," WINDOWS-1255 "," WINDOWS-1256 "," WINDOWS-1257 "," WINDOWS-1258 ".<br>Observação o fluxo de dados de mapeamento não dá suporte à codificação UTF-7. | Não       |
| compressionCodec | O codec de compactação usado para ler/gravar arquivos de texto. <br>Os valores permitidos são **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **snapshot**ou **lz4**. para usar ao salvar o arquivo. <br>Observação a atividade de cópia atualmente não dá suporte a "encaixar" & "lz4" e o fluxo de dados de mapeamento não dá suporte a "ZipDeflate". <br>Observação ao usar a atividade de cópia para descompactar arquivo (s) ZipDeflate e gravar no armazenamento de dados de coletor baseado em arquivo, os arquivos serão extraídos para a pasta: `<path specified in dataset>/<folder named as source zip file>/`. | Não       |
| compressionLevel | A taxa de compactação. <br>Os valores permitidos são **ideal** ou **mais rápido**.<br>- **mais rápido:** a operação de compactação deve ser concluída o mais rápido possível, mesmo que o arquivo resultante não seja compactado de maneira ideal.<br>- **ideal**: a operação de compactação deve ser corretamente compactada, mesmo se a operação levar mais tempo para ser concluída. Para obter mais informações, consulte o tópico [nível de compactação](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | Não       |

Abaixo está um exemplo de conjunto de texto delimitado no armazenamento de BLOBs do Azure:

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
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

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela fonte de texto delimitada e pelo coletor.

### <a name="delimited-text-as-source"></a>Texto delimitado como fonte 

As propriedades a seguir têm suporte na seção ***\*de origem*** da atividade de cópia\*.

| Propriedade       | Descrição                                                  | Obrigatório |
| -------------- | ------------------------------------------------------------ | -------- |
| tipo           | A propriedade Type da fonte da atividade de cópia deve ser definida como **DelimitedTextSource**. | Sim      |
| formatSettings | Um grupo de propriedades. Consulte a tabela de **configurações de leitura de texto delimitada** abaixo. | Não       |
| storeSettings  | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de leitura com suporte em `storeSettings`. | Não       |

**Configurações de leitura de texto delimitado** com suporte em `formatSettings`:

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | O tipo de formatSettings deve ser definido como **DelimitedTextReadSetting**. | Sim      |
| skipLineCount | Indica o número de linhas **não vazias** a serem ignoradas ao ler dados de arquivos de entrada. <br>Se as propriedades skipLineCount e firstRowAsHeader forem especificadas simultaneamente, as linhas são ignoradas primeiro e, em seguida, as informações de cabeçalho são lidas a partir do ficheiro de entrada. | Não       |

### <a name="delimited-text-as-sink"></a>Texto delimitado como coletor

As propriedades a seguir têm suporte na seção de ***\*do coletor*** de atividade de cópia\*.

| Propriedade       | Descrição                                                  | Obrigatório |
| -------------- | ------------------------------------------------------------ | -------- |
| tipo           | A propriedade Type da fonte da atividade de cópia deve ser definida como **DelimitedTextSink**. | Sim      |
| formatSettings | Um grupo de propriedades. Consulte a tabela de **configurações de gravação de texto delimitado** abaixo. |          |
| storeSettings  | Um grupo de propriedades sobre como gravar dados em um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de gravação com suporte em `storeSettings`.  | Não       |

**Configurações de gravação de texto delimitado** com suporte em `formatSettings`:

| Propriedade      | Descrição                                                  | Obrigatório                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| tipo          | O tipo de formatSettings deve ser definido como **DelimitedTextWriteSetting**. | Sim                                                   |
| fileExtension | A extensão de arquivo usada para nomear os arquivos de saída, por exemplo, `.csv`, `.txt`. Ele deve ser especificado quando o `fileName` não é especificado no conjunto de DelimitedText de saída. | Sim quando o nome do arquivo não é especificado no conjunto de resultados de saída |

## <a name="mapping-data-flow-properties"></a>Mapeando Propriedades de fluxo de dados

Aprenda detalhes da [transformação de origem](data-flow-source.md) e da [transformação de coletor](data-flow-sink.md) no fluxo de dados de mapeamento.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Mapeando fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
