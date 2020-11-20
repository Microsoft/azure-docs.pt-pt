---
title: 'Questões conhecidas: Migrações online para a Base de Dados SQL'
titleSuffix: Azure Database Migration Service
description: Conheça as questões conhecidas/limitações de migração com migrações on-line para Azure SQL Database utilizando o Serviço de Migração de Bases de Dados Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 748c3f8e38b98b2cbdcfecdf7d755827230fdb3d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962472"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database"></a>Questões conhecidas/limitações de migração com migrações online para Azure SQL Database

São descritos abaixo questões e limitações associadas às migrações on-line do SQL Server para Azure SQL Database.

> [!IMPORTANT]
> Com migrações on-line do SQL Server para Azure SQL Database, a migração de SQL_variant tipos de dados não é suportada.

### <a name="migration-of-temporal-tables-not-supported"></a>Migração de tabelas temporais não suportadas

**Sintoma**

Se a sua base de dados de origem for constituída por uma ou mais tabelas temporais, a migração da sua base de dados falha durante a operação "Carga completa de dados" e poderá ver a seguinte mensagem:

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![Exemplo de erros de tabela temporal](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Solução**

Utilize os passos seguintes.

1. Encontre as tabelas temporais no seu esquema de origem utilizando a consulta abaixo.

     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```

2. Exclua estas tabelas da lâmina de configuração de configurações de configuração de **configuração,** nas quais especifique tabelas para migração.

3. Reencaúndio da atividade migratória.

**Recursos**

Para mais informações, consulte o artigo [Tabelas Temporais.](/sql/relational-databases/tables/temporal-tables?view=sql-server-2017)

### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>A migração de tabelas inclui uma ou mais colunas com o tipo de dados hierárquica

**Sintoma**

Pode ver uma exceção SQL que sugere que "ntext é incompatível com a hierarquia" durante a operação "Full data load":

![exemplo de erros hierárquica](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Solução**

Utilize os passos seguintes.

1. Encontre as tabelas de utilizador que incluem colunas com o tipo de dados hierárquicas utilizando a consulta abaixo.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ```

2. Exclua estas tabelas da lâmina de configuração de configurações de configuração de **configuração,** nas quais especifique tabelas para migração.

3. Reencaúndio da atividade migratória.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Falhas de migração com várias violações de integridade com gatilhos ativos no esquema durante "Carga completa de dados" ou "Sincronização incremental de dados"

**Solução**

Utilize os passos seguintes.

1. Encontre os gatilhos que estão atualmente ativos na base de dados de origem utilizando a consulta abaixo:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Desative os gatilhos na sua base de dados de origem utilizando os passos fornecidos no artigo [DESATIVAR TRIGGER (Transact-SQL)](/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

3. Reencaúndio da atividade migratória.

### <a name="support-for-lob-data-types"></a>Suporte para tipos de dados LOB

**Sintoma**

Se o comprimento da coluna De Objeto Grande (LOB) for maior do que 32 KB, os dados podem ser truncados no alvo. Pode verificar o comprimento da coluna LOB utilizando a consulta abaixo:

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Solução**

Se tiver uma coluna LOB superior a 32 KB, contacte a equipa de engenharia da [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="issues-with-timestamp-columns"></a>Problemas com colunas de tempotamp

**Sintoma**

O Serviço de Migração da Base de Dados Azure não migra o valor do preço do tempo de origem; em vez disso, o Azure Database Migration Service gera um novo valor de etiqueta de tempo na tabela-alvo.

**Solução**

Se necessitar do Serviço de Migração da Base de Dados Azure para migrar o valor exato do cartão de ponto armazenado na tabela de origem, contacte a equipa de engenharia da [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Os erros de migração de dados não fornecem detalhes adicionais na lâmina de estado detalhada da Base de Dados

**Sintoma**

Quando se depara com falhas de migração na visão de estado dos dados das bases de dados, a seleção da ligação de **erros de migração de dados** na fita superior pode não fornecer detalhes adicionais específicos das falhas de migração.

![erros de migração de dados sem exemplo de detalhes](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Solução**

Para obter detalhes específicos de falha, utilize os seguintes passos.

1. Feche a lâmina de estado detalhada da Base de Dados para visualizar o ecrã de atividade da migração.

     ![tela de atividade de migração](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Selecione **Consulte os detalhes de erro** para visualizar mensagens de erro específicas que o ajudam a resolver erros de migração.

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>Técnica de geografia não suportada na migração online SQLDB

**Sintoma**

A migração falha com uma mensagem de erro contendo o seguinte texto:

```output
"** encountered a fatal error", "errorEvents":<Table>.<Column> is of type 'GEOGRAPHY', which is not supported by 'Full Load' under 'Full LOB' support mode.
```

**Solução**

Enquanto o Azure Database Migration Service suporta o tipo de dados de Geografia para migrações offline para Azure SQL Database, para migrações online, o tipo de dados de Geografia não é suportado. Tente métodos alternativos para alterar o tipo de dados na fonte para um tipo suportado antes de tentar utilizar o Azure Database Migration Service para uma migração online desta base de dados.

### <a name="supported-editions"></a>Edições apoiadas

**Sintoma**

A migração falha com uma mensagem de erro contendo o seguinte texto:

```output
Migration settings validation error: The edition of the server [Business Intelligence Edition (64-bit)] does not match the supported edition(s) [Enterprise,Standard,Developer].
```

**Solução**

O suporte para migrações online para Azure SQL Database utilizando o Azure Database Migration Service estende-se apenas às edições Enterprise, Standard e Developer. Certifique-se de que está a usar uma edição apoiada antes de iniciar o processo de migração.