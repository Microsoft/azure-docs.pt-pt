---
title: 'Questões conhecidas: Migrações online de PostgreSQL para Base de Dados Azure para PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Conheça questões conhecidas e limitações de migração com migrações online de PostgreSQL para Azure Database para PostgreSQ utilizando o Serviço de Migração de Bases de Dados Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: ecc3075bbddd313e7c6471abef0d201a79cb87ec
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471368"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>Questões conhecidas/limitações de migração com migrações on-line de PostgreSQL para Azure DB para PostgreSQL

Questões e limitações conhecidas associadas às migrações online de PostgreSQL para Base de Dados Azure para PostgreSQL são descritas nas seguintes secções.

## <a name="online-migration-configuration"></a>Configuração de migração on-line

- O servidor de origem PostgreSQL deve estar a executar a versão 9.4, 9.5, 9.6, 10 ou 11. Para mais informações, consulte o artigo [Versões de Base de Dados PostgreSQL suportadas](../postgresql/concepts-supported-versions.md).
- Apenas são apoiadas migrações para a mesma versão ou uma versão mais alta. Por exemplo, a migração do PostgreSQL 9.5 para a Base de Dados Azure para PostgreSQL 9.6 ou 10 é suportada, mas a migração do PostgreSQL 11 para postgreSQL 9.6 não é suportada.
- Para permitir a replicação lógica no ficheiro **postgresql.conf de origem PostgreSQL,** defina os seguintes parâmetros:
  - **wal_level** = lógico
  - **max_replication_slots** = [pelo menos o número máximo de bases de dados para a migração]; se quiser migrar quatro bases de dados, detete o valor para pelo menos 4.
  - **max_wal_senders** = [número de bases de dados em funcionamento simultaneamente]; o valor recomendado é 10
- Adicione o agente DMS IP à fonte PostgreSQL pg_hba.conf
  1. Tome nota do endereço IP dMS depois de terminar o fornecimento de uma instância do Serviço de Migração de Bases de Dados Azure.
  2. Adicione o endereço IP ao ficheiro pg_hba.conf, conforme mostrado:

      ```
          host  all     172.16.136.18/10    md5
          host  replication postgres    172.16.136.18/10    md5
      ```

- O utilizador deve ter a função REPLICATION no servidor que acolhe a base de dados de origem.
- A base de dados de origem e alvo deve coincidir.
- O esquema na base de dados Azure-target para o servidor PostgreSQL-Single não deve ter chaves estrangeiras. Utilize a seguinte consulta para deixar cair chaves estrangeiras:

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

- O esquema na base de dados Azure-target para o servidor PostgreSQL-Single não deve ter gatilhos. Utilize o seguinte para desativar os gatilhos na base de dados do alvo:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Limitações do tipo de dados

  **Limitação**: Se não houver chave primária nas tabelas, as alterações podem não ser sincronizadas na base de dados do alvo.

  **Supor:** Estabeleça temporariamente uma chave primária para que a migração continue. Pode remover a chave principal após a migração de dados estar completa.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Limitações ao migrar on-line da AWS RDS PostgreSQL

Quando tentar realizar uma migração on-line de AWS RDS PostgreSQL para Azure Database para PostgreSQL, poderá encontrar os seguintes erros.

- **Erro**: O valor predefinido da coluna '{column}' na tabela '{table}' na base de dados '{database}' é diferente nos servidores de origem e alvo. É '{value on source}' na origem e '{value on target}' no destino.

  **Limitação**: Este erro ocorre quando o valor predefinido de um esquema de coluna é diferente entre as bases de dados de origem e alvo.
  **Supor:** Certifique-se de que o esquema no alvo corresponde ao esquema na fonte. Para obter detalhes sobre o esquema migratório, consulte a documentação de [migração on-line Azure PostgreSQL](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

- **Erro**: A base de dados do target '{database}' tem tabelas '{number of tables}' onde como base de dados de origem '{database}' tem tabelas '{number of tables}'. O número de tabelas nas bases de dados de origem e de destino deve coincidir.

  **Limitação**: Este erro ocorre quando o número de tabelas é diferente entre as bases de dados de origem e alvo.

  **Supor:** Certifique-se de que o esquema no alvo corresponde ao esquema na fonte. Para obter detalhes sobre o esquema migratório, consulte a documentação de [migração on-line Azure PostgreSQL](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

- **Erro:** A base de dados de origem {database} está vazia.

  **Limitação**: Este erro ocorre quando a base de dados de origem está vazia. É muito provável porque selecionou a base de dados errada como fonte.

  **Seleção**: Verifique duas vezes a base de dados de origem selecionada para a migração e tente novamente.

- **Erro:** A base de dados de destino {database} está vazia. Migre o esquema.

  **Limitação**: Este erro ocorre quando não há esquema na base de dados do alvo. Certifique-se de que o esquema no alvo corresponde ao esquema na fonte.
  **Supor:** Certifique-se de que o esquema no alvo corresponde ao esquema na fonte. Para obter detalhes sobre o esquema migratório, consulte a documentação de [migração on-line Azure PostgreSQL](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

## <a name="other-limitations"></a>Outras limitações

- O nome da base de dados não pode incluir um ponto e vírgula (;).
- Uma mesa capturada deve ter uma Chave Primária. Se uma tabela não tiver uma chave primária, o resultado das operações de registo DELETE e UPDATE será imprevisível.
- A atualização de um segmento de Chave Primária é ignorada. Nesses casos, a aplicação de tal atualização será identificada pelo alvo como uma atualização que não atualizou nenhuma linha e resultará num registo escrito à tabela de exceções.
- A migração de várias tabelas com o mesmo nome, mas um caso diferente (por exemplo, quadro1, TABLE1 e Quadro1) pode causar comportamentos imprevisíveis e, portanto, não é suportado.
- Alterar o processamento de [CREATE ] ALTER / GOTA / Os DDLs da tabela TRUNCATE não são suportados.
- No Serviço de Migração de Bases de Dados Azure, uma única atividade de migração só pode acomodar até quatro bases de dados.
