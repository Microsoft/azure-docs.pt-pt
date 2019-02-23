---
title: Atividade obter metadados da fábrica de dados do Azure | Documentos da Microsoft
description: Saiba como pode utilizar a atividade de procedimento armazenado do SQL Server para invocar um procedimento armazenado num banco de dados do Azure SQL ou o Azure SQL Data Warehouse a partir de um pipeline do Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: shlo
ms.openlocfilehash: ca77fa092e41534e0f18323014c9cea583c19758
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56671663"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Atividade obter metadados da fábrica de dados do Azure
Atividade GetMetadata pode ser usada para recuperar **metadados** de quaisquer dados no Azure Data Factory. Esta atividade pode ser usada nos seguintes cenários:

- Validar as informações de metadados de quaisquer dados
- Acionar um pipeline, quando os dados estão prontos / disponíveis

A seguinte funcionalidade está disponível no fluxo de controlo:

- A saída da atividade GetMetadata pode ser utilizada em expressões condicionais para executar a validação.
- Um pipeline pode ser acionado quando a condição for satisfeita por meio de fazer-até um loop

## <a name="supported-capabilities"></a>Capacidades suportadas

A atividade GetMetadata usa um conjunto de dados como entrada necessária e apresenta informações de metadados disponíveis como saída da atividade. Atualmente, os conectores com metadados recuperável correspondente que se seguem são suportados e o tamanho máximo de metadados suportados é até **1MB**.

>[!NOTE]
>Se executar a atividade GetMetadata no Integration Runtime autoalojado, a capacidade de mais recente é suportada na versão 3.6 ou posterior. 

### <a name="supported-connectors"></a>Conectores suportados

**Armazenamento de ficheiros:**

| Conector/metadados | itemName<br>(ficheiro/pasta) | itemType<br>(ficheiro/pasta) | tamanho<br>(ficheiro) | criado<br>(ficheiro/pasta) | lastModified<br>(ficheiro/pasta) |childItems<br>(pasta) |contentMD5<br>(ficheiro) | estrutura<br/>(ficheiro) | columnCount<br>(ficheiro) | Existe<br>(ficheiro/pasta) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Amazon S3 | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| Google Cloud Storage | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| Blob do Azure | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| Armazenamento do Azure Data Lake Ger1 | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Armazenamento de Ficheiros do Azure | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| Sistema de Ficheiros | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| SFTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| FTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

- Amazon S3 e o armazenamento de Sloud do Google, o `lastModified` aplica-se para o registo e de chave, mas não virtual pasta; e o `exists` aplica-se para o registo e chave, mas não para o prefixo ou pasta virtual.
- Para o Blob do Azure, o `lastModified` aplica-se ao contentor e blob mas pasta není virtuální.

**Base de dados relacional:**

| Conector/metadados | estrutura | columnCount | Existe |
|:--- |:--- |:--- |:--- |
| Base de Dados SQL do Azure | √ | √ | √ |
| Instância Gerida da Base de Dados SQL do Azure | √ | √ | √ |
| Azure SQL Data Warehouse | √ | √ | √ |
| SQL Server | √ | √ | √ |

### <a name="metadata-options"></a>Opções de metadados

Os seguintes tipos de metadados podem ser especificados na lista de campos de atividade GetMetadata recuperar:

| Tipo de metadados | Descrição |
|:--- |:--- |
| itemName | Nome do ficheiro ou pasta. |
| itemType | Tipo de ficheiro ou pasta. Valor de saída é `File` ou `Folder`. |
| tamanho | Tamanho do ficheiro em bytes. Aplicável a apenas de ficheiros. |
| criado | Datetime de criação do ficheiro ou pasta. |
| lastModified | Última modificação datetime do ficheiro ou pasta. |
| childItems | Lista de subpastas e ficheiros dentro da pasta especificada. Aplicável apenas a pasta. Valor de saída é uma lista de nome e tipo de cada item subordinado. |
| contentMD5 | MD5 do ficheiro. Aplicável a apenas de ficheiros. |
| estrutura | Estrutura de dados dentro do ficheiro ou uma tabela de base de dados relacional. Valor de saída é uma lista de nome de coluna e o tipo de coluna. |
| columnCount | Número de colunas no interior do ficheiro ou uma tabela relacional. |
| Existe| Se um ficheiro/pasta/tabela já existe ou não. Tenha em atenção que "existe" seja especificado na lista de campos de GetaMetadata, a atividade não falha, mesmo quando o item (ficheiro/pasta/tabela) não existe; em vez disso, ele retorna `exists: false` na saída. |

>[!TIP]
>Quando deseja validar se um ficheiro/pasta/tabela existe ou não, especifique `exists` na lista de campos de atividade GetMetadata, em seguida, pode verificar o `exists: true/false` resultam da saída da atividade. Se `exists` não está configurado na lista de campos, GetMetadata atividade irão falhar quando o objeto não foi encontrado.

## <a name="syntax"></a>Sintaxe

**Atividade GetMetadata:**

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

**Conjunto de dados:**

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

Atualmente, atividade GetMetadata pode obter os seguintes tipos de informações de metadados.

Propriedade | Descrição | Necessário
-------- | ----------- | --------
fieldList | Lista os tipos de informações de metadados necessárias. Ver detalhes nos [opções de metadados](#metadata-options) seção sobre metadados suportados. | Sim 
Conjunto de dados | O conjunto de dados de referência, é possível obter pela atividade GetMetadata cuja atividade de metadados. Ver [capacidades suportadas](#supported-capabilities) secção em conectores suportados e consulte o tópico do conector nos detalhes da sintaxe de conjunto de dados. | Sim

## <a name="sample-output"></a>Resultado da amostra

O resultado de GetMetadata é mostrado na saída da atividade. Seguem-se dois exemplos com opções de metadados exaustiva selecionados na lista de campos como referência. Para utilizar o resultado na atividade subsequente, utilize o padrão de `@{activity('MyGetMetadataActivity').output.itemName}`.

### <a name="get-a-files-metadata"></a>Obter metadados de um ficheiro

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
Consulte outras atividades de fluxo de controle suportadas pelo Data Factory: 

- [Atividade Executar Pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)
- [Atividade Web](control-flow-web-activity.md)
