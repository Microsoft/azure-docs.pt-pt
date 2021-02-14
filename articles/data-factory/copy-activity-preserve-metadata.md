---
title: Preservar metadados e ACLs utilizando a atividade de cópia na Azure Data Factory
description: Saiba como preservar metadados e ACLs durante a cópia utilizando a atividade de cópia na Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: jingwang
ms.openlocfilehash: 396a598d143e85687f9dfbf765b3c18736627e41
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387721"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Preservar metadados e ACLs utilizando a atividade de cópia na Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Quando utilizar a atividade de cópia da Azure Data Factory para copiar dados de origem para afundar, nos seguintes cenários, também pode preservar os metadados e ACLs junto.

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a> Preservar metadados para a migração de lagos

Quando migrar dados de um lago de dados para outro, incluindo [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)e [Azure File Storage,](connector-azure-file-storage.md)pode optar por preservar os metadados do ficheiro juntamente com os dados.

A atividade de cópia suporta a preservação dos seguintes atributos durante a cópia de dados:

- **Todos os metadados especificados pelo cliente** 
- E as **seguintes cinco propriedades do sistema de armazenamento de dados** incorporadas : `contentType` , `contentLanguage` (exceto o Amazon S3), `contentEncoding` , `contentDisposition` `cacheControl` .

**Lidar com diferenças nos metadados:** O Amazon S3 e o Azure Storage permitem diferentes conjuntos de caracteres nas teclas dos metadados especificados pelo cliente. Quando opta por preservar metadados utilizando a atividade de cópia, a ADF substitui automaticamente os caracteres inválidos por '_'.

Quando copia ficheiros como é do Amazon S3/Azure Data Lake Storage Gen2/Azure Blob/Azure File Storage to Azure Data Lake Storage Gen2/Azure Blob/Azure File Storage com formato binário, pode encontrar a opção **Preserve** no separador **Definições** de Atividade de Cópia  >   para autoria de atividades ou na página Definições na Ferramenta de **Dados** de Cópia.

![Copiar atividade preservar metadados](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

Aqui está um exemplo de configuração JSON de atividade de cópia `preserve` (ver): 

```json
"activities":[
    {
        "name": "CopyAndPreserveMetadata",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AmazonS3ReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "Attributes"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset Amazon S3/Azure Blob/ADLS Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset for Azure Blob/ADLS Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a> Preservar ACLs de Data Lake Storage Gen1/Gen2 para Gen2

Ao atualizar de Azure Data Lake Storage Gen1 para Gen2 ou copiar dados entre a ADLS Gen2, pode optar por preservar as listas de controlo de acessos POSIX (ACLs) juntamente com ficheiros de dados. Para obter mais informações sobre o controlo de acessos, consulte [o controlo de acesso no Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) e o controlo de acesso em [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

A atividade de cópia suporta a preservação dos seguintes tipos de ACLs durante a cópia de dados. Pode selecionar um ou mais tipos:

- **ACL**: Copiar e preservar listas de controlo de acesso POSIX em ficheiros e diretórios. Copia todos os ACLs existentes de fonte para afundar. 
- **Proprietário**: Copiar e preservar o utilizador próprio de ficheiros e diretórios. É necessário aceder ao Super-utilizador para afundar a Data Lake Storage Gen2.
- **Grupo**: Copiar e preservar o grupo de ficheiros e diretórios. É necessário o acesso do super utilizador ao sumidouro Data Lake Storage Gen2 ou ao utilizador próprio (se o utilizador próprio também for membro do grupo-alvo).

Se especificar para copiar de uma pasta, a Data Factory replica os ACLs para essa pasta dada e os ficheiros e diretórios sob a pasta, se `recursive` for definido como verdadeiro. Se especificar para copiar a partir de um único ficheiro, os ACLs desse ficheiro são copiados.

>[!NOTE]
>Quando utilizar a ADF para preservar ACLs da Data Lake Storage Gen1/Gen2 para a Gen2, os ACLs existentes na pasta/ficheiros correspondentes da Gen2 serão substituídos.

>[!IMPORTANT]
>Quando optar por preservar acLs, certifique-se de conceder permissões suficientemente elevadas para que a Data Factory opere contra a sua conta de Data Lake Storage Gen2. Por exemplo, utilize a autenticação da chave de conta ou atribua a função de Proprietário de Dados de Armazenamento ao titular do serviço ou identidade gerida.

Quando configurar a fonte como Data Lake Storage Gen1/Gen2 com formato binário ou a opção de cópia binária, e afundar como Data Lake Storage Gen2 com formato binário ou a opção de cópia binária, pode encontrar a opção **Preserve** na página Definições na Ferramenta de **Dados** de Cópia ou no separador Definições de **Definições** de Cópia  >  para autoria de **atividades.**

![Data Lake Storage Gen1/Gen2 para Gen2 Preserve ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Aqui está um exemplo de configuração JSON de atividade de cópia `preserve` (ver): 

```json
"activities":[
    {
        "name": "CopyAndPreserveACLs",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1/Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>Passos seguintes

Consulte os outros artigos de Atividade de Cópia:

- [Visão geral da atividade da cópia](copy-activity-overview.md)
- [Desempenho da atividade de cópia](copy-activity-performance.md)
