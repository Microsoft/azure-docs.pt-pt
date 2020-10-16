---
title: Ativar e desativar políticas de retenção de dados - Azure SQL Edge
description: Saiba como ativar e desativar políticas de retenção de dados em Azure SQL Edge
keywords: SQL Edge, retenção de dados
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: ee2d65d66caef5cd9405d6e3d0e094de2e30ae87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90902496"
---
# <a name="enable-and-disable-data-retention-policies"></a>Permitir e desativar políticas de retenção de dados

Este tópico descreve como ativar e desativar políticas de retenção de dados para uma base de dados e uma tabela. 

## <a name="enable-data-retention-for-a-database"></a>Permitir a retenção de dados para uma base de dados

O exemplo a seguir mostra como permitir a retenção de dados utilizando [a Alter Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options).

```sql
ALTER DATABASE [<DatabaseName>] SET DATA_RETENTION  ON;
```

## <a name="check-if-data-retention-is-enabled-for-a-database"></a>Verifique se a retenção de dados está ativada para uma base de dados

O seguinte comando pode ser usado para verificar se a retenção de dados está ativada para uma base de dados
```sql
SELECT is_data_retention_enabled, name
FROM sys.databases;
```

## <a name="enable-data-retention-for-a-table"></a>Permitir a retenção de dados para uma tabela

A conservação de dados deve ser ativada para cada tabela para a qual pretende que os dados sejam automaticamente purgados. Quando a Retenção de Dados estiver ativada na base de dados e na tabela, uma tarefa do sistema de fundo digitalizará periodicamente a tabela para identificar e eliminar quaisquer linhas obsoletas (envelhecidas). A conservação de dados pode ser ativada numa tabela durante a criação de tabelas utilizando [a Tabela Criar](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) ou utilizando o Quadro [Alter](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

O exemplo a seguir mostra como permitir a retenção de dados para uma tabela utilizando [o Quadro Criar](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql). 

```sql
CREATE TABLE [dbo].[data_retention_table] 
(
[dbdatetime2] datetime2(7), 
[product_code] int, 
[value] char(10),  
CONSTRAINT [pk_current_data_retention_table] PRIMARY KEY CLUSTERED ([product_code])
) WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )
```

A `WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )` parte do comando da tabela de criação define a retenção de dados na tabela. O comando utiliza os seguintes parâmetros necessários 

- DATA_DELETION - Indica se a retenção de dados é ON ou OFF.
- FILTER_COLUMN - Nome na coluna da tabela, que será usada para verificar se as linhas estão obsoletas ou não. A coluna do filtro só pode ser uma coluna com os seguintes tipos de dados 
    - Date
    - Hora do Pequeno Natal
    - DateTime
    - DataTime2
    - Início de execução de tempo de data
- RETENTION_PERIOD - Um valor inteiro seguido por um descritor de unidade. As unidades permitidas são DIAS, DIAS, SEMANA, SEMANAS, MÊS, MESES, ANO E ANOS.

O exemplo a seguir mostra como permitir a retenção de dados para a tabela utilizando [o Quadro Alter](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).  

```sql
Alter Table [dbo].[data_retention_table]
SET (DATA_DELETION = On (FILTER_COLUMN = [timestamp], RETENTION_PERIOD = 1 day))
```

## <a name="check-if-data-retention-is-enabled-for-a-table"></a>Verifique se a retenção de dados está ativada para uma tabela

O seguinte comando pode ser usado para verificar as tabelas para as quais a retenção de dados está ativada

```sql
select name, data_retention_period, data_retention_period_unit from sys.tables
```

Um valor de data_retention_period = -1 e data_retention_period_unit como INFINITE, indica que a retenção de dados não está definida na tabela.

A seguinte consulta pode ser usada para identificar a coluna utilizada como filter_column para a retenção de dados. 

```sql
Select name from sys.columns
where is_data_deletion_filter_column =1 
and object_id = object_id(N'dbo.data_retention_table', N'U')
```

## <a name="corelating-db-and-table-data-retention-settings"></a>Corelating DB e definições de retenção de dados de tabela

A definição de retenção de dados na base de dados e na tabela, são utilizadas em conjunto para determinar se a recolha automática de filas envelhecidas será ou não executada nas tabelas. 

|Opção de Base de Dados | Opção de tabela | Comportamento |
|----------------|--------------|----------|
| OFF | OFF | A política de retenção de dados é desativada e a limpeza automática e manual dos registos envelhecidos é desativada.|
| OFF | ON  | A política de retenção de dados está ativada para a tabela. A limpeza automática de registos obsoletos é desativada, no entanto o método de limpeza manual pode ser usado para limpar registos obsoletos. |
| ON | OFF | A política de retenção de dados está ativada ao nível da base de dados. No entanto, uma vez que a opção é desativada ao nível da mesa, não existe uma limpeza baseada na retenção de linhas envelhecidas.|
| ON | ON | A política de retenção de dados está ativada tanto para a base de dados como para as tabelas. Está ativada a limpeza automática de registos obsoletos. |

## <a name="disable-data-retention-on-a-table"></a>Desativar a retenção de dados numa tabela 

A conservação de dados pode ser desativada numa tabela utilizando [o Quadro Alter](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql). O seguinte comando pode ser utilizado para desativar a retenção de dados numa tabela.

```sql
Alter Table [dbo].[data_retention_table]
Set (DATA_DELETION = OFF)
```

## <a name="disable-data-retention-on-a-database"></a>Desativar a retenção de dados numa base de dados

A conservação de dados pode ser desativada numa tabela utilizando [a Alter Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options). O seguinte comando pode ser utilizado para desativar a retenção de dados numa base de dados.

```sql
ALTER DATABASE <DatabaseName> SET DATA_RETENTION  OFF;
```

## <a name="next-steps"></a>Passos seguintes
- [Retenção de Dados e Purga automática de dados](data-retention-overview.md)
- [Gerir dados históricos com política de retenção](data-retention-cleanup.md)
