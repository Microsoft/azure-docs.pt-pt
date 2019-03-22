---
title: Artigo sobre as limitações de migração/problemas conhecidos com as migrações de online para a SQL Database do Azure | Documentos da Microsoft
description: Saiba mais sobre as limitações de migração/problemas conhecidos com as migrações de online para a base de dados do Azure SQL.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/05/2019
ms.openlocfilehash: 38a59a3a390977c5a3fd22b185542f5f2ec33d79
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58091499"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-db"></a>Limitações de migração/problemas conhecidos com as migrações de online para a BD SQL do Azure

Problemas conhecidos e limitações associadas a migrações online do SQL Server a SQL Database do Azure estão descritas abaixo.

### <a name="migration-of-temporal-tables-not-supported"></a>Migração de tabelas temporais não suportado

**Sintoma**

Se a sua base de dados de origem é composta por um ou mais tabelas temporais, a migração de base de dados falha durante a operação de "carregamento de dados completa" e pode ver a seguinte mensagem:

{"resourceId": "/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType": "Erro de migração de base de dados", "errorEvents": "[" funcionalidades de captura não foi possível definir. RetCode: SQL_ERROR SqlState: 42000 NativeError: Mensagem 13570: [Microsoft] [SQL Server Native Client 11.0][SQL] [SQL Server] a utilização da replicação não é suportado com a tabela temporal com versão do sistema "[aplicativo. Cidades] "linha: 1 Column: -1 "]" }
 
 ![Exemplo de erros de tabela temporal](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Solução**

1. Encontre as tabelas temporais no seu esquema de origem com a consulta abaixo.
     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```
2. Excluir essas tabelas a partir da **configurar definições de migração** painel, no qual especifica tabelas para migração.

3. Volte a executar a atividade de migração.

**Recursos**

Para obter mais informações, consulte o artigo [tabelas temporais](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017).
 
### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>Migração de tabelas inclui uma ou mais colunas com o tipo de dados hierarchyid

**Sintoma**

Poderá ver uma exceção de SQL sugerindo "ntext é incompatível com hierarchyid" durante a operação de "carregamento de dados completa":
     
![exemplo de erros de hierarchyid](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Solução**

1. Encontre as tabelas de utilizador que inclua colunas com o tipo de dados hierarchyid, usando a consulta abaixo.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ``` 

   1. Excluir essas tabelas a partir da **configurar definições de migração** painel, no qual especifica tabelas para migração.

   1. Volte a executar a atividade de migração.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Falhas de migração com vários violações de integridade com os acionadores de Active Directory no esquema durante a "carregamento de dados completa" ou "sincronização de dados Incremental"

**Solução**
1. Localize os acionadores que estão atualmente ativos no banco de dados de origem com a consulta abaixo:
     ```
     select * from sys.triggers where is_disabled =0
     ```
2. Desativar os disparadores na sua base de dados de origem através dos passos apresentados no artigo [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

3. Volte a executar a atividade de migração.

### <a name="support-for-lob-data-types"></a>Suporte para tipos de dados LOB

**Sintoma**

Se o comprimento da coluna de objeto grande (LOB) é maior do que 32 KB, dados poderão obter truncados no destino. Pode verificar o comprimento de coluna LOB usando a consulta abaixo: 

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Solução**

Se tiver uma coluna de LOB que é maior do que 32 KB, contacte a equipa de engenharia em [faça migrações de base de dados do Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="issues-with-timestamp-columns"></a>Problemas com as colunas timestamp

**Sintoma**

O DMS não migrar o valor da origem timestamp; em vez disso, o DMS gera um novo valor de timestamp na tabela de destino.

**Solução**

Se precisar de DMS para migrar o valor exato timestamp armazenado na tabela de origem, contacte a equipa de engenharia em [faça migrações de base de dados do Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="data-migration-errors-do-not-provide-additional-details-on-the-database-detailed-status-blade"></a>Erros de migração de dados não fornecem detalhes adicionais no painel de estado detalhado da base de dados.

**Sintoma**

Quando encontrar as falhas de migração na vista de estado de detalhes de bases de dados, selecionando o **erros de migração de dados** link da faixa de opções principais poderão não fornecer detalhes adicionais específicos para as falhas de migração.

![erros de migração de dados nenhum exemplo de detalhes](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Solução**

Para obter detalhes sobre a falha específica, siga os passos abaixo.

1. Feche o painel de estado detalhado da base de dados para apresentar o ecrã de atividade de migração.

     ![ecrã de atividade de migração](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Selecione **ver detalhes do erro** para ver mensagens de erro específicas que ajudam a resolver problemas de erros de migração.
