---
title: Formato Avro na Fábrica de Dados Azure
description: Este tópico descreve como lidar com o formato Avro na Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jingwang
ms.openlocfilehash: 931287fa2a4104069b101236bec9f76bb7193e8d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416357"
---
# <a name="avro-format-in-azure-data-factory"></a>Formato Avro na Fábrica de Dados Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Siga este artigo quando pretender **analisar os ficheiros Avro ou escrever os dados no formato Avro**. 

O formato Avro é suportado para os seguintes conectores: [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage,](connector-azure-file-storage.md) [File System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados Avro.

| Propriedade         | Descrição                                                  | Necessário |
| ---------------- | ------------------------------------------------------------ | -------- |
| tipo             | A propriedade tipo do conjunto de dados deve ser definida para **Avro**. | Sim      |
| localização         | Definições de localização dos ficheiros. Cada conector baseado em ficheiros tem `location`o seu próprio tipo de localização e propriedades suportadas em . Consulte os detalhes na secção de propriedades do conjunto de dados **do conector ->**. | Sim      |
| avroCompressionCodec | O código de compressão para usar ao escrever para ficheiros Avro. Ao ler os ficheiros Avro, a Data Factory determina automaticamente o código de compressão com base nos metadados do ficheiro.<br>Os tipos suportados são "**nenhum**" (padrão), "**esvaziar**",**snappy**". Nota atualmente A atividade da Cópia não suporta snappy ao ler/escrever ficheiros Avro. | Não       |

> [!NOTE]
> O espaço branco no nome da coluna não é suportado para ficheiros Avro.

Abaixo está um exemplo de conjunto de dados Avro no Armazenamento de Blob Azure:

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
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
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte avro e pia.

### <a name="avro-as-source"></a>Avro como fonte

As seguintes propriedades são suportadas na secção de *** \*origem\* *** da atividade de cópia.

| Propriedade      | Descrição                                                  | Necessário |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade do tipo da fonte de atividade de cópia deve ser definida para **AvroSource**. | Sim      |
| lojasDefinições | Um grupo de propriedades sobre como ler dados de uma loja de dados. Cada conector baseado em ficheiros `storeSettings`tem as suas próprias definições de leitura suportadas em . . Consulte os detalhes na secção de propriedades de propriedade do artigo do **conector -> Copiar**. | Não       |

### <a name="avro-as-sink"></a>Avro como pia

As seguintes propriedades são suportadas na secção de *** \*sumidouro\* *** da atividade de cópia.

| Propriedade      | Descrição                                                  | Necessário |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade do tipo da fonte de atividade de cópia deve ser definida para **AvroSink**. | Sim      |
| lojasDefinições | Um grupo de propriedades sobre como escrever dados para uma loja de dados. Cada conector baseado em ficheiros `storeSettings`tem as suas próprias definições de escrita suportadas em baixo . Consulte os detalhes na secção de propriedades de propriedade do artigo do **conector -> Copiar**. | Não       |

## <a name="data-type-support"></a>Suporte de tipo de dados

### <a name="copy-activity"></a>Atividade Copiar
Os tipos de [dados complexos](https://avro.apache.org/docs/current/spec.html#schema_complex) da Avro não são suportados (registos, enums, matrizes, mapas, sindicatos e fixos) na Atividade de Cópia.

### <a name="data-flows"></a>Fluxos de dados
Ao trabalhar com ficheiros Avro em fluxos de dados, pode ler e escrever tipos de dados complexos, mas certifique-se de limpar primeiro o esquema físico do conjunto de dados. Nos fluxos de dados, pode definir a sua projeção lógica e obter colunas que são estruturas complexas, em seguida, mapear automaticamente esses campos para um ficheiro Avro.

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Obtenha atividade de Metadados](control-flow-get-metadata-activity.md)
