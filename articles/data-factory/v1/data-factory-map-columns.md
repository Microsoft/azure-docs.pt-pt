---
title: Mapeando colunas do conjunto de linhas no Azure Data Factory
description: Saiba como mapear colunas de origem para colunas de destino.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6eb7012e28319ee6cc86de5ee56090743d681068
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74923875"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Mapear colunas do conjunto de origem para colunas do conjunto de fonte de destino
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. 

O mapeamento de coluna pode ser usado para especificar como as colunas especificadas na "estrutura" da tabela de origem são mapeadas para as colunas especificadas na "estrutura" da tabela de coletor. A propriedade **columnMapping** está disponível na seção **typeproperties** da atividade de cópia.

O mapeamento de coluna oferece suporte aos seguintes cenários:

* Todas as colunas na estrutura do conjunto de dados de origem são mapeadas para todas as colunas na estrutura do conjunto de dados do coletor.
* Um subconjunto das colunas na estrutura do conjunto de dados de origem é mapeado para todas as colunas na estrutura do conjunto de dados do coletor.

Veja a seguir as condições de erro que resultam em uma exceção:

* Uma quantidade menor de colunas ou mais colunas na "estrutura" da tabela de coletores do que o especificado no mapeamento.
* Mapeamento duplicado.
* O resultado da consulta SQL não tem um nome de coluna especificado no mapeamento.

> [!NOTE]
> Os exemplos a seguir são para o Azure SQL e o blob do Azure, mas são aplicáveis a qualquer armazenamento de dados que ofereça suporte a conjuntos de dados retangulares. Ajuste o conjunto de dados e as definições de serviço vinculadas em exemplos para apontar para dado na fonte de dados relevante.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Exemplo 1 – mapeamento de coluna do Azure SQL para o blob do Azure
Neste exemplo, a tabela de entrada tem uma estrutura e aponta para uma tabela SQL em um banco de dados SQL do Azure.

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

Neste exemplo, a tabela de saída tem uma estrutura e aponta para um blob em um armazenamento de BLOBs do Azure.

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

O JSON a seguir define uma atividade de cópia em um pipeline. As colunas da origem são mapeadas para colunas no coletor (**ColumnMappings**) usando a propriedade **Translator** .

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

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Exemplo 2 – mapeamento de coluna com consulta SQL do Azure SQL para o blob do Azure
Neste exemplo, uma consulta SQL é usada para extrair dados do SQL do Azure em vez de simplesmente especificar o nome da tabela e os nomes de coluna na seção "estrutura". 

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
Nesse caso, os resultados da consulta são mapeados primeiro para colunas especificadas em "Structure" da origem. Em seguida, as colunas da "estrutura" de origem são mapeadas para colunas na "estrutura" do coletor com as regras especificadas em columnMappings.  Suponha que a consulta retorne 5 colunas, mais duas colunas do que aquelas especificadas na "estrutura" da origem.

**Fluxo de mapeamento de coluna**

![Fluxo de mapeamento de coluna-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Passos seguintes
Consulte o artigo para obter um tutorial sobre como usar a atividade de cópia: 

- [Copiar dados do armazenamento de BLOBs para o banco de dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
