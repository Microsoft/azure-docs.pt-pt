---
title: Formato de textolimtado na Azure Data Factory
description: Este tópico descreve como lidar com o formato de texto delimitado na Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: jingwang
ms.openlocfilehash: 1cff49e4ddb7423e1e5956e1436cefd18abe6dfe
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107079"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Formato de textolimtado na Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Siga este artigo quando pretender **analisar os ficheiros de texto delimitados ou escrever os dados em formato de texto delimitado**. 

O formato de textolimtado é suportado para os seguintes conectores: [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), Azure File Storage Gen2 , [Azure File Storage,](connector-azure-file-storage.md) [File System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados de texto delimitado.

| Propriedade         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| tipo             | A propriedade do tipo do conjunto de dados deve ser definida como **DelimitedText**. | Yes      |
| localização         | Definições de localização do(s) ficheiros. Cada conector baseado em ficheiros tem o seu próprio tipo de localização e propriedades suportadas em `location` .  | Yes      |
| columnDelimiter  | O(s) caracterisse usado para separar colunas num ficheiro. <br>O valor predefinido é **vírgula. `,`** Quando o delimitador da coluna é definido como corda vazia, o que significa que não há delimiter, toda a linha é tomada como uma única coluna.<br>Atualmente, o delimiter de coluna como string vazio ou multi-char é suportado apenas para mapeamento de fluxo de dados, mas não atividade de Cópia.  | No       |
| rowDelimiter     | O único carácter ou "\r\n" usado para separar linhas num ficheiro. <br>O valor predefinido é qualquer um dos seguintes valores **na leitura: ["\r\n", "\r", "\n"]** e **"\n" ou "\r\n" na escrita** por mapeamento do fluxo de dados e da atividade copy, respectivamente. <br>Quando o delimiter de linha não estiver definido para nenhum delimiter (corda vazia), olimiter da coluna deve ser definido como nenhum delimiter (cadeia vazia) também, o que significa tratar todo o conteúdo como um único valor.<br>Atualmente, o delimiter de linha como string vazio é suportado apenas para mapeamento de fluxo de dados, mas não atividade de Cópia. | No       |
| quoteChar        | O único carácter para citar valores de coluna se contiver delimiter de coluna. <br>O valor predefinido são **cotações duplas.** `"` <br>Quando `quoteChar` é definido como corda vazia, significa que não há citação char e valor da coluna não é citado, e é usado para escapar do `escapeChar` delimiter da coluna e de si mesmo. | No       |
| escapeChar       | O único personagem a escapar citações dentro de um valor citado.<br>O valor **predefinido `\`** é retrocesso. <br>Quando `escapeChar` for definido como corda vazia, o deve ser definido como corda vazia `quoteChar` também, e nesse caso certifique-se de que todos os valores da coluna não contêm delimiters. | No       |
| firstRowAsHeader | Especifica se deve tratar/fazer a primeira linha como uma linha de cabeçalho com nomes de colunas.<br>Os valores permitidos são **verdadeiros** e **falsos** (padrão).<br>Quando a primeira linha como cabeçalho é falsa, note que a pré-visualização de dados de UI e a produção automática de atividade geram nomes de colunas como Prop_{n} (a partir de 0), a atividade da cópia requer [mapeamento explícito](copy-activity-schema-and-type-mapping.md#explicit-mapping) de origem para afundar e localizar colunas por ordinal (a partir de 1) e mapear listas de fluxos de dados e localizar colunas com nome como Column_{n} (a partir de 1).  | No       |
| nullValue        | Especifica a representação de cadeia de valor nulo. <br>O valor predefinido é **a corda vazia.** | No       |
| encodingName     | O tipo de codificação utilizado para ler/escrever ficheiros de teste. <br>Allowed values are as follows: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254",.<br>O fluxo de dados de mapeamento de notas não suporta codificação UTF-7. | No       |
| compressãoCodec | O códice de compressão usado para ler/escrever ficheiros de texto. <br>Os valores permitidos são **bzip2**, **gzip,** **deflate,** **ZipDeflate,** **TarGzip,** **Tar,** **snappy,** ou **lz4**. O padrão não é comprimido. <br>**Nota** atualmente A atividade copy não suporta "snappy" & "lz4", e o fluxo de dados de mapeamento não suporta "ZipDeflate", "TarGzip" e "Tar". <br>**Nota** quando utilizar a atividade de cópia para **descomprimir os** / ficheiros **TarGzip TarGzip** /  e escrever para a loja de dados da pia baseada em ficheiros, por predefinição os ficheiros são extraídos para a pasta: , `<path specified in dataset>/<folder named as source compressed file>/` utilize na fonte de atividade de `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` [cópia](#delimited-text-as-source) para controlar se deve preservar o nome dos ficheiros(s) comprimidos como estrutura de pasta. | No       |
| compressãoLevel | A relação de compressão. <br>Os valores permitidos são **ótimos** ou **mais rápidos.**<br>- **Mais rápido:** O funcionamento da compressão deve ser concluído o mais rapidamente possível, mesmo que o ficheiro resultante não seja perfeitamente comprimido.<br>- **Ótimo**: O funcionamento da compressão deve ser perfeitamente comprimido, mesmo que a operação leve mais tempo a ser concluída. Para mais informações, consulte o tópico [nível de compressão.](/dotnet/api/system.io.compression.compressionlevel) | No       |

Abaixo está um exemplo de conjunto de dados de texto delimitado no armazenamento Azure Blob:

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

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte de texto delimitada e pia.

### <a name="delimited-text-as-source"></a>Texto delimitado como fonte 

As seguintes propriedades são suportadas na secção ***\* de origem \**** da atividade de cópia.

| Propriedade       | Descrição                                                  | Obrigatório |
| -------------- | ------------------------------------------------------------ | -------- |
| tipo           | A propriedade tipo da fonte de atividade de cópia deve ser definida como **DelimitedTextSource**. | Yes      |
| formatoStas | Um grupo de propriedades. Consulte a tabela **de definições de texto delimitada** abaixo. |  No       |
| lojaSs  | Um grupo de propriedades sobre como ler dados de uma loja de dados. Cada conector baseado em ficheiros tem as suas próprias definições de leitura suportadas em `storeSettings` . | No       |

Definições **de leitura de texto delimited suportadas** em `formatSettings` :

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | O tipo de formatoStas devem ser definidas para **DelimitedTextReadSettings**. | Yes      |
| skipLineCount | Indica o número de linhas **não vazias** a saltar ao ler dados a partir de ficheiros de entrada. <br>Se as propriedades skipLineCount e firstRowAsHeader forem especificadas simultaneamente, as linhas são ignoradas primeiro e, em seguida, as informações de cabeçalho são lidas a partir do ficheiro de entrada. | No       |
| compressãoProperties | Um grupo de propriedades sobre como descomprimir dados para um determinado codec de compressão. | No       |
| preservarZipFileNameAsFolder<br>*(em `compressionProperties` -> `type` `ZipDeflateReadSettings` conforme)* |  Aplica-se quando o conjunto de dados de entrada é configurado com compressão **ZipDeflate.** Indica se deve preservar o nome do ficheiro zip de origem como estrutura de pasta durante a cópia.<br>- Quando definido como **verdadeiro (predefinido)**, a Data Factory escreve ficheiros desapertados para `<path specified in dataset>/<folder named as source zip file>/` .<br>- Quando definidos como **falsos,** a Data Factory escreve ficheiros desapertados diretamente para `<path specified in dataset>` . Certifique-se de que não tem nomes de ficheiros duplicados em diferentes ficheiros zip de origem para evitar corridas ou comportamentos inesperados.  | No |
| preservar CompressãoFileNameAsFolder<br>(em *`compressionProperties` -> `type` ou `TarGZipReadSettings` `TarReadSettings` menos)*  | Aplica-se quando o conjunto de dados de entrada é configurado com a compressão  / **tarGzip.** Indica se deve preservar o nome do ficheiro comprimido de origem como estrutura de pasta durante a cópia.<br>- Quando definido como **verdadeiro (predefinido)**, a Data Factory escreve ficheiros descomprimidos para `<path specified in dataset>/<folder named as source compressed file>/` . <br>- Quando definido como **falso,** a Data Factory escreve ficheiros descomprimidos diretamente para `<path specified in dataset>` . Certifique-se de que não tem nomes de ficheiros duplicados em diferentes ficheiros de origem para evitar corridas ou comportamentos inesperados. | No |

```json
"activities": [
    {
        "name": "CopyFromDelimitedText",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                },
                "formatSettings": {
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 3,
                    "compressionProperties": {
                        "type": "ZipDeflateReadSettings",
                        "preserveZipFileNameAsFolder": false
                    }
                }
            },
            ...
        }
        ...
    }
]
```

### <a name="delimited-text-as-sink"></a>Texto delimitado como pia

As seguintes propriedades são suportadas na secção de ***\* lavatório \**** de atividade de cópia.

| Propriedade       | Descrição                                                  | Obrigatório |
| -------------- | ------------------------------------------------------------ | -------- |
| tipo           | A propriedade do tipo da fonte de atividade de cópia deve ser definida como **DelimitedTextSink**. | Yes      |
| formatoStas | Um grupo de propriedades. Consulte a tabela **de definições de texto delimitada** abaixo. |    No      |
| lojaSs  | Um grupo de propriedades sobre como escrever dados para uma loja de dados. Cada conector baseado em ficheiros tem as suas próprias definições de escrita suportadas em `storeSettings` .  | No       |

Definições **de escrita de texto delimitadas suportadas** em `formatSettings` :

| Propriedade      | Descrição                                                  | Obrigatório                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| tipo          | O tipo de formatoStas devem ser definidas com **DelimitedTextWriteSettings**. | Yes                                                   |
| arquivoExtensão | A extensão do ficheiro utilizada para nomear os ficheiros de saída, por `.csv` exemplo, `.txt` , . Deve ser especificado quando o `fileName` não é especificado no conjunto de dados DelimitedText de saída. Quando o nome do ficheiro estiver configurado no conjunto de dados de saída, será utilizado como nome do ficheiro da pia e a definição de extensão do ficheiro será ignorada.  | Sim, quando o nome do ficheiro não é especificado no conjunto de dados de saída |
| maxRowsPerFile | Ao escrever dados numa pasta, pode optar por escrever em vários ficheiros e especificar as linhas máximas por ficheiro.  | No |
| fileNamePrefix | Aplicável quando `maxRowsPerFile` é configurado.<br> Especifique o prefixo do nome do ficheiro ao escrever dados em vários ficheiros, resultando neste padrão: `<fileNamePrefix>_00000.<fileExtension>` . Se não for especificado, o prefixo do nome do ficheiro será gerado automaticamente. Esta propriedade não se aplica quando a fonte é loja baseada em ficheiros ou [loja de dados ativada por opção de partição.](copy-activity-performance-features.md)  | No |

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

No mapeamento dos fluxos de dados, pode ler e escrever para o formato de texto delimitado nas seguintes lojas de dados: [Azure Blob Storage,](connector-azure-blob-storage.md#mapping-data-flow-properties) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties), e [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Propriedades de origem

A tabela abaixo lista as propriedades suportadas por uma fonte de texto delimitada. Pode editar estas propriedades no separador **Opções Fonte.**

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Caminhos de wild card | Todos os ficheiros correspondentes ao caminho wildcard serão processados. Substitui a pasta e o caminho do ficheiro definido no conjunto de dados. | não | Corda[] | wildcardPaths |
| Caminho da raiz da partição | Para os dados de ficheiros que são divididos, pode introduzir um caminho de raiz de partição para ler pastas partidas como colunas | não | String | partitionRootPath |
| Lista de ficheiros | Se a sua fonte está a apontar para um ficheiro de texto que lista ficheiros para processar | não | `true` ou `false` | fileList |
| Linhas multiline | O ficheiro de origem contém linhas que abrangem várias linhas. Os valores multiline devem estar em aspas. | não `true` ou `false` | multiLineRow |
| Coluna para armazenar nome de ficheiro | Criar uma nova coluna com o nome e caminho do ficheiro de origem | não | String | rowUrlColumn |
| Após a conclusão | Elimine ou mova os ficheiros após o processamento. O caminho do arquivo começa a partir da raiz do recipiente | não | Excluir: `true` ou `false` <br> Mover-se: `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| Filtrar por última modificação | Opte por filtrar ficheiros com base na última alteração que foram alterados | não | CarimboDeDataEHora | modificado Depois <br> modificadoSForo antes |
| Não permita que não encontrem ficheiros | Se for verdade, um erro não é jogado se nenhum ficheiro for encontrado | não | `true` ou `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Exemplo de origem

A imagem abaixo é um exemplo de uma configuração de origem de texto delimitada nos fluxos de dados de mapeamento.

![Fonte detexto delimited](media/data-flow/delimited-text-source.png)

O script de fluxo de dados associado é:

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    multiLineRow: true,
    wildcardPaths:['*.csv']) ~> CSVSource
```

