---
title: Excel formato na Azure Data Factory
description: Este tópico descreve como lidar com o formato Excel na Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: b1f95cf0a62aa68fe86f37cea137251553458a1d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348884"
---
# <a name="excel-format-in-azure-data-factory"></a>Excel formato na Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Siga este artigo quando quiser **analisar os ficheiros Excel**. A Azure Data Factory suporta ".xls" e ".xlsx".

O formato Excel é suportado para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), Azure File Storage Gen2 , [Azure File Storage](connector-azure-file-storage.md), FILE [System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS](connector-hdfs.md), [HTTP](connector-http.md)e [SFTP](connector-sftp.md). É suportado como fonte, mas não afundado. 

**Nota:** O formato ".xls" não é suportado durante a utilização [http](connector-http.md). 

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados Excel.

| Propriedade         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| tipo             | A propriedade tipo do conjunto de dados deve ser definida para **Excel**.   | Sim      |
| localização         | Definições de localização do(s) ficheiros. Cada conector baseado em ficheiros tem o seu próprio tipo de localização e propriedades suportadas em `location` . | Sim      |
| nome de folhaName        | O nome da folha de cálculo do Excel para ler dados.                       | Sim      |
| gama            | A gama de células na folha de cálculo dada para localizar os dados seletivos, por exemplo:<br>- Não especificado: lê toda a folha de cálculo como uma tabela da primeira linha e coluna não vazias<br>- `A3`: lê uma tabela a partir da célula dada, deteta dinamicamente todas as linhas abaixo e todas as colunas à direita<br>- `A3:H5`: lê esta gama fixa como uma tabela<br>- `A3:A3`: lê esta única célula | Não       |
| firstRowAsHeader | Especifica se deve tratar a primeira linha na folha/intervalo dada como uma linha de cabeçalho com nomes de colunas.<br>Os valores permitidos são **verdadeiros** e **falsos** (padrão). | Não       |
| nullValue        | Especifica a representação de cadeia de valor nulo. <br>O valor predefinido é **a corda vazia.** | Não       |
| compressão | Grupo de propriedades para configurar a compressão do ficheiro. Configure esta secção quando pretender fazer compressão/descompressão durante a execução da atividade. | Não |
| tipo<br/>*(em) `compression`* | O códice de compressão usado para ler/escrever ficheiros JSON. <br>Os valores permitidos são **bzip2**, **gzip,** **deflate,** **ZipDeflate,** **TarGzip,** **Tar,** **snappy,** ou **lz4**. O padrão não é comprimido.<br>**Nota** atualmente A atividade copy não suporta "snappy" & "lz4", e o fluxo de dados de mapeamento não suporta "ZipDeflate", "TarGzip" e "Tar".<br>**Note** que ao utilizar a atividade de cópia para descomprimir ficheiros **ZipDeflate** e escrever para a loja de dados da pia baseada em ficheiros, os ficheiros são extraídos para a pasta: `<path specified in dataset>/<folder named as source zip file>/` . | Não.  |
| nível<br/>*(em) `compression`* | A relação de compressão. <br>Os valores permitidos são **ótimos** ou **mais rápidos.**<br>- **Mais rápido:** O funcionamento da compressão deve ser concluído o mais rapidamente possível, mesmo que o ficheiro resultante não seja perfeitamente comprimido.<br>- **Ótimo**: O funcionamento da compressão deve ser perfeitamente comprimido, mesmo que a operação leve mais tempo a ser concluída. Para mais informações, consulte o tópico [nível de compressão.](/dotnet/api/system.io.compression.compressionlevel) | Não       |

Abaixo está um exemplo do conjunto de dados do Excel no Azure Blob Storage:

