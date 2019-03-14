---
title: Artigo sobre as limitações de migração/problemas conhecidos com as migrações de online para a base de dados do Azure para MySQL | Documentos da Microsoft
description: Saiba mais sobre as limitações de migração/problemas conhecidos com as migrações de online para a base de dados do Azure para MySQL.
services: database-migration
author: HJToland3
ms.author: scphang
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 5cca15e609a723870be7a564bf61fcd0d88e5706
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57790548"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-postgresql"></a>Limitações de migração/problemas conhecidos com migrações online BD do Azure para PostgreSQL

Problemas conhecidos e limitações associadas a migrações online, do PostgreSQL, para a base de dados do Azure para PostgreSQL são descritas nas seções a seguir. 

## <a name="online-migration-configuration"></a>Configuração de migração online
- A servidor PostgreSQL de origem tem de executar a versão 9.5.11, 9.6.7 ou 10.3 ou posterior. Para obter mais informações, consulte o artigo [versões da base de dados PostgreSQL](../postgresql/concepts-supported-versions.md).
- Apenas a mesma versão as migrações são suportadas. Por exemplo, PostgreSQL migrar 9.5.11 à base de dados do Azure para PostgreSQL 9.6.7 não é suportada.
- Para ativar a replicação de lógica na **da origem de PostgreSQL postgresql.conf** de ficheiros, defina os seguintes parâmetros:
    - **wal_level** = logical
    - **max_replication_slots** = [o número máximo de bases de dados para a migração]; se pretender migrar 4 bases de dados, defina o valor como 4
    - **max_wal_senders** = [número de bases de dados em execução em simultâneo]; o valor recomendado é 10
- Adicionar IP de agente do DMS ao pg_hba de PostgresSQL origem
    1. Tome nota do endereço IP do DMS, depois de concluir o aprovisionamento de uma instância do DMS.
    2. Adicione o endereço IP para o ficheiro de pg_hba, conforme mostrado:

        todos os 172.16.136.18/10 md5 anfitrião replicação postgres 172.16.136.18/10 md5 de anfitrião

- O utilizador tem de ter a permissão de Superutilizador no servidor a alojar a base de dados de origem
- Além de ter o ENUM no esquema de base de dados de origem, os esquemas de banco de dados de origem e destino têm de corresponder.
- O esquema no destino da base de dados do Azure para PostgreSQL não tem de ter as chaves estrangeiras. Utilize a seguinte consulta para remover chaves externas:

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

- O esquema na base de dados do Azure para PostgreSQL destino não pode ter qualquer gatilhos. Utilize o seguinte para desativar disparadores no banco de dados de destino:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Limitações de tipo de dados

- **Limitação**: Se houver um tipo de dados de Enumeração à base de dados PostgreSQL, a migração irá falhar durante a sincronização contínua.

    **Solução**: Modificar o tipo de dados de Enumeração para caráter variados na base de dados do Azure para PostgreSQL.

- **Limitação**: Se não houver nenhuma chave primária em tabelas, a sincronização contínua irá falhar.

    **Solução**: Defina temporariamente uma chave primária para a tabela para a migração continuar. Pode remover a chave primária após a conclusão da migração de dados.

## <a name="lob-limitations"></a>Limitações de LOB
Grandes colunas de objeto (LOB) são colunas que podem crescer grandes. Para o PostgreSQL, tipos de dados LOB exemplos de XML, JSON, imagem, texto, etc.

- **Limitação**: Se os tipos de dados LOB são utilizados como chaves primárias, a migração falhará.

    **Solução**: Substitua a chave primária com outros tipos de dados ou colunas que não são LOB.

- **Limitação**: Se o comprimento da coluna de objeto grande (LOB) é maior do que 32 KB, dados podem ser truncados no destino. Pode verificar o comprimento de coluna LOB usando esta consulta:

    ```
    SELECT max(length(cast(body as text))) as body FROM customer_mail
    ```

    **Solução**: Se tiver o objeto LOB que é maior do que 32 KB, contacte a equipa de engenharia na [faça migrações de base de dados do Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

- **Limitação**: Se não existem colunas LOB na tabela e não existe nenhum conjunto de chaves primário para a tabela, os dados não podem ser migrados para esta tabela.

    **Solução**: Defina temporariamente uma chave primária para a tabela para a migração continuar. Pode remover a chave primária após a conclusão da migração de dados.

## <a name="postgresql10-workaround"></a>PostgreSQL10 workaround
PostgreSQL 10.x faz alterações vários nomes de pastas de pg_xlog e, por conseguinte, fazendo com que a migração não está em execução conforme esperado. Se estiver migrando do PostgreSQL 10.x à base de dados do Azure para PostgreSQL 10.3, execute o seguinte script na base de dados do PostgreSQL origem para criar a função de invólucro em torno das funções de pg_xlog.

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

## <a name="other-limitations"></a>Outras limitações
- O nome de base de dados não pode incluir uma ponto e vírgula (;).
- Cadeia de palavra-passe que tenha chavetas {chavetas} não é suportada. Esta limitação aplica-se para ambas as ligar à origem PostgreSQL e o destino da base de dados do Azure para PostgreSQL.
- Uma tabela capturada tem de ter uma chave primária. Se uma tabela não tiver uma chave primária, o resultado de operações de registo DELETE e UPDATE será imprevisível.
- A atualizar um segmento de chave primária é ignorada. Nesses casos, aplicar uma atualização desse tipo será identificado pelo destino como uma atualização que não atualizar quaisquer linhas e irá resultar num registo de escrita para a tabela de exceções.
- Migração de várias tabelas com o mesmo nome, mas um outro caso (por exemplo, table1, TABLE1 e como Table1) pode causar um comportamento imprevisível e, portanto, não é suportada.
- Alterar o processamento de [criar | ALTER | LARGUE] tabela DDLs são suportadas, a menos que eles são mantidos num bloco de corpo do procedimento/função interna ou em outras construções aninhadas. Por exemplo, não será possível capturar a seguinte alteração:

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

- Não é suportado o processamento de alteração (sincronização contínua) de operações TRUNCADOS. Não é suportada a migração de tabelas particionadas. Quando é detetada uma tabela particionada, ocorrem os seguintes procedimentos:
    - A base de dados irá reportar uma lista de tabelas principais e subordinados.
    - A tabela será criada no destino como uma tabela normal com as mesmas propriedades como as tabelas selecionadas.
    - Se a tabela principal da base de dados de origem tem o mesmo valor de chave primária como suas tabelas filho, será gerado um erro de "chave duplicada".
- O DMS, o limite de bases de dados para migrar na atividade de uma migração única é quatro.