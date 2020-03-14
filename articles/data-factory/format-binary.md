---
title: Formato binário na Fábrica de Dados Azure
description: Este tópico descreve como lidar com o formato Binário na Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 8ebb4f0d1a06a7bf29dc46cd696b6acfd2527095
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260699"
---
# <a name="binary-format-in-azure-data-factory"></a>Formato binário na Fábrica de Dados Azure

O formato binário é suportado para os seguintes conectores: [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage,](connector-azure-file-storage.md) [File System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)e [SFTP](connector-sftp.md).

Pode utilizar o conjunto de dados binário na [atividade da Cópia,](copy-activity-overview.md) [a atividade do GetMetadata,](control-flow-get-metadata-activity.md)ou [eliminar a atividade](delete-activity.md). Ao utilizar o conjunto de dados binário, a ADF não analisa o conteúdo dos ficheiros, mas trata-o como está. 

>[!NOTE]
>Ao utilizar o conjunto de dados binário na atividade da cópia, só é possível copiar do conjunto de dados binário para o conjunto de dados binário.

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados binário.

| Propriedade         | Descrição                                                  | Required |
| ---------------- | ------------------------------------------------------------ | -------- |
| tipo             | A propriedade tipo do conjunto de dados deve ser definida para **Binary**. | Sim      |
| localização         | Definições de localização dos ficheiros. Cada conector baseado em ficheiros tem o seu próprio tipo de localização e propriedades suportadas em `location`. **Consulte detalhes no artigo do conector -> Secção**de propriedades do conjunto de dados . | Sim      |
| compression | Grupo de propriedades para configurar a compressão de ficheiros. Configure esta secção quando pretender fazer compressão/descompressão durante a execução da atividade. | Não |
| tipo | O código de compressão usado para ler/escrever ficheiros binários. <br>Os valores permitidos são **bzip2,** **gzip,** **esvaziar,** **ZipDeflate**. para usar ao guardar o ficheiro.<br>Note ao utilizar a atividade de cópia para descomprimir ficheiros ZipDeflate e escrever para uma loja de dados de sumidouro baseada em ficheiros, os ficheiros serão extraídos para a pasta: `<path specified in dataset>/<folder named as source zip file>/`. | Não       |
| nível | A relação de compressão. Aplicar quando o conjunto de dados for utilizado no afundatório de atividade da Cópia.<br>Os valores permitidos são **Ideais** ou **Mais Rápidos.**<br>- **Mais rápido:** A operação de compressão deve estar concluída o mais rapidamente possível, mesmo que o ficheiro resultante não esteja devidamente comprimido.<br>- **Optimal**: O funcionamento da compressão deve ser optimizadamente comprimido, mesmo que a operação leve mais tempo a concluir. Para mais informações, consulte o tópico [do Nível de Compressão.](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) | Não       |

Abaixo está um exemplo de conjunto de dados binário no armazenamento de Blob Azure:

```json
{
    "name": "BinaryDataset",
    "properties": {
        "type": "Binary",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte binária e pia.

>[!NOTE]
>Ao utilizar o conjunto de dados binário na atividade da cópia, só é possível copiar do conjunto de dados binário para o conjunto de dados binário.

### <a name="binary-as-source"></a>Binário como fonte

As seguintes propriedades são suportadas na atividade de cópia ***\*secção\*fonte.***

| Propriedade      | Descrição                                                  | Required |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade do tipo da fonte de atividade de cópia deve ser definida para **BinarySource**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como ler dados de uma loja de dados. Cada conector baseado em ficheiros tem as suas próprias definições de leitura suportadas em `storeSettings`. **Consulte os detalhes no artigo do conector -> Secção de propriedades**de atividadede cópia . | Não       |

### <a name="binary-as-sink"></a>Binário como pia

As seguintes propriedades são suportadas na atividade de cópia ***\*pia\**** secção.

| Propriedade      | Descrição                                                  | Required |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade do tipo da fonte de atividade de cópia deve ser definida para **BinarySink**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como escrever dados para uma loja de dados. Cada conector baseado em ficheiros tem as suas próprias definições de escrita suportadas em `storeSettings`. **Consulte os detalhes no artigo do conector -> Secção de propriedades**de atividadede cópia . | Não       |

## <a name="next-steps"></a>Passos Seguintes

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Obtenha atividade de Metadados](control-flow-get-metadata-activity.md)
- [Eliminar atividade](delete-activity.md)
