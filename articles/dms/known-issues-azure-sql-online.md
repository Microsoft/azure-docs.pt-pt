---
title: 'Questões conhecidas: Migrações online para base de dados SQL'
titleSuffix: Azure Database Migration Service
description: Conheça questões/limitações de migração conhecidas com migrações online para a Base de Dados Azure SQL utilizando o Serviço de Migração de Bases de Dados Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: e7efdb7244e2c7e4651a4507b538123f8d320c1e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77650780"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database"></a>Questões conhecidas/limitações de migração com migrações online para base de dados Azure SQL

São descritas abaixo questões e limitações conhecidas associadas às migrações online do SQL Server para a Base de Dados SQL Azure.

> [!IMPORTANT]
> Com as migrações on-line do SQL Server para a Base de Dados SQL Azure, a migração de tipos de dados SQL_variant não é suportada.

### <a name="migration-of-temporal-tables-not-supported"></a>Migração de tabelas temporais não apoiadas

**Sintoma**

Se a sua base de dados de origem for constituída por uma ou mais tabelas temporais, a sua migração na base de dados falha durante o funcionamento "Carga completa de dados" e poderá ver a seguinte mensagem:

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![Exemplo de erros de tabela temporal](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Supor**

Utilize os seguintes passos.

1. Encontre as tabelas tempora no seu esquema de origem utilizando a consulta abaixo.

     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```

2. Exclua estas tabelas da lâmina de definições de **migração Configure,** na qual especifica tabelas para migração.

3. Reexecutar a atividade migratória.

**Recursos**

Para mais informações, consulte o artigo [Tabelas Temporais.](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017)

### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>A migração de tabelas inclui uma ou mais colunas com o tipo de dados hierárquica

**Sintoma**

Pode ver uma Exceção SQL sugerindo que "o texto é incompatível com a hierarquia" durante a operação "Carga completa de dados":

![exemplo de erros hierárquios](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Supor**

Utilize os seguintes passos.

1. Encontre as tabelas de utilizador que incluem colunas com o tipo de dados hierárquicos utilizando a consulta abaixo.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ```

2. Exclua estas tabelas da lâmina de definições de **migração Configure,** na qual especifica tabelas para migração.

3. Reexecutar a atividade migratória.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Falhas migratórias com várias violações de integridade com gatilhos ativos no esquema durante "Carga completa de dados" ou "Sincronização incremental de dados"

**Supor**

Utilize os seguintes passos.

1. Encontre os gatilhos que estão atualmente ativos na base de dados de origem utilizando a consulta abaixo:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Desative os gatilhos na base de dados da fonte utilizando os passos fornecidos no artigo [TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

3. Reexecutar a atividade migratória.

### <a name="support-for-lob-data-types"></a>Suporte para tipos de dados LOB

**Sintoma**

Se o comprimento da coluna De Objeto Grande (LOB) for superior a 32 KB, os dados podem ser truncados no alvo. Pode verificar o comprimento da coluna LOB utilizando a consulta abaixo:

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Supor**

Se tiver uma coluna LOB superior a 32 KB, contacte a equipa de engenharia da [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="issues-with-timestamp-columns"></a>Problemas com colunas de carimbos de tempo

**Sintoma**

O Serviço de Migração da Base de Dados Azure não migra o valor do carimbo de tempo de origem; em vez disso, o Azure Database Migration Service gera um novo valor de carimbo temporal na tabela-alvo.

**Supor**

Se precisar do Serviço de Migração de Bases de Dados Azure para migrar o valor exato de carimbo de tempo armazenado na tabela de origem, contacte a equipa de engenharia da [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Erros de migração de dados não fornecem detalhes adicionais na base de dados detalhada sintetizada lâmina de estado

**Sintoma**

Quando se depara com falhas migratórias na visão de estado dos Dados de Dados, a seleção da ligação de erros de **migração de Dados** na fita superior pode não fornecer detalhes adicionais específicos às falhas migratórias.

![erros de migração de dados sem exemplo de detalhes](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Supor**

Para obter detalhes específicos de falha, use os seguintes passos.

1. Feche a lâmina de estado detalhada da Base de Dados para exibir o ecrã da atividade migratória.

     ![tela de atividade migratória](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Selecione **Ver detalhes de erro** para visualizar mensagens de erro específicas que o ajudam a resolver erros de migração.

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>Tipo de dados de geografia não suportado na migração online SQLDB

**Sintoma**

A migração falha com uma mensagem de erro contendo o seguinte texto:

     “** encountered a fatal error”, "errorEvents":<Table>.<Column> is of type 'GEOGRAPHY', which is not supported by 'Full Load' under 'Full LOB' support mode."

**Supor**

Enquanto o Azure Database Migration Service suporta o tipo de dados de Geografia para migrações offline para a Base de Dados Azure SQL, para migrações online, o tipo de dados de Geografia não é suportado. Tente métodos alternativos para alterar o tipo de dados na fonte para um tipo suportado antes de tentar utilizar o Serviço de Migração de Bases de Dados Azure para uma migração online desta base de dados.

### <a name="supported-editions"></a>Edições apoiadas

**Sintoma**

A migração falha com uma mensagem de erro contendo o seguinte texto:

    Migration settings validation error: The edition of the server [Business Intelligence Edition (64-bit)] does not match the supported edition(s) [Enterprise,Standard,Developer].

**Supor**

O suporte para migrações on-line para a Base de Dados Azure SQL utilizando o Serviço de Migração de Bases de Dados Azure estende-se apenas às edições Enterprise, Standard e Developer. Certifique-se de que está a usar uma edição apoiada antes de iniciar o processo de migração.
