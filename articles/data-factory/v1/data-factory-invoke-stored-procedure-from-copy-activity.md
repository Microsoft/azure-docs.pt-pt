---
title: Invocar um procedimento armazenado de atividade de cópia de fábrica de dados do Azure | Documentos da Microsoft
description: Saiba como invocar um procedimento armazenado na base de dados do Azure SQL ou o SQL Server de uma atividade de cópia de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 35e9347039a7b9939ab4d2719f9738429dec168c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60824257"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Invocar um procedimento armazenado de atividade de cópia no Azure Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [transformar dados com a atividade de procedimento armazenado no Data Factory](../transform-data-using-stored-procedure.md).


Ao copiar dados para o [SQL Server](data-factory-sqlserver-connector.md) ou [base de dados do Azure SQL](data-factory-azure-sql-connector.md), pode configurar o **SqlSink** na atividade de cópia para invocar um procedimento armazenado. Pode querer utilizar armazenados procedimento para executar qualquer processamento adicional (mesclagem de colunas, procurar valores, a inserção em várias tabelas, etc.) é necessária antes de inserir dados na tabela de destino. Esta funcionalidade tira partido das [Table-Valued parâmetros](https://msdn.microsoft.com/library/bb675163.aspx). 

O exemplo a seguir mostra como invocar um procedimento armazenado numa base de dados do SQL Server a partir de um pipeline do Data Factory (atividade de cópia):  

## <a name="output-dataset-json"></a>Conjunto de dados de saída JSON
No dataset de saída JSON, defina o **tipo** para: **SqlServerTable**. Defina-o como **AzureSqlTable** para utilizar com uma base de dados SQL do Azure. O valor para **tableName** propriedade tem de corresponder ao nome do primeiro parâmetro do procedimento armazenado.  

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

## <a name="sqlsink-section-in-copy-activity-json"></a>Secção de SqlSink no JSON de atividade de cópia
Definir o **SqlSink** secção o JSON da atividade de cópia da seguinte forma. Para invocar um procedimento armazenado ao inserir dados na base de dados de sink/destino, especifique valores para os dois **SqlWriterStoredProcedureName** e **SqlWriterTableType** propriedades. Para obter descrições destas propriedades, veja [SqlSink secção no artigo de conector do SQL Server](data-factory-sqlserver-connector.md#sqlsink).

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

## <a name="stored-procedure-definition"></a>Definição do procedimento armazenado 
Na sua base de dados, definir o procedimento armazenado com o mesmo nome que **SqlWriterStoredProcedureName**. O procedimento armazenado processa dados de entrada do arquivo de dados de origem e insere dados numa tabela na base de dados de destino. O nome do primeiro parâmetro do procedimento armazenado tem de corresponder ao tableName definido no conjunto de dados JSON (Marketing).

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
Na sua base de dados, definir o tipo de tabela com o mesmo nome que **SqlWriterTableType**. O esquema do tipo de tabela tem de corresponder ao esquema do conjunto de dados de entrada.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>Passos Seguintes
Reveja o conector seguinte artigos que para obter exemplos JSON completos: 

- [Base de Dados SQL do Azure](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
