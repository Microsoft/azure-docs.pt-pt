---
title: Artigo sobre problemas conhecidos/limitações de migração com migrações online para o banco de dados do Azure para MySQL | Microsoft Docs
description: Saiba mais sobre problemas conhecidos/limitações de migração com migrações online para o banco de dados do Azure para MySQL.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 08/06/2019
ms.openlocfilehash: 56758e2962adb41c9876171c89b37263a70ed0e4
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743546"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-postgresql"></a>Problemas conhecidos/limitações de migração com migrações online para o BD do Azure para PostgreSQL

Os problemas conhecidos e as limitações associadas a migrações online do PostgreSQL para o banco de dados do Azure para PostgreSQL são descritas nas seções a seguir.

## <a name="online-migration-configuration"></a>Configuração de migração online

- O servidor PostgreSQL de origem deve estar executando a versão 9.5.11, 9.6.7 ou 10,3 ou posterior. Para obter mais informações, consulte o artigo [versões de banco de dados PostgreSQL com suporte](../postgresql/concepts-supported-versions.md).
- Há suporte apenas para migrações de versão. Por exemplo, não há suporte para a migração do PostgreSQL 9.5.11 para o banco de dados do Azure para PostgreSQL 9.6.7.

    > [!NOTE]
    > Para PostgreSQL versão 10, atualmente o DMS dá suporte apenas à migração da versão 10,3 para o banco de dados do Azure para PostgreSQL. Estamos planejando dar suporte a versões mais recentes do PostgreSQL em breve.

- Para habilitar a replicação lógica no arquivo PostgreSQL **. conf de origem** , defina os seguintes parâmetros:
  - **wal_level** = logical
  - **max_replication_slots** = [número máximo de bancos de dados para migração]; Se você quiser migrar 4 bancos de dados, defina o valor como 4
  - **max_wal_senders** = [número de bancos de dados em execução simultânea]; o valor recomendado é 10
- Adicionar o IP do agente DMS à origem PostgreSQL pg_hba. conf
  1. Anote o endereço IP DMS depois de concluir o provisionamento de uma instância do DMS.
  2. Adicione o endereço IP ao arquivo pg_hba. conf, conforme mostrado:

        hospedar todas as 172.16.136.18/10 postgres de replicação de host MD5 172.16.136.18/10 MD5

- O usuário deve ter a permissão de superusuário no servidor que hospeda o banco de dados de origem
- Além de ter ENUM no esquema de banco de dados de origem, os esquemas de banco de dados de origem e de destino devem corresponder.
- O esquema no banco de dados do Azure de destino para PostgreSQL não deve ter chaves estrangeiras. Use a seguinte consulta para remover chaves estrangeiras:

    ```
                                SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ', 
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema, 
        tc.constraint_name as foreignkey, 
        tc.table_name as tableName, 
        kcu.column_name, 
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name 
    FROM 
        information_schema.table_constraints AS tc 
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    
    ```

    Execute o script de remoção de chave externa (que é a segunda coluna) no resultado da consulta.

- O esquema no banco de dados do Azure de destino para PostgreSQL não deve ter nenhum gatilho. Use o seguinte para desabilitar gatilhos no banco de dados de destino:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Limitações de DataType

- **Limitação**: Se houver um tipo de dados ENUM no banco de dados PostgreSQL de origem, a migração falhará durante a sincronização contínua.

    **Solução alternativa**: Modifique o tipo de dados ENUM para o caractere variável no banco de dados do Azure para PostgreSQL.

- **Limitação**: Se não houver nenhuma chave primária em tabelas, a sincronização contínua falhará.

    **Solução alternativa**: Defina temporariamente uma chave primária para a tabela para que a migração continue. Você pode remover a chave primária após a conclusão da migração de dados.

- **Limitação**: JSONB DataType não tem suporte para migração.

## <a name="lob-limitations"></a>Limitações de LOB

As colunas de LOB (objeto grande) são colunas que podem crescer muito. Para PostgreSQL, exemplos de tipos de dados LOB incluem XML, JSON, imagem, texto, etc.

- **Limitação**: Se os tipos de dados LOB forem usados como chaves primárias, a migração falhará.

    **Solução alternativa**: Substitua a chave primária por outros tipos de texto ou colunas que não são LOB.

