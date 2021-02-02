---
title: 'Questões conhecidas: Migrações online de PostgreSQL para Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Conheça questões conhecidas e limitações de migração com migrações on-line de PostgreSQL a Azure Database for PostgreSQL utilizando o Serviço de Migração da Base de Dados Azure.
services: database-migration
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: fdefcabdda64402610f115832976ec9f7af81b80
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258834"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>Questões conhecidas/limitações de migração com migrações on-line de PostgreSQL a Azure DB para PostgreSQL

Questões e limitações conhecidas associadas às migrações online de PostgreSQL a Azure Database for PostgreSQL são descritas nas seguintes secções.

## <a name="online-migration-configuration"></a>Configuração de migração on-line

- O servidor PostgreSQL de origem deve estar a executar a versão 9.4, 9.5, 9.6, 10 ou 11. Para obter mais informações, consulte o artigo [Versões postgresQL suportadas na base de dados.](../postgresql/concepts-supported-versions.md)
- Apenas as migrações para a mesma versão ou uma versão superior são suportadas. Por exemplo, a migração da Base de Dados PostgreSQL 9.5 para Azure para postgreSQL 9.6 ou 10 é suportada, mas migrar de PostgreSQL 11 para PostgreSQL 9.6 não é suportado.
- Para permitir a replicação lógica no ficheiro **postgresql.conf de origem,** desafie os seguintes parâmetros:
  - **wal_level** = lógica
  - **max_replication_slots** = [número máximo de bases de dados para a migração]; se quiser migrar quatro bases de dados, desa um valor para pelo menos 4.
  - **max_wal_senders** = [número de bases de dados em execução simultânea]; o valor recomendado é 10
- Adicione o ip do agente DMS à fonte PostgreSQL pg_hba.conf
  1. Tome nota do endereço IP DMS depois de terminar de apresentando uma instância do Serviço de Migração da Base de Dados Azure.
  2. Adicione o endereço IP ao ficheiro pg_hba.conf como mostrado:

      ```
          host    all    172.16.136.18/10    md5
          host    replication postgres    172.16.136.18/10     md5
      ```

- O utilizador deve ter a função REPLICAÇÃO no servidor que hospeda a base de dados de origem.
- Os esquemas de base de dados de origem e alvo devem coincidir.
- O esquema na base de dados Azure alvo para PostgreSQL-Single servidor não deve ter chaves estrangeiras. Utilize a seguinte consulta para deixar cair as teclas estrangeiras:

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

- O esquema na Base de Dados Azure alvo para PostgreSQL-Single servidor não deve ter nenhum gatilho. Utilize o seguinte para desativar os gatilhos na base de dados-alvo:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="size-limitations"></a>Limitações de tamanho
- Pode migrar até 2 TB de dados de PostgreSQL para Azure DB para PostgreSQL utilizando um único serviço DMS.
## <a name="datatype-limitations"></a>Limitações do tipo de dados

  **Limitação**: Se não houver uma chave primária nas tabelas, as alterações podem não ser sincronizadas na base de dados-alvo.

  **Solução alternativa**: Definir temporariamente uma chave primária para a mesa para a migração continuar. Pode remover a chave primária após a migração de dados estar completa.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Limitações ao migrar on-line a partir do AWS RDS PostgreSQL

Quando tentar efetuar uma migração on-line de AWS RDSQL para Azure Database for PostgreSQL, poderá encontrar os seguintes erros.

- **Erro**: O Valor predefinido de coluna '{column}' na tabela '{table}' na base de dados '{database}' é diferente nos servidores de origem e de destino. É '{value on source}' na origem e '{value on target}' no destino.

  **Limitação:** Este erro ocorre quando o valor predefinido num esquema de coluna é diferente entre as bases de dados de origem e alvo.
  **Solução alternativa**: Certifique-se de que o esquema no esquema do alvo corresponde ao esquema da fonte. Para obter detalhes sobre o esquema migratório, consulte a [documentação de migração online Azure PostgreSQL](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema).

- **Erro**: A base de dados de destino '{database}' tem '{number of tables}' tabelas, ao passo que a base de dados de origem '{database}' tem '{number of tables}' tabelas. O número de tabelas nas bases de dados de origem e de destino deve coincidir.

  **Limitação:** Este erro ocorre quando o número de tabelas é diferente entre as bases de dados de origem e alvo.

  **Solução alternativa**: Certifique-se de que o esquema no esquema do alvo corresponde ao esquema da fonte. Para obter detalhes sobre o esquema migratório, consulte a [documentação de migração online Azure PostgreSQL](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema).

- **Erro:** A base de dados de origem {base de dados} está vazia.

  **Limitação:** Este erro ocorre quando a base de dados de origem está vazia. É muito provável que tenha selecionado a base de dados errada como fonte.

  **Solução alternativa:** Verifique novamente a base de dados de origem selecionada para a migração e, em seguida, tente novamente.

- **Erro:** A base de dados-alvo {base de dados} está vazia. Migre o esquema.

  **Limitação:** Este erro ocorre quando não há esquema na base de dados-alvo. Certifique-se de que o esquema no alvo corresponde ao esquema da fonte.
  **Solução alternativa**: Certifique-se de que o esquema no esquema do alvo corresponde ao esquema da fonte. Para obter detalhes sobre o esquema migratório, consulte a [documentação de migração online Azure PostgreSQL](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema).

## <a name="other-limitations"></a>Outras limitações

- O nome da base de dados não pode incluir um ponto e vírgula (;).
- Uma mesa capturada deve ter uma chave primária. Se uma tabela não tiver uma chave primária, o resultado das operações de gravação DELETE e UPDATE será imprevisível.
- A atualização de um segmento de Chave Primária é ignorada. Nesses casos, a aplicação de tal atualização será identificada pelo alvo como uma atualização que não atualizou nenhuma linha e resultará num registo escrito na tabela de exceções.
- A migração de várias tabelas com o mesmo nome, mas um caso diferente (por exemplo, quadro1, TABLE1 e Quadro1) pode causar comportamentos imprevisíveis e, portanto, não é suportado.
- Alterar o processamento de [CREATE | ALTER | | DROP Os DDLs de mesa não são suportados.
- No Serviço de Migração da Base de Dados Azure, uma única atividade migratória só pode acomodar até quatro bases de dados.
- A migração da mesa pg_largeobject não é apoiada. 
