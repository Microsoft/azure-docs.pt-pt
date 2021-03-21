---
title: Obtenha atividade de metadados na Azure Data Factory
description: Saiba como utilizar a atividade get metadados num oleoduto data factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: jingwang
ms.openlocfilehash: bd8fc3383d6d9a0afb7733cb94643623e6879d23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178546"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Obtenha atividade de metadados na Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pode utilizar a atividade Get Metadados para recuperar os metadados de quaisquer dados na Azure Data Factory. Pode utilizar a saída da atividade Get Metadata em expressões condicionais para realizar validação ou consumir os metadados em atividades subsequentes.

## <a name="supported-capabilities"></a>Capacidades suportadas

A atividade Get Metadados requer um conjunto de dados como uma entrada e devolve informações de metadados como saída. Atualmente, os seguintes conectores e os metadados recuperáveis correspondentes são suportados. O tamanho máximo dos metadados devolvidos é **de 4 MB.**

### <a name="supported-connectors"></a>Conectores suportados

**Armazenamento de ficheiros**

| Conector/Metadados | itemName<br>(ficheiro/pasta) | itemType<br>(ficheiro/pasta) | size<br>(arquivo) | criado<br>(ficheiro/pasta) | últimaModified<sup>1</sup><br>(ficheiro/pasta) |childItems<br>(pasta) |conteúdoMD5<br>(arquivo) | estrutura<sup>2</sup><br/>(arquivo) | colunaCount<sup>2</sup><br>(arquivo) | existe<sup>3</sup><br>(ficheiro/pasta) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| [Armazenamento do Azure Data Lake Ger1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) (Armazenamento do Azure Data Lake Gen2) | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| [Ficheiros do Azure](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Sistema de Ficheiros](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

<sup>1</sup> `lastModified` Metadados:
- Para o Amazon S3 e o Google Cloud Storage, `lastModified` aplica-se ao balde e à chave, mas não à pasta virtual, e `exists` aplica-se ao balde e à chave, mas não ao prefixo ou pasta virtual. 
- Para o armazenamento Azure Blob, `lastModified` aplica-se ao recipiente e à bolha, mas não à pasta virtual.

<sup>2</sup> Metadados `structure` e não são `columnCount` suportados ao obter metadados de ficheiros Binários, JSON ou XML.

<sup>3</sup> Metadados `exists` : Para o Amazon S3 e o Google Cloud Storage, `exists` aplica-se ao balde e à chave, mas não à pasta prefixo ou virtual.

Tenha em atenção o seguinte:

- Ao utilizar a atividade De Metadados contra uma pasta, certifique-se de que tem permissão LIST/EXECUTE para a pasta dada.
- O filtro Wildcard nas pastas/ficheiros não é suportado para a atividade Get Metadata.
- `modifiedDatetimeStart` e `modifiedDatetimeEnd` conjunto de filtro no conector:

    - Estas duas propriedades são utilizadas para filtrar os itens da criança quando recebem metadados de uma pasta. Não se aplica quando se obtenha metadados de um ficheiro.
    - Quando este filtro é utilizado, a `childItems` saída de entrada inclui apenas os ficheiros que são modificados dentro do intervalo especificado, mas não as pastas.
    - Para aplicar este filtro, a atividade GetMetadata enumerará todos os ficheiros na pasta especificada e verificará o tempo modificado. Evite apontar para uma pasta com um grande número de ficheiros, mesmo que a contagem de ficheiros qualificada esperada seja pequena. 

**Base de dados relacional**

| Conector/Metadados | estrutura | colunaCount | existe |
|:--- |:--- |:--- |:--- |
| [Base de Dados SQL do Azure](connector-azure-sql-database.md) | √ | √ | √ |
| [Instância Gerida do SQL no Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) | √ | √ | √ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Opções de metadados

Pode especificar os seguintes tipos de metadados na lista de atividades do Get Metadata para recuperar as informações correspondentes:

| Tipo de metadados | Description |
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

> [!TIP]
> Quando pretende validar a existência de um ficheiro, pasta ou tabela, especifique `exists` na lista de casos de atividade de Metadados. Em seguida, pode verificar o `exists: true/false` resultado na saída da atividade. Se `exists` não for especificado na lista de campo, a atividade Get Metadadata falhará se o objeto não for encontrado.

> [!NOTE]
> Quando obtém metadados nas lojas de ficheiros e `modifiedDatetimeStart` configurar `modifiedDatetimeEnd` ou, a `childItems` saída inclui apenas ficheiros na trajetória especificada que têm um último tempo modificado dentro do intervalo especificado. Os artigos nas sub-dobradeiras não estão incluídos.

> [!NOTE]
> Para que a lista de campos **Estruturar** forneça a estrutura real de dados para textos delimitados e conjuntos de dados de formato Excel, deve ativar a `First Row as Header` propriedade, que é suportada apenas para estas fontes de dados.

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
fieldList | Os tipos de informação de metadados necessários. Para obter mais informações sobre metadados suportados, consulte a secção de opções de [metadados](#metadata-options) deste artigo. | Yes 
conjunto de dados | O conjunto de dados de referência cujos metadados devem ser recuperados pela atividade Get Metadados. Consulte a secção [Capabilities](#supported-capabilities) para obter informações sobre conectores suportados. Consulte os tópicos específicos do conector para obter detalhes da sintaxe do conjunto de dados. | Yes
formatoStas | Aplicar quando utilizar o conjunto de dados do tipo de formato. | No
lojaSs | Aplicar quando utilizar o conjunto de dados do tipo de formato. | No

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
