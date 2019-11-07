---
title: Invocar procedimento armazenado de Azure Data Factory atividade de cópia
description: Saiba como invocar um procedimento armazenado no banco de dados SQL do Azure ou SQL Server de uma atividade de cópia Azure Data Factory.
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
ms.openlocfilehash: 0c5bb7ab4e8932c9568293620352435c7259d810
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682496"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Invocar procedimento armazenado da atividade de cópia no Azure Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [transformar dados usando a atividade de procedimento armazenado no data Factory](../transform-data-using-stored-procedure.md).


Ao copiar dados para o [SQL Server](data-factory-sqlserver-connector.md) ou para o [Azure SQL Database](data-factory-azure-sql-connector.md), você pode configurar o **sqlsink** na atividade de cópia para invocar um procedimento armazenado. Talvez você queira usar o procedimento armazenado para executar qualquer processamento adicional (mesclando colunas, pesquisando valores, inserindo em várias tabelas etc.) é necessário antes de inserir dados na tabela de destino. Esse recurso aproveita os [parâmetros com valor de tabela](https://msdn.microsoft.com/library/bb675163.aspx). 

O exemplo a seguir mostra como invocar um procedimento armazenado em um SQL Server banco de dados de um pipeline de Data Factory (atividade de cópia):  

## <a name="output-dataset-json"></a>DataSet de saída JSON
No DataSet de saída JSON, defina o **tipo** como: **sqlservertable**. Defina-o como **AzureSqlTable** para usar com um banco de dados SQL do Azure. O valor da propriedade **TableName** deve corresponder ao nome do primeiro parâmetro do procedimento armazenado.  

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

## <a name="sqlsink-section-in-copy-activity-json"></a>Seção sqlsink na atividade de cópia JSON
Defina a seção **sqlsink** no JSON da atividade de cópia da seguinte maneira. Para invocar um procedimento armazenado durante a inserção de dados no banco de dado do coletor/destino, especifique valores para as propriedades **SqlWriterStoredProcedureName** e **SqlWriterTableType** . Para obter descrições dessas propriedades, consulte [a seção sqlsink no artigo conector de SQL Server](data-factory-sqlserver-connector.md#sqlsink).

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
No banco de dados, defina o procedimento armazenado com o mesmo nome que **SqlWriterStoredProcedureName**. O procedimento armazenado manipula dados de entrada do armazenamento de dados de origem e insere dados em uma tabela no banco de dado de destino. O nome do primeiro parâmetro do procedimento armazenado deve corresponder ao TableName definido no DataSet JSON (marketing).

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
No banco de dados, defina o tipo de tabela com o mesmo nome que **SqlWriterTableType**. O esquema do tipo de tabela deve corresponder ao esquema do conjunto de dados de entrada.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>Passos seguintes
Examine os seguintes artigos do conector para obter exemplos de JSON completos: 

- [Base de Dados SQL do Azure](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
