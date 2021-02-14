---
title: Formato binário na Azure Data Factory
description: Este tópico descreve como lidar com o formato binário na Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: cc5b54e99584b74b287fa66deba1694419b46b16
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393688"
---
# <a name="binary-format-in-azure-data-factory"></a>Formato binário na Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O formato binário é suportado para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), Azure File Storage Gen2 , [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)e [SFTP](connector-sftp.md).

Pode utilizar o conjunto de dados binário na [atividade Copy](copy-activity-overview.md), [GetMetadata](control-flow-get-metadata-activity.md)ou [eliminar a atividade](delete-activity.md). Ao utilizar o conjunto de dados binário, a ADF não analisa o conteúdo dos ficheiros, mas trata-o como está. 

>[!NOTE]
>Ao utilizar o conjunto de dados binário na atividade de cópia, só pode copiar do conjunto de dados binário para o conjunto de dados binários.

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Datasets.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados binários.

| Propriedade         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| tipo             | A propriedade do tipo do conjunto de dados deve ser definida como **Binary**. | Yes      |
| localização         | Definições de localização do(s) ficheiros. Cada conector baseado em ficheiros tem o seu próprio tipo de localização e propriedades suportadas em `location` . **Consulte os detalhes na secção de propriedades do conector -> Dataset**. | Yes      |
| compressão | Grupo de propriedades para configurar a compressão do ficheiro. Configure esta secção quando pretender fazer compressão/descompressão durante a execução da atividade. | No |
| tipo | O códice de compressão usado para ler/escrever ficheiros binários. <br>Os valores permitidos são **bzip2**, **gzip,** **deflate,** **ZipDeflate,** **Tar,** ou **TarGzip**. <br>**Nota** quando utilizar a atividade de cópia para **descomprimir os** / ficheiros **TarGzip TarGzip** /  e escrever para a loja de dados da pia baseada em ficheiros, por predefinição os ficheiros são extraídos para a pasta: , `<path specified in dataset>/<folder named as source compressed file>/` utilize na fonte de atividade de `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` [cópia](#binary-as-source) para controlar se deve preservar o nome dos ficheiros(s) comprimidos como estrutura de pasta.| No       |
| nível | A relação de compressão. Aplicar quando o conjunto de dados for utilizado na pia de atividade do Copy.<br>Os valores permitidos são **ótimos** ou **mais rápidos.**<br>- **Mais rápido:** O funcionamento da compressão deve ser concluído o mais rapidamente possível, mesmo que o ficheiro resultante não seja perfeitamente comprimido.<br>- **Ótimo**: O funcionamento da compressão deve ser perfeitamente comprimido, mesmo que a operação leve mais tempo a ser concluída. Para mais informações, consulte o tópico [nível de compressão.](/dotnet/api/system.io.compression.compressionlevel) | No       |

Abaixo está um exemplo do conjunto de dados binário no Armazenamento Azure Blob:

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

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte binária e pia.

>[!NOTE]
>Ao utilizar o conjunto de dados binário na atividade de cópia, só pode copiar do conjunto de dados binário para o conjunto de dados binários.

### <a name="binary-as-source"></a>Binário como fonte

As seguintes propriedades são suportadas na secção ***\* de origem \**** da atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade tipo da fonte de atividade de cópia deve ser definida como **BinarySource**. | Yes      |
| formatoStas | Um grupo de propriedades. Consulte a tabela **de definições de leitura binária** abaixo. | No       |
| lojaSs | Um grupo de propriedades sobre como ler dados de uma loja de dados. Cada conector baseado em ficheiros tem as suas próprias definições de leitura suportadas em `storeSettings` . **Consulte os detalhes na secção de propriedades de atividade do conector -> Copy**. | No       |

Definições **de leitura binária** suportadas em `formatSettings` :

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | O tipo de formatoSettings deve ser definido para **BinaryReadSettings**. | Yes      |
| compressãoProperties | Um grupo de propriedades sobre como descomprimir dados para um determinado codec de compressão. | No       |
| preservarZipFileNameAsFolder<br>*(em `compressionProperties` -> `type` `ZipDeflateReadSettings` conforme)* | Aplica-se quando o conjunto de dados de entrada é configurado com compressão **ZipDeflate.** Indica se deve preservar o nome do ficheiro zip de origem como estrutura de pasta durante a cópia.<br>- Quando definido como **verdadeiro (predefinido)**, a Data Factory escreve ficheiros desapertados para `<path specified in dataset>/<folder named as source zip file>/` .<br>- Quando definidos como **falsos,** a Data Factory escreve ficheiros desapertados diretamente para `<path specified in dataset>` . Certifique-se de que não tem nomes de ficheiros duplicados em diferentes ficheiros zip de origem para evitar corridas ou comportamentos inesperados.  | No |
| preservar CompressãoFileNameAsFolder<br>(em *`compressionProperties` -> `type` ou `TarGZipReadSettings` `TarReadSettings` menos)* | Aplica-se quando o conjunto de dados de entrada é configurado com a compressão  / **tarGzip.** Indica se deve preservar o nome do ficheiro comprimido de origem como estrutura de pasta durante a cópia.<br>- Quando definido como **verdadeiro (predefinido)**, a Data Factory escreve ficheiros descomprimidos para `<path specified in dataset>/<folder named as source compressed file>/` . <br>- Quando definido como **falso,** a Data Factory escreve ficheiros descomprimidos diretamente para `<path specified in dataset>` . Certifique-se de que não tem nomes de ficheiros duplicados em diferentes ficheiros de origem para evitar corridas ou comportamentos inesperados. | No |

```json
"activities": [
    {
        "name": "CopyFromBinary",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true,
                    "deleteFilesAfterCompletion": true
                },
                "formatSettings": {
                    "type": "BinaryReadSettings",
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

### <a name="binary-as-sink"></a>Binário como pia

As seguintes propriedades são suportadas na secção de ***\* lavatório \**** de atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| tipo          | A propriedade do tipo da fonte de atividade de cópia deve ser definida como **BinarySink**. | Yes      |
| lojaSs | Um grupo de propriedades sobre como escrever dados para uma loja de dados. Cada conector baseado em ficheiros tem as suas próprias definições de escrita suportadas em `storeSettings` . **Consulte os detalhes na secção de propriedades de atividade do conector -> Copy**. | Não       |

## <a name="next-steps"></a>Próximos passos

- [Visão geral da atividade da cópia](copy-activity-overview.md)
- [Atividade getMetadata](control-flow-get-metadata-activity.md)
- [Eliminar atividade](delete-activity.md)