- **Limitação**: Se o comprimento da coluna de objeto grande (LOB) for maior que 32 KB, os dados poderão ser truncados no destino. Você pode verificar o comprimento da coluna LOB usando esta consulta:

    ```
    SELECT max(length(cast(body as text))) as body FROM customer_mail
    ```

    **Solução alternativa**: Se você tiver um objeto LOB maior que 32 KB, entre em contato com a equipe de engenharia em [solicitar migrações de banco de dados do Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

- **Limitação**: Se houver colunas LOB na tabela e não houver nenhum conjunto de chaves primária para a tabela, os dados poderão não ser migrados para essa tabela.

    **Solução alternativa**: Defina temporariamente uma chave primária para a tabela para que a migração continue. Você pode remover a chave primária após a conclusão da migração de dados.

## <a name="postgresql10-workaround"></a>Solução alternativa de PostgreSQL10

O PostgreSQL 10. x faz várias alterações em nomes de pasta pg_xlog e, portanto, provocando a não execução da migração conforme o esperado. Se você estiver migrando do PostgreSQL 10. x para o banco de dados do Azure para PostgreSQL 10,3, execute o seguinte script no banco de dados PostgreSQL de origem para criar uma função de wrapper em volta das funções pg_xlog.

```
BEGIN;
CREATE SCHEMA IF NOT EXISTS fnRenames;
CREATE OR REPLACE FUNCTION fnRenames.pg_switch_xlog() RETURNS pg_lsn AS $$ 
   SELECT pg_switch_wal(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_pause() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_pause(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_resume() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_resume(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_is_xlog_replay_paused() RETURNS boolean AS $$ 
   SELECT pg_is_wal_replay_paused(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name(lsn pg_lsn) RETURNS TEXT AS $$ 
   SELECT pg_walfile_name(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_replay_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_replay_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_receive_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_receive_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_flush_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_flush_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_insert_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_insert_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_location_diff(lsn1 pg_lsn, lsn2 pg_lsn) RETURNS NUMERIC AS $$ 
   SELECT pg_wal_lsn_diff(lsn1, lsn2); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name_offset(lsn pg_lsn, OUT TEXT, OUT INTEGER) AS $$ 
   SELECT pg_walfile_name_offset(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_create_logical_replication_slot(slot_name name, plugin name, 
   temporary BOOLEAN DEFAULT FALSE, OUT slot_name name, OUT xlog_position pg_lsn) RETURNS RECORD AS $$ 
   SELECT slot_name::NAME, lsn::pg_lsn FROM pg_catalog.pg_create_logical_replication_slot(slot_name, plugin, 
   temporary); $$ LANGUAGE SQL;
ALTER USER PG_User SET search_path = fnRenames, pg_catalog, "$user", public;

-- DROP SCHEMA fnRenames CASCADE;
-- ALTER USER PG_User SET search_path TO DEFAULT;
COMMIT;
```

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Limitações ao migrar online do AWS RDS PostgreSQL

Ao tentar executar uma migração online do AWS RDS PostgreSQL para o banco de dados do Azure para PostgreSQL, você pode encontrar os erros a seguir.

- **Erro**: O Valor predefinido de coluna '{column}' na tabela '{table}' na base de dados '{database}' é diferente nos servidores de origem e de destino. É '{value on source}' na origem e '{value on target}' no destino.

  **Limitação**: Esse erro ocorre quando o valor padrão em um esquema de coluna é diferente entre os bancos de dados de origem e de destino.
  **Solução alternativa**: Verifique se o esquema no destino corresponde ao esquema na origem. Para obter detalhes sobre a migração de esquema, consulte a [documentação de migração online do PostgreSQL do Azure](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

- **Erro**: A base de dados de destino '{database}' tem '{number of tables}' tabelas, ao passo que a base de dados de origem '{database}' tem '{number of tables}' tabelas. O número de tabelas nas bases de dados de origem e de destino deve coincidir.

  **Limitação**: Esse erro ocorre quando o número de tabelas é diferente entre os bancos de dados de origem e de destino.
  **Solução alternativa**: Verifique se o esquema no destino corresponde ao esquema na origem. Para obter detalhes sobre a migração de esquema, consulte a [documentação de migração online do PostgreSQL do Azure](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

- **Erro:** O banco de dados de origem {Database} está vazio.

  **Limitação**: Esse erro ocorre quando o banco de dados de origem está vazio. Tal deve-se provavelmente devido ao facto de ter selecionado a base de dados incorreta como origem.
  **Solução alternativa**: Verifique novamente o banco de dados de origem que você selecionou para migração e tente novamente.

- **Erro:** O banco de dados de destino {Database} está vazio. Migre o esquema.

  **Limitação**: Esse erro ocorre quando não há nenhum esquema no banco de dados de destino. Verifique se o esquema no destino corresponde ao esquema na origem.
  **Solução alternativa**: Verifique se o esquema no destino corresponde ao esquema na origem. Para obter detalhes sobre a migração de esquema, consulte a [documentação de migração online do PostgreSQL do Azure](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

## <a name="other-limitations"></a>Outras limitações

- O nome do banco de dados não pode incluir um ponto-e-vírgula (;).
- Não há suporte para a cadeia de caracteres de senha que tem chaves de abertura e fechamento {}. Essa limitação se aplica tanto à conexão com o PostgreSQL de origem quanto ao banco de dados do Azure de destino para PostgreSQL.
- Uma tabela capturada deve ter uma chave primária. Se uma tabela não tiver uma chave primária, o resultado de operações de exclusão e registro de atualização será imprevisível.
- A atualização de um segmento de chave primária é ignorada. Nesses casos, a aplicação dessa atualização será identificada pelo destino como uma atualização que não atualizou nenhuma linha e resultará em um registro gravado na tabela de exceções.
- A migração de várias tabelas com o mesmo nome, mas com um caso diferente (por exemplo, Table1, TABELA1 e Table1) pode causar um comportamento imprevisível e, portanto, não tem suporte.
- Alterar o processamento de [criar | ALTERAR | DROP] as tabelas DDLs têm suporte a menos que sejam mantidas em um bloco de corpo de função/procedimento interno ou em outras construções aninhadas. Por exemplo, a seguinte alteração não será capturada:

    ```
    CREATE OR REPLACE FUNCTION pg.create_distributors1() RETURNS void
    LANGUAGE plpgsql
    AS $$
    BEGIN
    create table pg.distributors1(did serial PRIMARY KEY,name varchar(40)
    NOT NULL);
    END;
    $$;
    ```

- O processamento de alterações (sincronização contínua) de operações de TRUNCAmento não tem suporte. Não há suporte para a migração de tabelas particionadas. Quando uma tabela particionada é detectada, ocorrem as seguintes ações:

  - O banco de dados relatará uma lista de tabelas pai e filho.
  - A tabela será criada no destino como uma tabela regular com as mesmas propriedades das tabelas selecionadas.
  - Se a tabela pai no banco de dados de origem tiver o mesmo valor de chave primária que suas tabelas filho, um erro de "chave duplicada" será gerado.

- No DMS, o limite de bancos de dados a serem migrados em uma única atividade de migração é quatro.
