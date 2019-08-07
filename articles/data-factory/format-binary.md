---
title: Formato binário em Azure Data Factory | Microsoft Docs
description: Este tópico descreve como lidar com o formato binário no Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: a3b3e4e873497b3b0f726d815a798919ace4f931
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68842546"
---
# <a name="binary-format-in-azure-data-factory"></a>Formato binário no Azure Data Factory

O formato binário tem suporte para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [armazenamento de arquivos do Azure](connector-azure-file-storage.md), [sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

Você pode usar o conjunto de um DataSet binário na atividade de [cópia](copy-activity-overview.md), na [atividade GetMetadata](control-flow-get-metadata-activity.md)ou na [atividade de exclusão](delete-activity.md). Ao usar o DataSet binário, o ADF não analisa o conteúdo do arquivo, mas o trata como está. 

>[!NOTE]
>Ao usar o DataSet binário na atividade de cópia, você só pode copiar de um conjunto de um binário para um conjunto de um binário.

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de banco de e binário.

| Propriedade         | Descrição                                                  | Necessário |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | A propriedade Type do conjunto de conjuntos deve ser definida como **Binary**. | Sim      |
| location         | Configurações de local dos arquivos. Cada conector baseado em arquivo tem seu próprio tipo de local e propriedades com `location`suporte em. **Consulte os detalhes no artigo do conector – > seção Propriedades do conjunto de informações**. | Sim      |
| compression | Grupo de propriedades para configurar a compactação de arquivo. Configure esta seção quando desejar fazer compactação/descompactação durante a execução da atividade. | Não |
| type | O codec de compactação usado para ler/gravar arquivos binários. <br>Os valores permitidos são **bzip2**, **gzip**, deflate, **ZipDeflate**. para usar ao salvar o arquivo. | Não       |
| level | A taxa de compactação. Aplicar quando o conjunto de um for usado no coletor da atividade de cópia.<br>Os valores permitidos são **ideal** ou **mais rápido**.<br>- **Mais rápido** A operação de compactação deve ser concluída o mais rápido possível, mesmo que o arquivo resultante não seja compactado de maneira ideal.<br>- **Ideal**: A operação de compactação deve ser corretamente compactada, mesmo se a operação levar mais tempo para ser concluída. Para obter mais informações, consulte [nível de compactação](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) tópico. | Não       |

Veja abaixo um exemplo de conjunto de uma Binary DataSet no armazenamento de BLOBs do Azure:

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

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista das propriedades com suporte pela fonte binária e pelo coletor.

>[!NOTE]
>Ao usar o DataSet binário na atividade de cópia, você só pode copiar de um conjunto de um binário para um conjunto de um binário.

### <a name="binary-as-source"></a>Binário como fonte

As propriedades a seguir têm suporte na seção ***\*origem\**** da atividade de cópia.

| Propriedade      | Descrição                                                  | Necessário |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade Type da fonte da atividade de cópia deve ser definidacomo binaryname. | Sim      |
| storeSettings | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de leitura com `storeSettings`suporte em. **Veja os detalhes no artigo do conector – > seção Propriedades da atividade de cópia**. | Não       |

### <a name="binary-as-sink"></a>Binário como coletor

As propriedades a seguir têm suporte na seção ***\*coletor\**** de atividade de cópia.

| Propriedade      | Descrição                                                  | Necessário |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade Type da fonte da atividade de cópia deve ser definida como **BinarySink**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como gravar dados em um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de gravação com `storeSettings`suporte em. **Veja os detalhes no artigo do conector – > seção Propriedades da atividade de cópia**. | Não       |

## <a name="next-steps"></a>Passos Seguintes

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
- [Excluir atividade](delete-activity.md)
