---
title: Invocar procedimento armazenado da Azure Data Factory Copy Activity
description: Saiba como invocar um procedimento armazenado na Base de Dados Azure SQL ou NO SQL Server a partir de uma atividade de cópia da Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6f06b84ac0807a37c7adc603a557894be85a4cea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374971"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Invocar procedimento armazenado a partir da atividade de cópia na Azure Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte os dados de [transformação utilizando a atividade de procedimento armazenado na Data Factory](../transform-data-using-stored-procedure.md).


Ao copiar dados para [o SQL Server](data-factory-sqlserver-connector.md) ou [na Base de Dados Azure SQL,](data-factory-azure-sql-connector.md)pode configurar o **SqlSink** na atividade de cópia para invocar um procedimento armazenado. Pode querer utilizar o procedimento armazenado para efetuar qualquer processamento adicional (colunas de fusão, procurando valores, inserção em várias tabelas, etc.) antes de inserir dados na tabela de destino. Esta funcionalidade tira partido dos [parâmetros valorizados da tabela.](/dotnet/framework/data/adonet/sql/table-valued-parameters) 

A amostra que se segue mostra como invocar um procedimento armazenado numa base de dados do SQL Server a partir de um pipeline da Data Factory (atividade de cópia):  

## <a name="output-dataset-json"></a>Conjunto de dados de saída JSON
No conjunto de dados de saída JSON, defina o **tipo** para: **SqlServerTable**. Desapedaça-o ao **AzureSqlTable** para utilizar com a Base de Dados Azure SQL. O valor para **a propriedade tableName** deve corresponder ao nome do primeiro parâmetro do procedimento armazenado.  

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
Defina a secção **SqlSink** na atividade de cópia JSON da seguinte forma. Para invocar um procedimento armazenado ao inserir dados na base de dados da pia/destino, especifique os valores tanto para as propriedades **do SqlWriterStorEdProcedureName** como **para o SqlWriterTableType.** Para obter descrições destas propriedades, consulte [a secção SqlSink no artigo do conector SQL Server](data-factory-sqlserver-connector.md#sqlsink).

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
Na sua base de dados, defina o procedimento armazenado com o mesmo nome que **o SqlWriterStorEdProcedureName**. O procedimento armazenado trata os dados de entrada da loja de dados de origem e insere dados numa tabela na base de dados de destino. O nome do primeiro parâmetro do procedimento armazenado deve coincidir com o conjunto de tabelaSName definido no conjunto de dados JSON (Marketing).

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```

## <a name="table-type-definition"></a>Definição do tipo de tabela
Na sua base de dados, defina o tipo de tabela com o mesmo nome que **SqlWriterTableType**. O esquema do tipo de tabela deve coincidir com o esquema do conjunto de dados de entrada.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>Passos seguintes
Reveja os seguintes artigos de conector que para obter exemplos completos de JSON: 

- [Base de Dados SQL do Azure](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)