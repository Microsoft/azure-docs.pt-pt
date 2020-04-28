---
title: Mapeando colunas de conjunto de dados na Fábrica de Dados de Azure
description: Aprenda a mapear colunas de origem para colunas de destino.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74923875"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Map source dataset columns to destination dataset columns
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. 

O mapeamento da coluna pode ser utilizado para especificar como as colunas especificadas na "estrutura" do mapa da tabela de origem às colunas especificadas na "estrutura" da tabela de sumidouros. A propriedade **columnMapping** está disponível na secção **typeProperties** da atividade Copy.

O mapeamento da coluna suporta os seguintes cenários:

* Todas as colunas da estrutura de conjunto de dados de origem estão mapeadas para todas as colunas na estrutura do conjunto de dados do lavatório.
* Um subconjunto das colunas na estrutura do conjunto de dados de origem é mapeado para todas as colunas na estrutura do conjunto de dados do lavatório.

Seguem-se as condições de erro que resultam numa exceção:

* Ou menos colunas ou mais colunas na "estrutura" da tabela do lavatório do que especificado no mapeamento.
* Mapeamento duplicado.
* O resultado da consulta SQL não tem um nome de coluna especificado no mapeamento.

> [!NOTE]
> As seguintes amostras são para O SQL e o Blob Azure, mas são aplicáveis a qualquer loja de dados que suporte conjuntos de dados retangulares. Ajuste as definições de conjunto de dados e de serviço satiseleções em exemplos para apontar aos dados na fonte de dados relevante.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Amostra 1 – mapeamento de colunas de Azure SQL para blob Azure
Nesta amostra, a tabela de entrada tem uma estrutura e aponta para uma tabela SQL numa base de dados Azure SQL.

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

Nesta amostra, a tabela de saída tem uma estrutura e aponta para uma bolha num armazenamento de bolhas Azure.

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

O seguinte JSON define uma atividade de cópia num oleoduto. As colunas da fonte mapeadas para colunas em sumidouro **(columnMappings**) utilizando a propriedade **tradutora.**

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
**Fluxo de mapeamento de colunas:**

![Fluxo de mapeamento de coluna](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Amostra 2 – mapeamento de colunas com consulta SQL de Azure SQL a Blob Azure
Nesta amostra, uma consulta SQL é usada para extrair dados do Azure SQL em vez de simplesmente especificar o nome da tabela e os nomes da coluna na secção "estrutura". 

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
Neste caso, os resultados da consulta são primeiro mapeados para colunas especificadas em "estrutura" de origem. Em seguida, as colunas da "estrutura" da fonte são mapeadas para colunas em "estrutura" de afundar com regras especificadas em colunaSMaps.  Suponha que a consulta rederede 5 colunas, mais duas colunas do que as especificadas na "estrutura" da fonte.

**Fluxo de mapeamento de coluna**

![Fluxo de mapeamento de coluna-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Passos seguintes
Consulte o artigo para um tutorial sobre a utilização da Atividade de Cópia: 

- [Copiar dados do Armazenamento Blob para a Base de Dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
