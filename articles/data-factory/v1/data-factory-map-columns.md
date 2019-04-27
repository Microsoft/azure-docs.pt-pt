---
title: Mapeamento de colunas do conjunto de dados no Azure Data Factory | Documentos da Microsoft
description: Saiba como mapear colunas de origem para colunas de destino.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1b009ac2ca42e9804b88989b55b2e73524732550
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60238116"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Mapear colunas do conjunto de dados de origem para colunas do conjunto de dados de destino
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. 

Mapeamento de colunas pode ser utilizado para especificar a forma como as colunas especificadas na "estrutura" do mapa de tabela de origem para colunas especificada na "estrutura" da tabela do sink. O **columnMapping** propriedade está disponível na **typeProperties** secção da atividade de cópia.

Mapeamento de colunas suporta os seguintes cenários:

* Todas as colunas na estrutura do conjunto de dados de origem estão mapeadas para todas as colunas na estrutura do conjunto de dados de sink.
* Um subconjunto das colunas na estrutura do conjunto de dados de origem é mapeado para todas as colunas na estrutura do conjunto de dados de sink.

Seguem-se as condições de erro que resultam numa exceção:

* Menos colunas ou mais colunas na "estrutura" da tabela do sink que especificado no mapeamento.
* Mapeamento de duplicados.
* Resultado da consulta SQL não tem um nome de coluna especificado no mapeamento.

> [!NOTE]
> Os exemplos seguintes são para o SQL do Azure e BLOBs do Azure, mas são aplicáveis a qualquer arquivo de dados que suporta conjuntos de dados retangulares. Ajuste o conjunto de dados e definições de serviço ligado nos exemplos para apontar para dados na origem de dados relevantes.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Exemplo 1 – a coluna de mapeamento de SQL do Azure para BLOBs do Azure
Neste exemplo, a tabela de entrada tem uma estrutura e aponta para uma tabela SQL na base de dados SQL do Azure.

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Neste exemplo, a tabela de saída tem uma estrutura e aponta para um blob num armazenamento de Blobs do Azure.

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

O JSON seguinte define uma atividade de cópia num pipeline. As colunas de origem mapeado para colunas no sink (**columnMappings**), utilizando o **Translator** propriedade.

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**Fluxo de mapeamento de coluna:**

![Fluxo de mapeamento de coluna](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Exemplo 2 – coluna mapeamento com consulta SQL da SQL do Azure para BLOBs do Azure
Neste exemplo, uma consulta SQL é utilizada para extrair dados do SQL do Azure em vez de simplesmente especificar o nome da tabela e os nomes das colunas na seção de "estrutura". 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
Neste caso, os resultados da consulta são mapeados para colunas especificadas na "estrutura" da origem. Em seguida, as colunas da origem de "estrutura" são mapeadas para colunas no sink "estrutura" com as regras especificadas em columnMappings.  Suponha que a consulta retorna 5 colunas, dois mais colunas do que as especificadas na "estrutura" da origem.

**Fluxo de mapeamento de coluna**

![Mapeamento de coluna fluxo-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Passos Seguintes
Consulte o artigo para obter um tutorial sobre como utilizar a atividade de cópia: 

- [Copiar dados de armazenamento de BLOBs para base de dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
