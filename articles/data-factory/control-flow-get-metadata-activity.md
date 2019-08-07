---
title: Obter atividade de metadados no Azure Data Factory | Microsoft Docs
description: Saiba como você pode usar a atividade GetMetadata em um pipeline Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: b819a990b9f607aaf70bf2e16a5857de3f7306cc
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827487"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Obter atividade de metadados no Azure Data Factory

A atividade GetMetadata pode ser usada para recuperar **metadados** de quaisquer dados no Azure data Factory. Essa atividade pode ser usada nos seguintes cenários:

- Validar as informações de metadados de quaisquer dados
- Disparar um pipeline quando os dados estiverem prontos/disponíveis

A funcionalidade a seguir está disponível no fluxo de controle:

- A saída da atividade GetMetadata pode ser usada em expressões condicionais para executar a validação.
- Um pipeline pode ser disparado quando A condição é satisfeita por meio do loop do-until

## <a name="supported-capabilities"></a>Capacidades suportadas

A atividade GetMetadata usa um conjunto de dados como uma entrada necessária e gera informações de metadados disponíveis como saída da atividade. Atualmente, os seguintes conectores com metadados recuperáveis correspondentes têm suporte e o tamanho máximo de metadados com suporte é de até **1mb**.

>[!NOTE]
>Se você executar a atividade GetMetadata em um Integration Runtime hospedado internamente, a funcionalidade mais recente terá suporte na versão 3,6 ou superior. 

### <a name="supported-connectors"></a>Conectores com suporte

**Armazenamento de arquivos:**

| Conector/metadados | itemName<br>(arquivo/pasta) | itemType<br>(arquivo/pasta) | size<br>Grupo | criado<br>(arquivo/pasta) | lastModified<br>(arquivo/pasta) |childItems<br>pasta |contentMD5<br>Grupo | structure<br/>Grupo | columnCount<br>Grupo | existe<br>(arquivo/pasta) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Armazenamento em nuvem do Google](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Blob do Azure](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Armazenamento do Azure Data Lake Ger1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [File Storage do Azure](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Sistema de Ficheiros](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

- Para o Amazon S3 e o Google Cloud Storage `lastModified` , o aplica-se ao Bucket e à chave, mas não `exists` à pasta virtual;; e aplica-se ao Bucket e à chave, mas não ao prefixo ou à pasta virtual.
- Para o blob do Azure `lastModified` , o aplica-se ao contêiner e ao blob, mas não à pasta virtual.

**Banco de dados relacional:**

| Conector/metadados | structure | columnCount | existe |
|:--- |:--- |:--- |:--- |
| [Base de Dados SQL do Azure](connector-azure-sql-database.md) | √ | √ | √ |
| [Instância gerida da base de dados SQL do Azure](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Opções de metadados

Os seguintes tipos de metadados podem ser especificados na lista de campos de atividade de GetMetadata para recuperar:

| Tipo de metadados | Descrição |
|:--- |:--- |
| itemName | Nome do arquivo ou da pasta. |
| itemType | Tipo de arquivo ou pasta. O valor de `File` saída `Folder`é ou. |
| size | Tamanho do arquivo em bytes. Aplicável somente ao arquivo. |
| criado | Data e hora de criação do arquivo ou da pasta. |
| lastModified | Data e hora da última modificação do arquivo ou da pasta. |
| childItems | Lista de subpastas e arquivos dentro da pasta especificada. Aplicável somente à pasta. O valor de saída é uma lista de nome e tipo de cada item filho. |
| contentMD5 | MD5 do arquivo. Aplicável somente ao arquivo. |
| structure | Estrutura de dados dentro do arquivo ou tabela de banco de dado relacional. O valor de saída é uma lista de nome de coluna e tipo de coluna. |
| columnCount | Número de colunas dentro do arquivo ou tabela relacional. |
| existe| Se um arquivo/pasta/tabela existe ou não. Observação se "Exists" for especificado na lista de campos GetaMetadata, a atividade não falhará mesmo quando o item (arquivo/pasta/tabela) não existir; em vez disso, `exists: false` ele retorna na saída. |

>[!TIP]
>Quando você deseja validar se um arquivo/pasta/tabela existe ou não, especifique `exists` na lista de campos de atividade GetMetadata, em seguida, você pode verificar o `exists: true/false` resultado da saída da atividade. Se `exists` não estiver configurado na lista de campos, a atividade GetMetadata falhará quando o objeto não for encontrado.

>[!NOTE]
>Quando você obtém metadados de repositórios de arquivos `modifiedDatetimeStart` e configura e `modifiedDatetimeEnd`/ou `childItems` , o na saída só retorna arquivos sob o caminho fornecido com a hora da última modificação entre o intervalo, mas nenhuma subpasta.

## <a name="syntax"></a>Sintaxe

**Atividade de GetMetadata:**

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

**DataSet**

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

## <a name="type-properties"></a>Propriedades do tipo

Atualmente, a atividade GetMetadata pode buscar os seguintes tipos de informações de metadados.

Propriedade | Descrição | Requerido
-------- | ----------- | --------
fieldList | Lista os tipos de informações de metadados necessários. Veja detalhes na seção [Opções de metadados](#metadata-options) em metadados com suporte. | Sim 
DataSet | O conjunto de uma referência cuja atividade de metadados deve ser recuperada pela atividade GetMetadata. Consulte a seção [recursos com suporte](#supported-capabilities) em conectores com suporte e consulte o tópico de conector nos detalhes da sintaxe do conjunto de informações. | Sim
formatSettings | Aplicar ao usar o tipo de conjunto de banco de forma (parquet, DelimitedText). | Não
storeSettings | Aplicar ao usar o tipo de conjunto de banco de forma (parquet, DelimitedText). | Não

## <a name="sample-output"></a>Resultado da amostra

O resultado de GetMetadata é mostrado na saída da atividade. Abaixo estão dois exemplos com opções de metadados exaustivos selecionadas na lista de campos como referência. Para usar o resultado na atividade subsequente, use o padrão de `@{activity('MyGetMetadataActivity').output.itemName}`.

### <a name="get-a-files-metadata"></a>Obter metadados de um arquivo

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

### <a name="get-a-folders-metadata"></a>Obter metadados de uma pasta

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

## <a name="next-steps"></a>Passos Seguintes
Consulte outras atividades de fluxo de controle com suporte pelo Data Factory: 

- [Atividade Executar Pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
