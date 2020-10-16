---
title: Obtenha atividade de metadados na Azure Data Factory
description: Saiba como utilizar a atividade get metadados num oleoduto data factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: jingwang
ms.openlocfilehash: e32115c590d73f5c93f322d3bd542096f2964a4c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91297611"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Obtenha atividade de metadados na Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pode utilizar a atividade Get Metadados para recuperar os metadados de quaisquer dados na Azure Data Factory. Pode utilizar esta atividade nos seguintes cenários:

- Validar os metadados de quaisquer dados.
- Desencadeie um oleoduto quando os dados estiverem prontos/disponíveis.

A seguinte funcionalidade está disponível no fluxo de controlo:

- Pode utilizar a saída da atividade Get Metadata em expressões condicionais para realizar validação.
- Pode acionar um gasoduto quando uma condição é satisfeita através do Do Until looping.

## <a name="capabilities"></a>Capacidades

A atividade Get Metadados requer um conjunto de dados como uma entrada e devolve informações de metadados como saída. Atualmente, os seguintes conectores e metadados recuperáveis correspondentes são suportados. O tamanho máximo dos metadados devolvidos é de cerca de 4 MB.

>[!NOTE]
>Se executar a atividade Get Metadata num tempo de integração auto-hospedado, as capacidades mais recentes são suportadas na versão 3.6 ou posterior.

### <a name="supported-connectors"></a>Conectores suportados

**Armazenamento de ficheiros**

