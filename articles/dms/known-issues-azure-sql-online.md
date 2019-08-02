---
title: Artigo sobre problemas conhecidos/limitações de migração com migrações online para o banco de dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre problemas conhecidos/limitações de migração com migrações online para o banco de dados SQL do Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 07/27/2019
ms.openlocfilehash: 7cd8b7c2accae097c971aec4b92cf38ed5d3af08
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561513"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database"></a>Problemas conhecidos/limitações de migração com migrações online para o banco de dados SQL do Azure

Os problemas conhecidos e as limitações associadas a migrações online do SQL Server para o banco de dados SQL do Azure são descritos abaixo.

> [!IMPORTANT]
> Com as migrações online do SQL Server para o banco de dados SQL do Azure, não há suporte para a migração de tipos de dado SQL_variant.

### <a name="migration-of-temporal-tables-not-supported"></a>Não há suporte para a migração de tabelas temporais

**Sintoma**

Se o banco de dados de origem consistir em uma ou mais tabelas temporais, a migração do banco de dados falhará durante a operação de "carregamento de carga total" e você poderá ver a seguinte mensagem:

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![Exemplo de erros de tabela temporal](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Solução**

Use as etapas a seguir.

1. Localize as tabelas temporais em seu esquema de origem usando a consulta abaixo.

     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```

2. Exclua essas tabelas da folha **definir configurações de migração** , nas quais você especifica tabelas para migração.

3. Execute novamente a atividade de migração.

**Recursos**

Para obter mais informações, consulte o artigo [tabelas temporais](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017).

### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>A migração de tabelas inclui uma ou mais colunas com o tipo de dados hierarchyid

**Sintoma**

Você pode ver uma exceção SQL sugerindo "ntext é incompatível com hierarchyid" durante a operação de "carregamento de dados completo":

![exemplo de erros hierarchyid](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Solução**

Use as etapas a seguir.

1. Localize as tabelas de usuário que incluem colunas com o tipo de dados hierarchyid usando a consulta abaixo.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ```

2. Exclua essas tabelas da folha **definir configurações de migração** , nas quais você especifica tabelas para migração.

3. Execute novamente a atividade de migração.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Falhas de migração com várias violações de integridade com gatilhos ativos no esquema durante a "carga de dados completa" ou "sincronização de dados incremental"

**Solução**

Use as etapas a seguir.

1. Localize os gatilhos que estão ativos no momento no banco de dados de origem usando a consulta abaixo:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Desabilite os gatilhos no banco de dados de origem usando as etapas fornecidas no artigo [desabilitar gatilho (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

3. Execute novamente a atividade de migração.

### <a name="support-for-lob-data-types"></a>Suporte para tipos de dados LOB

**Sintoma**

Se o comprimento da coluna de objeto grande (LOB) for maior que 32 KB, os dados poderão ser truncados no destino. Você pode verificar o comprimento da coluna LOB usando a consulta abaixo:

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Solução**

Se você tiver uma coluna LOB maior que 32 KB, entre em contato com a equipe de engenharia em [solicitar migrações de banco de dados do Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="issues-with-timestamp-columns"></a>Problemas com colunas timestamp

**Sintoma**

O serviço de migração de banco de dados do Azure não migra o valor de timestamp de origem; em vez disso, o serviço de migração de banco de dados do Azure gera um novo valor timestamp na tabela de destino.

**Solução**

Se você precisar do serviço de migração de banco de dados do Azure para migrar o valor exato do carimbo de data/hora armazenado na tabela de origem, contate a equipe de engenharia em [solicitar migrações de banco de dados](mailto:AskAzureDatabaseMigrations@service.microsoft.com)

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Os erros de migração de dados não fornecem detalhes adicionais sobre a folha status detalhado do banco de dados

**Sintoma**

Quando você se depara com falhas de migração na exibição de status detalhes de bancos de **dados** , a seleção do link de erros de migração de dados na faixa de visão superior pode não fornecer detalhes adicionais específicos para as falhas de migração.

![exemplo de erros de migração de dados sem detalhes](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Solução**

Para obter detalhes específicos da falha, use as etapas a seguir.

1. Feche a folha status detalhado do banco de dados para exibir a tela atividade de migração.

     ![tela de atividade de migração](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Selecione **Ver detalhes do erro** para exibir mensagens de erro específicas que ajudam a solucionar erros de migração.

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>Tipo de dados de Geografia sem suporte na migração online do SQLDB

**Sintoma**

A migração falha com uma mensagem de erro que contém o seguinte texto:

     “** encountered a fatal error”, "errorEvents":<Table>.<Column> is of type 'GEOGRAPHY', which is not supported by 'Full Load' under 'Full LOB' support mode."

**Solução**

Embora o serviço de migração de banco de dados do Azure dê suporte ao tipo de dado geography para migrações offline para o Azure SQL Database, para migrações online, não há suporte para o DataType geography. Experimente os métodos alternativos para alterar o tipo de dados na origem para um Type com suporte antes de tentar usar o serviço de migração de banco de dados do Azure para uma migração online desse banco de dados.

### <a name="supported-editions"></a>Edições suportadas

**Sintoma**

A migração falha com uma mensagem de erro que contém o seguinte texto:

    Migration settings validation error: The edition of the server [Business Intelligence Edition (64-bit)] does not match the supported edition(s) [Enterprise,Standard,Developer].

**Solução**

O suporte para migrações online para o banco de dados SQL do Azure usando o serviço de migração de banco de dados do Azure se estende somente às edições Enterprise, Standard e Developer. Certifique-se de que você está usando uma edição com suporte antes de iniciar o processo de migração.