> [!NOTE]
> Fontes de fluxo de dados suportam um conjunto limitado de globbing Linux que é suporte por sistemas de ficheiros Hadoop

### <a name="sink-properties"></a>Propriedades de pia

A tabela abaixo lista as propriedades suportadas por um lavatório de texto delimitado. Pode editar estas propriedades no **separador Definições.**

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Limpe a pasta | Se a pasta de destino for apurada antes de escrever | não | `true` ou `false` | truncato |
| Opção de nome de ficheiro | O formato de nomeação dos dados escritos. Por predefinição, um ficheiro por partição em formato `part-#####-tid-<guid>` | não | Padrão: Corda <br> Por partição: String[] <br> Ficheiro de nome como dados de coluna: Cadeia <br> Saída para um único ficheiro: `['<fileName>']` <br> Pasta de nome como dados de coluna: String | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames <br> rowFolderUrlColumn |
| Citar tudo | Incluir todos os valores em cotações | não | `true` ou `false` | citaçãoTo |
| Cabeçalho | Adicione cabeçalhos de clientes aos ficheiros de saída | não | `[<string array>]` | cabeçalho |

### <a name="sink-example"></a>Exemplo de pia

A imagem abaixo é um exemplo de uma configuração de sumidouro de texto delimitada nos fluxos de dados de mapeamento.

![Pia detextado delimited](media/data-flow/delimited-text-sink.png)

O script de fluxo de dados associado é:

```
CSVSource sink(allowSchemaDrift: true,
    validateSchema: false,
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CSVSink
```

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da atividade da cópia](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Atividade getMetadata](control-flow-get-metadata-activity.md)
