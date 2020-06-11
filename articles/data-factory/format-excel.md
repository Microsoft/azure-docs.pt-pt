---
title: Excel formato na Azure Data Factory
description: Este tópico descreve como lidar com o formato Excel na Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: jingwang
ms.openlocfilehash: 8b4876377501209e19ac496d605d228208d2323d
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84670923"
---
# <a name="excel-format-in-azure-data-factory"></a>Excel formato na Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Siga este artigo quando quiser **analisar os ficheiros Excel**. A Azure Data Factory suporta ".xls" e ".xlsx".

O formato Excel é suportado para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), Azure File Storage Gen2 , [Azure File Storage](connector-azure-file-storage.md), FILE [System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS](connector-hdfs.md), [HTTP](connector-http.md)e [SFTP](connector-sftp.md). É suportado como fonte, mas não afundado.

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados Excel.

| Propriedade         | Descrição                                                  | Necessário |
| ---------------- | ------------------------------------------------------------ | -------- |
| tipo             | A propriedade tipo do conjunto de dados deve ser definida para **Excel**.   | Sim      |
| localização         | Definições de localização do(s) ficheiros. Cada conector baseado em ficheiros tem o seu próprio tipo de localização e propriedades suportadas em `location` . | Sim      |
| nome de folhaName        | O nome da folha de cálculo do Excel para ler dados.                       | Sim      |
| gama            | A gama celular na folha de cálculo dada para localizar os dados seletivos, por exemplo `A3:H5` (uma tabela de A3 a H5), `A3` (uma tabela a partir da célula A3), `A3:A3` (única célula). Se não for especificado, a ADF lê a partir de toda a folha de cálculo como uma tabela. | Não       |
| firstRowAsHeader | Especifica se deve tratar a primeira linha na folha/intervalo dada como uma linha de cabeçalho com nomes de colunas.<br>Os valores permitidos são **verdadeiros** e **falsos** (padrão). | Não       |
| nullValue        | Especifica a representação de cadeia de valor nulo. <br>O valor predefinido é **a corda vazia.** | Não       |
| compressãoCodec | O codec de compressão usado para ler ficheiros Excel. <br>Os valores permitidos são **bzip2,** **gzip,** **deflate,** **ZipDeflate,** **snappy,** ou **lz4**. O padrão não é comprimido. <br>**Nota** atualmente A atividade copy não suporta "snappy" & "lz4", e o fluxo de dados de mapeamento não suporta "ZipDeflate". <br>**Note** que ao utilizar a atividade de cópia para descomprimir ficheiros **ZipDeflate** e escrever para a loja de dados da pia baseada em ficheiros, os ficheiros são extraídos para a pasta: `<path specified in dataset>/<folder named as source zip file>/` . | Não       |
| compressãoLevel | A relação de compressão. <br>Os valores permitidos são **ótimos** ou **mais rápidos.**<br>- **Mais rápido:** O funcionamento da compressão deve ser concluído o mais rapidamente possível, mesmo que o ficheiro resultante não seja perfeitamente comprimido.<br>- **Ótimo**: O funcionamento da compressão deve ser perfeitamente comprimido, mesmo que a operação leve mais tempo a ser concluída. Para mais informações, consulte o tópico [nível de compressão.](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) | Não       |

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

As seguintes propriedades são suportadas na secção *** \* de origem \* *** da atividade de cópia.

| Propriedade      | Descrição                                                  | Necessário |
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

## <a name="next-steps"></a>Próximos passos

- [Visão geral da atividade da cópia](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Atividade getMetadata](control-flow-get-metadata-activity.md)