| Conector/Metadados | itemName<br>(ficheiro/pasta) | itemType<br>(ficheiro/pasta) | size<br>(arquivo) | criado<br>(ficheiro/pasta) | últimaModificado<br>(ficheiro/pasta) |childItems<br>(pasta) |conteúdoMD5<br>(arquivo) | estrutura<br/>(arquivo) | colunaCount<br>(arquivo) | existe<br>(ficheiro/pasta) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Armazenamento do Azure Data Lake Ger1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) (Armazenamento do Azure Data Lake Gen2) | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| [Ficheiros do Azure](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Sistema de Ficheiros](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

- Ao utilizar a atividade De Metadados contra uma pasta, certifique-se de que tem permissão LIST/EXECUTE para a pasta dada.
- Para o Amazon S3 e o Google Cloud Storage, `lastModified` aplica-se ao balde e à chave, mas não à pasta virtual, e `exists` aplica-se ao balde e à chave, mas não ao prefixo ou pasta virtual.
- Para o armazenamento Azure Blob, `lastModified` aplica-se ao recipiente e à bolha, mas não à pasta virtual.
- `lastModified` O filtro aplica-se atualmente a itens de filtragem de crianças, mas não à pasta/ficheiro especificado.
- O filtro Wildcard nas pastas/ficheiros não é suportado para a atividade Get Metadata.
- `structure` e `columnCount` não são suportados ao obter metadados de ficheiros Binary, JSON ou XML.

**Base de dados relacional**

| Conector/Metadados | estrutura | colunaCount | existe |
|:--- |:--- |:--- |:--- |
| [Base de Dados SQL do Azure](connector-azure-sql-database.md) | √ | √ | √ |
| [Instância Gerida do SQL no Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) | √ | √ | √ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Opções de metadados

Pode especificar os seguintes tipos de metadados na lista de atividades do Get Metadata para recuperar as informações correspondentes:

| Tipo de metadados | Descrição |
|:--- |:--- |
| itemName | Nome do ficheiro ou pasta. |
| itemType | Tipo de ficheiro ou pasta. O valor devolvido é `File` `Folder` ou. |
| size | Tamanho do ficheiro, bytes. Aplicável apenas aos ficheiros. |
| criado | Data criada do ficheiro ou pasta. |
| últimaModificado | Última data modificada do ficheiro ou pasta. |
| childItems | Lista de sub-dobradeiras e ficheiros na pasta dada. Aplicável apenas às pastas. Valor devolvido é uma lista do nome e tipo de cada item infantil. |
| conteúdoMD5 | MD5 do ficheiro. Aplicável apenas aos ficheiros. |
| estrutura | Estrutura de dados do ficheiro ou tabela de bases de dados relacional. Valor devolvido é uma lista de nomes de colunas e tipos de colunas. |
| colunaCount | Número de colunas no ficheiro ou tabela relacional. |
| existe| Se existe um ficheiro, pasta ou tabela. Se `exists` for especificado na lista de campos De Metadados, a atividade não falhará mesmo que o ficheiro, a pasta ou a tabela não existam. Em vez disso, `exists: false` é devolvido na saída. |

>[!TIP]
>Quando pretende validar a existência de um ficheiro, pasta ou tabela, especifique `exists` na lista de casos de atividade de Metadados. Em seguida, pode verificar o `exists: true/false` resultado na saída da atividade. Se `exists` não for especificado na lista de campo, a atividade Get Metadadata falhará se o objeto não for encontrado.

>[!NOTE]
>Quando obtém metadados nas lojas de ficheiros e `modifiedDatetimeStart` configurar `modifiedDatetimeEnd` ou, a `childItems` saída in incluirá apenas ficheiros na via dada que tenham um último tempo modificado dentro do intervalo especificado. Não incluirá itens em sub-dobradeiras.

## <a name="syntax"></a>Syntax

**Obtenha atividade de metadados**

```json
{
    "name":"MyActivity",
    "type":"GetMetadata",
    "dependsOn":[

    ],
    "policy":{
        "timeout":"7.00:00:00",
        "retry":0,
        "retryIntervalInSeconds":30,
        "secureOutput":false,
        "secureInput":false
    },
    "userProperties":[

    ],
    "typeProperties":{
        "dataset":{
            "referenceName":"MyDataset",
            "type":"DatasetReference"
        },
        "fieldList":[
            "size",
            "lastModified",
            "structure"
        ],
        "storeSettings":{
            "type":"AzureBlobStorageReadSettings"
        },
        "formatSettings":{
            "type":"JsonReadSettings"
        }
    }
}
```

**Conjunto de dados**

```json
{
    "name":"MyDataset",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[

        ],
        "type":"Json",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":"file.json",
                "folderPath":"folder",
                "container":"container"
            }
        }
    }
}
```

## <a name="type-properties"></a>Tipo de propriedades

Atualmente, a atividade Get Metadata pode devolver os seguintes tipos de informações de metadados:

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
fieldList | Os tipos de informação de metadados necessários. Para obter mais informações sobre metadados suportados, consulte a secção de opções de [metadados](#metadata-options) deste artigo. | Sim 
conjunto de dados | O conjunto de dados de referência cujos metadados devem ser recuperados pela atividade Get Metadados. Consulte a secção [Capabilities](#capabilities) para obter informações sobre conectores suportados. Consulte os tópicos específicos do conector para obter detalhes da sintaxe do conjunto de dados. | Sim
formatoStas | Aplicar quando utilizar o conjunto de dados do tipo de formato. | Não
lojaSs | Aplicar quando utilizar o conjunto de dados do tipo de formato. | Não

## <a name="sample-output"></a>Saída de exemplo

Os resultados do Get Metadados são apresentados na saída da atividade. Seguem-se duas amostras que mostram extensas opções de metadados. Para utilizar os resultados numa atividade subsequente, utilize este padrão: `@{activity('MyGetMetadataActivity').output.itemName}` .

### <a name="get-a-files-metadata"></a>Obtenha os metadados de um ficheiro

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>Obtenha os metadados de uma pasta

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>Passos seguintes
Conheça outras atividades de fluxo de controlo apoiadas pela Data Factory:

- [Executar atividade de Pipeline](control-flow-execute-pipeline-activity.md)
- [Atividade ForEach](control-flow-for-each-activity.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Atividade web](control-flow-web-activity.md)
