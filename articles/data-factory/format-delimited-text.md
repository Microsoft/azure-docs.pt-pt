---
title: Formato de texto delimitado na Fábrica de Dados Azure
description: Este tópico descreve como lidar com o formato de texto delimitado na Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: jingwang
ms.openlocfilehash: 696c175f55ecdb09b1b88f9766c4c678afe2c4ec
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417095"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Formato de texto delimitado na Fábrica de Dados Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Siga este artigo quando pretender analisar os ficheiros de **texto delimitados ou escrever os dados em formato de texto delimitado**. 

O formato de texto delimitado é suportado para os seguintes conectores: [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage,](connector-azure-file-storage.md) [File System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados de texto delimitado.

| Propriedade         | Descrição                                                  | Necessário |
| ---------------- | ------------------------------------------------------------ | -------- |
| tipo             | A propriedade do tipo do conjunto de dados deve ser definida para **DelimitedText**. | Sim      |
| localização         | Definições de localização dos ficheiros. Cada conector baseado em ficheiros tem `location`o seu próprio tipo de localização e propriedades suportadas em .  | Sim      |
| columnDelimiter  | O(s) caracteres utilizados para separar colunas num ficheiro. <br>O valor padrão é **vírina. `,` ** Quando o delimitador da coluna é definido como corda vazia, o que significa que não há delimitador, toda a linha é tomada como uma única coluna.<br>Atualmente, o delimitador de colunas como corda vazia ou multi-char é suportado apenas para mapear o fluxo de dados, mas não a atividade de Cópia.  | Não       |
| rowDelimiter     | O único personagem ou "\r\n" usado para separar linhas num ficheiro. <br>O valor predefinido é qualquer um dos seguintes valores **na leitura: ["\r\n", "\r", "\n"]** e **"\n" ou "\r\n" na escrita,** mapeando o fluxo de dados e a atividade de Cópia, respectivamente. <br>Quando o delimitador da linha não for definido para delimitador (corda vazia), o delimitador da coluna deve ser definido como no delimitador (corda vazia), o que significa tratar todo o conteúdo como um único valor.<br>Atualmente, o delimitador de linha como corda vazia é suportado apenas para mapear fluxo de dados, mas não para copiar atividade. | Não       |
| quoteChar        | O único carácter para citar valores de coluna se contiver delimitador de colunas. <br>O valor predefinido é de duas `"` **cotações.** <br>Para mapear o `quoteChar` fluxo de dados, não pode ser uma corda vazia. <br>Para a atividade `quoteChar` de Cópia, quando é definida como cadeia vazia, significa que `escapeChar` não há citação de carvão e o valor da coluna não é citado, e é usado para escapar ao delimitador da coluna e a si mesmo. | Não       |
| escapeChar       | O único personagem para escapar citações dentro de um valor citado.<br>O valor predefinido é **backslash `\` **. <br>Para mapear o `escapeChar` fluxo de dados, não pode ser uma corda vazia. <br/>Para a atividade `escapeChar` da Cópia, quando `quoteChar` é definida como corda vazia, a corda deve ser definida como corda vazia também, caso em que certifique-se de que todos os valores da coluna não contêm delimitadores. | Não       |
| firstRowAsHeader | Especifica se deve tratar/fazer a primeira linha como uma linha de cabeçalho com nomes de colunas.<br>Os valores permitidos são **verdadeiros** e **falsos** (padrão). | Não       |
| nullValue        | Especifica a representação de cadeia de valor nulo. <br>O valor predefinido é **a cadeia vazia.** | Não       |
| encodingName     | O tipo de codificação utilizado para ler/escrever ficheiros de teste. <br>Os valores permitidos são os seguintes: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM868", "IBM86 0", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "IBM01149", "ISO-2022-JP", "ISO-2022-JP", "ISO-2022-JP", "ISO-2022-JP", "ISO-2022-JP", "ISO-2022-JP", "ISO-2022-JP", "ISO-2022-JP", "ISO-2022-JP", "ISO-2022-JP", "ISO-2022-JP", "ISO-2022-JP", "ISO-2022-JP", "ISO-2022-JP", "ISO-2022-JP", "ISO-2022-JP", "ISO-2022-JP", "ISO-2022-JP", "ISO-2022-JP", "ISO-2022-JP", "ISO-2022-JP", "ISO-2 8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-8", "ISO-8859-9", "ISO-8859-9", "ISO-8859-9", "ISO-8859-8", "ISO-8859-8", "ISO-8859-9", "ISO-8859-9", "ISO-8859-9", "ISO-8859-9", "ISO-8859-9", "ISo-8859-8", "ISo-8859-9", "ISO-8859-9", "ISO-8859-9", "ISO-8859-9", "ISO-8859-9", "ISO-8859-9", "ISO-8859-9", "ISO-8859-9", "ISO-8859-9", "ISO-8859 , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".<br>Nota o fluxo de dados de mapeamento de nota não suporta codificação UTF-7. | Não       |
| compressãoCodec | O codec de compressão utilizado para ler/escrever ficheiros de texto. <br>Os valores permitidos são **bzip2,** **gzip,** **desinsuflação,** **ZipDeflate,** **snappy,** ou **lz4**. O padrão não é comprimido. <br>**Nota** atualmente A atividade da Cópia não suporta "snappy" & "lz4", e o fluxo de dados de mapeamento não suporta "ZipDeflate". <br>**Note** ao utilizar a atividade de cópia para descomprimir ficheiros ZipDeflate e escrever para `<path specified in dataset>/<folder named as source zip file>/`a loja de dados do lavatório baseado em ficheiros, os ficheiros serão extraídos para a pasta: . | Não       |
| compressãoN | A relação de compressão. <br>Os valores permitidos são **Ideais** ou **Mais Rápidos.**<br>- **Mais rápido:** A operação de compressão deve ser concluída o mais rapidamente possível, mesmo que o ficheiro resultante não esteja devidamente comprimido.<br>- **Ótimo:** O funcionamento da compressão deve ser optimizadamente comprimido, mesmo que a operação leve mais tempo a concluir. Para mais informações, consulte o tópico [do Nível de Compressão.](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) | Não       |

Abaixo está um exemplo de conjunto de dados de texto delimitado no Armazenamento de Blob Azure:

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
            "escapeChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte de texto delimitada e pia.

### <a name="delimited-text-as-source"></a>Texto delimitado como fonte 

As seguintes propriedades são suportadas na secção de *** \*origem\* *** da atividade de cópia.

| Propriedade       | Descrição                                                  | Necessário |
| -------------- | ------------------------------------------------------------ | -------- |
| tipo           | A propriedade do tipo da fonte de atividade de cópia deve ser definida para **DelimitedTextSource**. | Sim      |
| formatoDefinições | Um grupo de propriedades. Consulte a tabela de definições de leitura de **texto delimitada** abaixo. | Não       |
| lojasDefinições  | Um grupo de propriedades sobre como ler dados de uma loja de dados. Cada conector baseado em ficheiros `storeSettings`tem as suas próprias definições de leitura suportadas em . . | Não       |

Definições de leitura de `formatSettings`texto **delimitada** suportada em:

| Propriedade      | Descrição                                                  | Necessário |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | O tipo de formatoAs definições devem ser definidas para **Definições deLeitura deTexto delimitadas**. | Sim      |
| skipLineCount | Indica o número de linhas **não vazias** a saltar ao ler os dados dos ficheiros de entrada. <br>Se as propriedades skipLineCount e firstRowAsHeader forem especificadas simultaneamente, as linhas são ignoradas primeiro e, em seguida, as informações de cabeçalho são lidas a partir do ficheiro de entrada. | Não       |

### <a name="delimited-text-as-sink"></a>Texto delimitado como pia

As seguintes propriedades são suportadas na secção de *** \*sumidouro\* *** da atividade de cópia.

| Propriedade       | Descrição                                                  | Necessário |
| -------------- | ------------------------------------------------------------ | -------- |
| tipo           | A propriedade do tipo da fonte de atividade de cópia deve ser definida para **DelimitedTextSink**. | Sim      |
| formatoDefinições | Um grupo de propriedades. Consulte a tabela de definições de escrita de **texto delimitada** abaixo. |          |
| lojasDefinições  | Um grupo de propriedades sobre como escrever dados para uma loja de dados. Cada conector baseado em ficheiros `storeSettings`tem as suas próprias definições de escrita suportadas em baixo .  | Não       |

Definições de escrita de `formatSettings`texto **limitadasuportadas** em:

| Propriedade      | Descrição                                                  | Necessário                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| tipo          | O tipo de formatoDefinições devem ser definidas para **Definições Delimitadas de TextoWrite .** | Sim                                                   |
| arquivoExtensão | A extensão do ficheiro utilizada para nomear `.csv` `.txt`os ficheiros de saída, por exemplo, . Deve ser especificado `fileName` quando o conjunto de dados DelimitedText não está especificado na saída. Quando o nome do ficheiro estiver configurado no conjunto de dados de saída, será utilizado como o nome do ficheiro da pia e a definição de extensão do ficheiro será ignorada.  | Sim, quando o nome do ficheiro não é especificado no conjunto de dados de saída |

## <a name="mapping-data-flow-properties"></a>Mapeando propriedades de fluxo de dados

Aprenda detalhes da transformação de [fontes](data-flow-source.md) e [transformação](data-flow-sink.md) de afundar no fluxo de dados de mapeamento.

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Mapeando o fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Obtenha atividade de Metadados](control-flow-get-metadata-activity.md)
