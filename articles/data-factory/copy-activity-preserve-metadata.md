---
title: Preservar metadados e ACLs utilizando a atividade de cópia na Fábrica de Dados Azure
description: Saiba como preservar metadados e ACLs durante a cópia utilizando a atividade de cópia na Azure Data Factory.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260842"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Preservar metadados e ACLs utilizando a atividade de cópia na Fábrica de Dados Azure

Quando utiliza a atividade de cópia da Azure Data Factory para copiar dados de origem para afundar, nos seguintes cenários, também pode preservar os metadados e os ACLs junto.

## <a name="preserve-metadata"></a>Preservar metadados para migração de lagos

Ao migrar dados de um lago de dados para outro, incluindo [amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob,](connector-azure-blob-storage.md)e [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md)pode optar por preservar os metadados dos ficheiros juntamente com os dados.

A atividade de cópia suporta a preservação dos seguintes atributos durante a cópia de dados:

- **Todos os metadados especificados pelo cliente** 
- E as **seguintes cinco propriedades do sistema integrado**em loja de dados : `contentType`, `contentLanguage` (exceto amazon S3), `contentEncoding`, `contentDisposition`, `cacheControl`.

Quando copia ficheiros como é da Amazon S3/Azure Data Lake Storage Gen2/Azure Blob para Azure Data Lake Storage Gen2/Azure Blob com formato binário, pode encontrar a opção **Preserve** no separador Definições de > **De Definições** de Atividade de **Cópia** para autor de atividades ou a página **Definições** na Ferramenta de Dados de Cópia.

![Copiar dados de preservação de atividade](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

Aqui está um exemplo de configuração JSON de atividade de cópia (ver `preserve`): 

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

Ao atualizar de Azure Data Lake Storage Gen1 para Gen2, pode optar por preservar as listas de controlo de acesso POSIX (ACLs) juntamente com ficheiros de dados. Para obter mais informações sobre o controlo de acesso, consulte o [controlo de acesso em Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) e [controle de acesso em Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

A atividade de cópia suporta a preservação dos seguintes tipos de ACLs durante a cópia de dados. Pode selecionar um ou mais tipos:

- **ACL**: Copiar e preservar listas de controlo de acesso POSIX em ficheiros e diretórios. Copia todos os ACLs existentes de origem em pia. 
- **Proprietário**: Copiar e preservar o utilizador proprietário de ficheiros e diretórios. É necessário o acesso do super-utilizador ao armazenamento do lago de dados Gen2.
- **Grupo**: Copiar e preservar o grupo de detenção de ficheiros e diretórios. É necessário o acesso do super-utilizador ao data Lake Storage Gen2 ou ao utilizador próprio (se o utilizador possuir também for membro do grupo alvo).

Se especificar copiar a partir de uma pasta, a Data Factory replica os ACLs para a pasta dada e os ficheiros e diretórios que estão por baixo, se `recursive` for em realidade. Se especificar copiar a partir de um único ficheiro, os ACLs desse ficheiro são copiados.

>[!NOTE]
>Quando utilizar a ADF para preservar os ACLs do Data Lake Storage Gen1 para a Gen2, os ACLs existentes na pasta/ficheiros correspondentes da Gen2 serão substituídos.

>[!IMPORTANT]
>Quando optar por preservar OS ACLs, certifique-se de que concede permissões suficientemente elevadas para que a Data Factory opere contra a sua conta de Armazenamento de Data Lake Gen2. Por exemplo, utilize a autenticação da chave da conta ou atribua a função de Proprietário de Dados do Depósito Blob ao principal do serviço ou identidade gerida.

Quando configurar a fonte como Data Lake Storage Gen1 com formato binário ou a opção de cópia binária, e afundar como Data Lake Storage Gen2 com formato binário ou a opção de cópia binária, pode encontrar a opção **Preservar** na página **Definições** na Ferramenta de Dados de Cópia ou no separador De **Definições** de > **De Imagem** para a autoria da atividade.

![Data Lake Storage Gen1 para Gen2 Preserve ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Aqui está um exemplo de configuração JSON de atividade de cópia (ver `preserve`): 

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

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Copiar desempenho da atividade](copy-activity-performance.md)
