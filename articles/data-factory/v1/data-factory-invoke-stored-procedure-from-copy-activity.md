---
title: Invocar procedimento armazenado da Atividade de Cópia da Fábrica de Dados de Azure
description: Saiba como invocar um procedimento armazenado na Base de Dados Azure SQL, ou SQL Server a partir de uma atividade de cópia da Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d05c2b03a0c498144f37c9b6205053120a596b09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74924086"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Invocar procedimento armazenado a partir da atividade de cópia na Fábrica de Dados Azure
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [a transformação de dados utilizando a atividade de procedimento armazenado na Fábrica](../transform-data-using-stored-procedure.md)de Dados .


Ao copiar dados para [o SQL Server](data-factory-sqlserver-connector.md) ou para a Base de [Dados SQL,](data-factory-azure-sql-connector.md)pode configurar o **SqlSink** na atividade de cópia para invocar um procedimento armazenado. É necessário utilizar o procedimento armazenado para efetuar qualquer tratamento adicional (colunas de fusão, procurar valores, inserção em várias tabelas, etc.) antes de inserir os dados na tabela de destino. Esta funcionalidade tira partido dos [parâmetros de valor de tabela.](https://msdn.microsoft.com/library/bb675163.aspx) 

A amostra que se segue mostra como invocar um procedimento armazenado numa base de dados do SQL Server a partir de um pipeline data Factory (atividade de cópia):  

## <a name="output-dataset-json"></a>Conjunto de dados de saída JSON
No conjunto de dados de saída JSON, desloque o **tipo** para: **SqlServerTable**. Deite-o no **AzureSqlTable** para utilizar com uma base de dados Azure SQL. O valor da propriedade **tableName** deve coincidir com o nome do primeiro parâmetro do procedimento armazenado.  

```json
{
  "name": "SqlOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlLinkedService",
    "typeProperties": {
      "tableName": "Marketing"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

## <a name="sqlsink-section-in-copy-activity-json"></a>Secção SqlSink na atividade de cópia JSON
Defina a secção **SqlSink** na atividade de cópia JSON da seguinte forma. Para invocar um procedimento armazenado ao inserir dados na base de dados de sink/destination, especifique valores tanto para as propriedades **SqlWriterStoredProcedureName** e **SqlWriterTableType.** Para descrições destas propriedades, consulte a [secção SqlSink no artigo do conector SQL Server](data-factory-sqlserver-connector.md#sqlsink).

```json
"sink":
{
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
    "storedProcedureParameters":
            {
                "stringData": 
                {
                    "value": "str1"     
                }
            }
}
```

## <a name="stored-procedure-definition"></a>Definição de procedimento armazenado 
Na sua base de dados, defina o procedimento armazenado com o mesmo nome que **o Nome de Procedimento SqlWriterStored**. O procedimento armazenado trata os dados de entrada da loja de dados de origem e insere os dados numa tabela na base de dados do destino. O nome do primeiro parâmetro de procedimento armazenado deve coincidir com o nome do quadro definido no conjunto de dados JSON (Marketing).

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```

## <a name="table-type-definition"></a>Definição de tipo de tabela
Na sua base de dados, defina o tipo de tabela com o mesmo nome que **o SqlWriterTableType**. O esquema do tipo de mesa deve coincidir com o esquema do conjunto de dados de entrada.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>Passos seguintes
Reveja os seguintes artigos de conector que para exemplos completos da JSON: 

- [Base de Dados SQL do Azure](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
