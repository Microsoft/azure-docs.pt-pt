---
title: Mapeamento de colunas de conjuntos de dados na Fábrica de Dados Azure
description: Saiba como mapear colunas de origem para colunas de destino.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: fef2c6f120ae25e6aa1846d4971ff707da9bab92
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100371129"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Colunas de conjunto de dados de origem do mapa para colunas de conjunto de dados de destino
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. 

O mapeamento de colunas pode ser utilizado para especificar como as colunas especificadas na "estrutura" do mapa do quadro de origem para colunas especificadas na "estrutura" da tabela do lavatório. A **propriedade de cartografamento de colunas** está disponível na secção **de tipoProperties** da atividade Copy.

O mapeamento da coluna suporta os seguintes cenários:

* Todas as colunas na estrutura do conjunto de dados de origem estão mapeadas para todas as colunas na estrutura do conjunto de dados do lavatório.
* Um subconjunto das colunas na estrutura do conjunto de dados de origem é mapeado para todas as colunas na estrutura do conjunto de dados do lavatório.

Seguem-se as condições de erro que resultam numa exceção:

* Ou menos colunas ou mais colunas na "estrutura" da mesa do lavatório do que as especificadas no mapeamento.
* Mapeamento duplicado.
* O resultado da consulta SQL não tem um nome de coluna especificado no mapeamento.

> [!NOTE]
> As seguintes amostras são para Azure SQL e Azure Blob, mas são aplicáveis a qualquer loja de dados que suporte conjuntos de dados retangulares. Ajuste o conjunto de dados e as definições de serviços ligados em exemplos para apontar para os dados na fonte de dados relevante.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Amostra 1 – mapeamento de colunas de Azure SQL a Azure blob
Nesta amostra, a tabela de entrada tem uma estrutura e aponta para uma tabela SQL na Base de Dados Azure SQL.

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

O seguinte JSON define uma atividade de cópia num oleoduto. As colunas desde a fonte mapeadas a colunas em pia **(columnMappings**) utilizando a propriedade **Tradutor.**

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

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Amostra 2 - mapeamento de coluna com consulta SQL de Azure SQL a Azure blob
Nesta amostra, é utilizada uma consulta SQL para extrair dados do Azure SQL em vez de simplesmente especificar o nome da tabela e os nomes das colunas na secção "estrutura". 

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
Neste caso, os resultados da consulta são mapeados pela primeira vez para colunas especificadas em "estrutura" de origem. Em seguida, as colunas da "estrutura" de origem são mapeadas para colunas em "estrutura" do lavatório com regras especificadas em colunasMappings.  Suponha que a consulta retorna 5 colunas, mais duas colunas do que as especificadas na "estrutura" da fonte.

**Fluxo de mapeamento de coluna**

![Fluxo de mapeamento de coluna-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Passos seguintes
Consulte o artigo para um tutorial sobre a utilização da Copy Activity: 

- [Copiar dados do Blob Storage para a Base de Dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