```json
{
    "name": "ExcelDataset",
    "properties": {
        "type": "Excel",
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
            "sheetName": "MyWorksheet",
            "range": "A3:H5",
            "firstRowAsHeader": true
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte Excel.

### <a name="excel-as-source"></a>Excel como fonte 

As seguintes propriedades são suportadas na atividade de cópia **_ \_ secção \* fonte*** .

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade tipo da fonte de atividade de cópia deve ser definida para **ExcelSource**. | Sim      |
| lojaSs | Um grupo de propriedades sobre como ler dados de uma loja de dados. Cada conector baseado em ficheiros tem as suas próprias definições de leitura suportadas em `storeSettings` . | Não       |

```json
"activities": [
    {
        "name": "CopyFromExcel",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "ExcelSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            ...
        }
        ...
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

No mapeamento dos fluxos de dados, pode ler o formato Excel nas seguintes lojas de dados: [Azure Blob Storage,](connector-azure-blob-storage.md#mapping-data-flow-properties) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)e [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties). Pode apontar para ficheiros do Excel utilizando o conjunto de dados do Excel ou utilizando um [conjunto de dados inline](data-flow-source.md#inline-datasets).

### <a name="source-properties"></a>Propriedades de origem

A tabela abaixo lista as propriedades suportadas por uma fonte do Excel. Pode editar estas propriedades no separador **Opções Fonte.** Ao utilizar o conjunto de dados inline, verá definições de ficheiros adicionais, que são as mesmas que as propriedades descritas na secção [de propriedades do conjunto de dados.](#dataset-properties)

| Nome                      | Descrição                                                  | Obrigatório | Valores permitidos                                            | Propriedade de script de fluxo de dados         |
| ------------------------- | ------------------------------------------------------------ | -------- | --------------------------------------------------------- | --------------------------------- |
| Caminhos de wild card           | Todos os ficheiros correspondentes ao caminho wildcard serão processados. Substitui a pasta e o caminho do ficheiro definido no conjunto de dados. | não       | Corda[]                                                  | wildcardPaths                     |
| Caminho da raiz da partição       | Para os dados de ficheiros que são divididos, pode introduzir um caminho de raiz de partição para ler pastas partidas como colunas | não       | String                                                    | partitionRootPath                 |
| Lista de ficheiros             | Se a sua fonte está a apontar para um ficheiro de texto que lista ficheiros para processar | não       | `true` ou `false`                                         | fileList                          |
| Coluna para armazenar nome de ficheiro | Criar uma nova coluna com o nome e caminho do ficheiro de origem       | não       | String                                                    | rowUrlColumn                      |
| Após a conclusão          | Elimine ou mova os ficheiros após o processamento. O caminho do arquivo começa a partir da raiz do recipiente | não       | Excluir: `true` ou `false` <br> Mover-se: `['<from>', '<to>']` | purgeFiles <br> moveFiles         |
| Filtrar por última modificação   | Opte por filtrar ficheiros com base na última alteração que foram alterados | não       | Timestamp                                                 | modificado Depois <br> modificadoSForo antes |
| Não permita que não encontrem ficheiros | Se for verdade, um erro não é jogado se nenhum ficheiro for encontrado | não | `true` ou `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Exemplo de origem

A imagem abaixo é um exemplo de uma configuração de fonte excel no mapeamento de fluxos de dados usando o modo dataset.

![Fonte excel](media/data-flow/excel-source.png)

O script de fluxo de dados associado é:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    wildcardPaths:['*.xls']) ~> ExcelSource
```

Se utilizar o conjunto de dados inline, vê as seguintes opções de origem no fluxo de dados de mapeamento.

![Conjunto de dados inline de fonte excel](media/data-flow/excel-source-inline-dataset.png)

O script de fluxo de dados associado é:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'excel',
    fileSystem: 'container',
    folderPath: 'path',
    fileName: 'sample.xls',
    sheetName: 'worksheet',
    firstRowAsHeader: true) ~> ExcelSourceInlineDataset
```

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da atividade da cópia](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Atividade getMetadata](control-flow-get-metadata-activity.md)