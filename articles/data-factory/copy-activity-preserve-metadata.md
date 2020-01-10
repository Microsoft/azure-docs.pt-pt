---
title: Preservar metadados e ACLs usando a atividade de cópia no Azure Data Factory
description: Saiba mais sobre como preservar metadados e ACLs durante a cópia usando a atividade de cópia no Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: jingwang
ms.openlocfilehash: 056909f5fd5838e5ae50fb84bd3535029d862acf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483248"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Preservar metadados e ACLs usando a atividade de cópia no Azure Data Factory

Quando você usa Azure Data Factory atividade de cópia para copiar dados da origem para o coletor, nos cenários a seguir, você também pode preservar os metadados e as ACLs.

## <a name="preserve-metadata"></a>Preservar metadados para a migração do Lake

Ao migrar dados de um data Lake para outro, incluindo [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md)e [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), você pode optar por preservar os metadados do arquivo junto com os dados.

A atividade de cópia dá suporte à preservação dos seguintes atributos durante a cópia de dados:

- **Todos os metadados especificados pelo cliente** 
- E as **cinco Propriedades de sistema internas do repositório de dados**a seguir: `contentType`, `contentLanguage` (exceto para Amazon S3), `contentEncoding`, `contentDisposition``cacheControl`.

Ao copiar arquivos no estado em que se encontram do Amazon S3/Azure Data Lake Storage Gen2/blob do Azure para Azure Data Lake Storage Gen2/blob do Azure com formato binário, você pode encontrar a opção **preservar** na **guia Configurações** da **atividade de cópia** > para criação de atividade ou a página **configurações** na ferramenta copiar dados.

![Preservar metadados de atividade de cópia](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

Aqui está um exemplo de configuração de JSON da atividade de cópia (consulte `preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
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

## <a name="preserve-acls"></a>Preservar ACLs de Data Lake Storage Gen1 para Gen2

Ao atualizar de Azure Data Lake Storage Gen1 para Gen2, você pode optar por preservar as ACLs (listas de controle de acesso) do POSIX junto com os arquivos de dados. Para obter mais informações sobre o controle de acesso, consulte [controle de acesso em Azure data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) e [controle de acesso em Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

A atividade de cópia dá suporte à preservação dos seguintes tipos de ACLs durante a cópia de dados. Você pode selecionar um ou mais tipos:

- **ACL**: Copie e preserve listas de controle de acesso POSIX em arquivos e diretórios. Ele copia as ACLs completas existentes da origem para o coletor. 
- **Proprietário**: Copie e preserve o usuário proprietário de arquivos e diretórios. O acesso de superusuário ao Data Lake Storage Gen2 de coletor é necessário.
- **Grupo**: Copie e preserve o grupo proprietário de arquivos e diretórios. É necessário o acesso de superusuário ao coletor Data Lake Storage Gen2 ou ao usuário proprietário (se o usuário proprietário também for um membro do grupo de destino).

Se você especificar para copiar de uma pasta, Data Factory Replica as ACLs para aquela pasta especificada e os arquivos e diretórios sob ela, se `recursive` estiver definido como true. Se você especificar para copiar de um único arquivo, as ACLs nesse arquivo serão copiadas.

>[!NOTE]
>Quando você usa o ADF para preservar ACLs de Data Lake Storage Gen1 para Gen2, as ACLs existentes na pasta/arquivos correspondentes do Gen2's serão substituídas.

>[!IMPORTANT]
>Ao optar por preservar as ACLs, certifique-se de conceder permissões altas o suficiente para Data Factory operar em sua conta de Data Lake Storage Gen2 do coletor. Por exemplo, use a autenticação de chave de conta ou atribua a função de proprietário de dados do blob de armazenamento à entidade de serviço ou à identidade gerenciada.

Quando você configura a origem como Data Lake Storage Gen1 com o formato binário ou a opção de cópia binária, e o coletor como Data Lake Storage Gen2 com formato binário ou a opção de cópia binária, você pode encontrar a opção **preservar** na página **configurações** na ferramenta copiar dados ou na guia **configurações** de > de **atividade de cópia** para a criação de atividades.

![Data Lake Storage Gen1 Gen2 preservar ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Aqui está um exemplo de configuração de JSON da atividade de cópia (consulte `preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
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
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1 source>",
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

Consulte os outros artigos de atividade de cópia:

- [Descrição geral da atividade de cópia](copy-activity-overview.md)
- [Desempenho da atividade de cópia](copy-activity-performance.md)
