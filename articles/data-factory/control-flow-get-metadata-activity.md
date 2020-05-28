---
title: Obtenha atividade de Metadados na Fábrica de Dados do Azure
description: Aprenda a utilizar a atividade de Obter Metadados num pipeline data factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jingwang
ms.openlocfilehash: a59d9291d1eaa4aa87d40914679e39c9cbf29cee
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84112647"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Obtenha atividade de Metadados na Fábrica de Dados do Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pode utilizar a atividade Get Metadata para recuperar os metadados de quaisquer dados na Azure Data Factory. Pode utilizar esta atividade nos seguintes cenários:

- Valide os metadados de quaisquer dados.
- Desencadear um oleoduto quando os dados estiverem prontos/disponíveis.

A seguinte funcionalidade está disponível no fluxo de controlo:

- Pode utilizar a saída da atividade Get Metadata em expressões condicionais para efetuar validação.
- Pode acionar um oleoduto quando uma condição é satisfeita através do Do Até a circular.

## <a name="capabilities"></a>Capacidades

A atividade Get Metadata toma um conjunto de dados como entrada e devolve informações de metadados como saída. Atualmente, são suportados os seguintes conectores e metadados recuperáveis correspondentes. O tamanho máximo dos metadados devolvidos é de 2 MB.

>[!NOTE]
>Se executar a atividade Get Metadata num tempo de execução de integração auto-hospedado, as mais recentes capacidades são suportadas na versão 3.6 ou posterior.

### <a name="supported-connectors"></a>Conectores suportados

**Armazenamento de ficheiros**

| Conector/Metadados | itemName<br>(ficheiro/pasta) | artigoType<br>(ficheiro/pasta) | size<br>(arquivo) | criado<br>(ficheiro/pasta) | últimaModificada<br>(ficheiro/pasta) |criançaItems<br>(pasta) |conteúdoMD5<br>(arquivo) | estrutura<br/>(arquivo) | colunaCount<br>(arquivo) | existe<br>(ficheiro/pasta) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Armazenamento Azure Blob](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Armazenamento do Azure Data Lake Ger1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) (Armazenamento do Azure Data Lake Gen2) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Ficheiros do Azure](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Sistema de ficheiros](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

- Ao utilizar a atividade de Obter Metadados contra uma pasta, certifique-se de que tem permissão LIST/EXECUTE para a pasta dada.
- Para o Amazon S3 e o Google Cloud Storage, `lastModified` aplica-se ao balde e à chave, mas não à pasta virtual, e `exists` aplica-se ao balde e à chave, mas não ao prefixo ou pasta virtual.
- Para o armazenamento da Blob Azure, `lastModified` aplica-se ao recipiente e à bolha, mas não à pasta virtual.
- `lastModified`o filtro aplica-se atualmente para filtrar artigos infantis, mas não a própria pasta/ficheiro especificado.
- O filtro Wildcard nas pastas/ficheiros não é suportado para obter a atividade de Metadados.

**Base de dados relacional**

| Conector/Metadados | estrutura | colunaCount | existe |
|:--- |:--- |:--- |:--- |
| [Base de Dados SQL do Azure](connector-azure-sql-database.md) | √ | √ | √ |
| [Instância Gerida do Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) | √ | √ | √ |
| [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Opções de metadados

Pode especificar os seguintes tipos de metadados na lista de campo de atividade de Obter Metadados para recuperar as informações correspondentes:

| Tipo de metadados | Descrição |
|:--- |:--- |
| itemName | Nome do ficheiro ou pasta. |
| artigoType | Tipo do ficheiro ou pasta. O valor devolvido é `File` ou `Folder` . |
| size | Tamanho do ficheiro, em bytes. Aplicável apenas aos ficheiros. |
| criado | Criou a data do ficheiro ou pasta. |
| últimaModificada | Última data modificada do ficheiro ou pasta. |
| criançaItems | Lista de subpastas e ficheiros na pasta dada. Aplicável apenas às pastas. O valor devolvido é uma lista do nome e do tipo de cada item infantil. |
| conteúdoMD5 | MD5 do ficheiro. Aplicável apenas aos ficheiros. |
| estrutura | Estrutura de dados do ficheiro ou tabela de bases de dados relacional. O valor devolvido é uma lista de nomes de colunas e tipos de colunas. |
| colunaCount | Número de colunas no ficheiro ou tabela relacional. |
| existe| Quer exista um ficheiro, uma pasta ou uma tabela. Note que se for especificado na lista de `exists` campo Get Metadata, a atividade não falhará mesmo que o ficheiro, pasta ou tabela não existam. Em vez disso, `exists: false` é devolvido na saída. |

>[!TIP]
>Quando pretender validar que existe um ficheiro, pasta ou tabela, especificar na lista de campo de `exists` atividade seletiva Obter Metadados. Em seguida, pode verificar o `exists: true/false` resultado na saída da atividade. Se não estiver especificado na lista de `exists` campo, a atividade get metadata falhará se o objeto não for encontrado.

>[!NOTE]
>Quando obtém metadados de lojas de ficheiros e configuraou `modifiedDatetimeStart` ou , a saída incluirá `modifiedDatetimeEnd` `childItems` apenas ficheiros no caminho dado que tenham um último tempo modificado dentro da gama especificada. Não incluirá itens em subpastas.

## <a name="syntax"></a>Sintaxe

**Obtenha atividade de Metadados**

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```

**Conjunto de dados**

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "filename": "file.json",
            "format":{
                "type":"JsonFormat"
            }
        }
    }
}
```

## <a name="type-properties"></a>Propriedades de tipo

Atualmente, a atividade Get Metadata pode devolver os seguintes tipos de informações de metadados:

Propriedade | Descrição | Necessário
-------- | ----------- | --------
fieldList | Os tipos de informação dos metadados necessários. Para mais informações sobre metadados suportados, consulte a secção de opções de [Metadados](#metadata-options) deste artigo. | Sim 
conjunto de dados | O conjunto de dados de referência cujos metadados serão recuperados pela atividade get metadata. Consulte a secção [Capacidades](#capabilities) para obter informações sobre conectores suportados. Consulte os tópicos específicos do conector para obter detalhes da sintaxe do conjunto de dados. | Sim
formatoDefinições | Aplicar quando utilizar o conjunto de dados do tipo formato. | Não
lojasDefinições | Aplicar quando utilizar o conjunto de dados do tipo formato. | Não

## <a name="sample-output"></a>Resultado da amostra

Os resultados get Metadata são mostrados na saída da atividade. Seguem-se duas amostras que mostram extensas opções de metadados. Para utilizar os resultados numa atividade subsequente, utilize este padrão: `@{activity('MyGetMetadataActivity').output.itemName}` .

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

## <a name="next-steps"></a>Próximos passos
Conheça outras atividades de fluxo de controlo suportadas pela Data Factory:

- [Executar atividade do pipeline](control-flow-execute-pipeline-activity.md)
- [Atividade ForEach](control-flow-for-each-activity.md)
- [Atividade de procura](control-flow-lookup-activity.md)
- [Atividade web](control-flow-web-activity.md)
