---
title: 'Questões conhecidas: Migrações online para Azure Database para o MySQL'
titleSuffix: Azure Database Migration Service
description: Conheça questões conhecidas e limitações de migração com migrações on-line para Azure Database for MySQL ao utilizar o Serviço de Migração de Bases de Dados Azure.
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
ms.openlocfilehash: a9ac4830d11aa3360a272ac1feb167eb20c26c9a
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962625"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>Questões de migração online & limitações ao Azure DB para o MySQL com o Azure Database Migration Service

Questões e limitações conhecidas associadas às migrações on-line do MySQL para a Azure Database para o MySQL são descritas nas seguintes secções.

## <a name="online-migration-configuration"></a>Configuração de migração on-line


- A versão fonte MySQL Server deve ser a versão 5.6.35, 5.7.18 ou posterior
- Azure Database for MySQL suporta:
  - Edição comunitária MySQL
  - Motor InnoDB
- Migração da mesma versão. A migração do MySQL 5.6 para a Base de Dados Azure para o MySQL 5.7 não é suportada. As migrações de ou para o MySQL 8.0 não são suportadas.
- Ative o registo binário em my.ini (Windows) ou my.cnf (Unix)
  - Coloque Server_id em qualquer número maior ou igual a 1, por exemplo, Server_id=1 (apenas para o MySQL 5.6)
  - Definir log-bin = \<path> (apenas para MySQL 5.6)
  - Definir binlog_format = linha
  - Expire_logs_days = 5 (recomendado - apenas para MySQL 5.6)
- O utilizador deve ter a função ReplicationAdmin.
- As colagens definidas para a base de dados MySQL de origem são as mesmas que as definidas na Base de Dados Azure alvo para o MySQL.
- O Esquema deve coincidir entre a base de dados MySQL de origem e a base de dados-alvo na Base de Dados Azure para o MySQL.
- A Schema na base de dados Azure para o MySQL não deve ter chaves estrangeiras. Utilize a seguinte consulta para deixar cair as teclas estrangeiras:
    ```
    SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
          KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
          AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['schema_name']) Queries
      GROUP BY SchemaName;
    ```

    Execute o script de remoção de chave externa (que é a segunda coluna) no resultado da consulta.
- O Schema na base de dados Azure para o MySQL não deve ter nenhum gatilho. Para lançar gatilhos na base de dados alvo:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Limitações do tipo de dados

- **Limitação**: Se houver um tipo de dados JSON na base de dados MySQL de origem, a migração falhará durante a sincronização contínua.

    **Solução alternativa**: Modificar o tipo de dados JSON para texto médio ou longotex na base de dados MySQL de origem.

- **Limitação:** Se não houver uma chave primária nas tabelas, a sincronização contínua falhará.

    **Solução alternativa**: Definir temporariamente uma chave primária para a mesa para a migração continuar. Pode remover a chave primária após a migração de dados estar completa.

## <a name="lob-limitations"></a>Limitações LOB

As colunas de objetos grandes (LOB) são colunas que podem crescer grandes em tamanho. Para MySQL, Texto Médio, Longtext, Blob, Mediumblob, Longblob, etc., são alguns dos tipos de dados do LOB.

- **Limitação**: Se os tipos de dados LOB forem utilizados como chaves primárias, a migração falhará.

    **Resumo :** Substitua a tecla primária por outros tipos de dados ou colunas que não sejam LOB.

- **Limitação**: Se o comprimento da coluna De Objeto Grande (LOB) for maior do que o parâmetro "Tamanho limite LOB" (não deve ser superior a 64 KB), os dados podem ser truncados no alvo. Pode verificar o comprimento da coluna LOB utilizando esta consulta:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Solução alternativa**: Se tiver um objeto LOB superior a 64 KB, utilize o parâmetro "Permitir tamanho LOB ilimitado". Note que as migrações utilizando o parâmetro "Permitir tamanho LOB ilimitado" serão mais lentas do que as migrações utilizando o parâmetro "Tamanho limite LOB".

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Limitações ao migrar on-line a partir do AWS RDS MySQL

Quando tentar efetuar uma migração on-line de AWS RDS MySQL para Azure Database para o MySQL, poderá encontrar os seguintes erros.

- **Erro:** A base de {0} dados ' tem chaves estrangeiras no alvo. Corrija o destino e inicie uma nova atividade de migração de dados. Execute abaixo o roteiro no alvo para listar as chaves estrangeiras(s)

  **Limitação**: Se tiver teclas estranhas no seu esquema, a carga inicial e a sincronização contínua da migração falharão.
  **Solução :** Execute o seguinte script na bancada mySQL para extrair o script de chave estrangeira gota e adicionar script de chave estrangeira:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Erro:** A base de {0} dados ' ' não existe no servidor. O servidor de origem MySQL fornecido diferencia maiúsculas de minúsculas. Verifique o nome da base de dados.

  **Limitação**: Ao migrar uma base de dados MySQL para Azure utilizando a Interface da Linha de Comando (CLI), os utilizadores podem atingir este erro. O serviço não conseguiu localizar a base de dados no servidor de origem, o que pode ser porque pode ter fornecido o nome de base de dados incorreto ou a base de dados não existe no servidor listado. Os nomes da base de dados de notas são sensíveis a maiôs.

  **Solução alternativa**: Forneça o nome exato da base de dados e tente novamente.

- **Erro:** Existem tabelas com o mesmo nome na base de dados '{database}'. A Base de Dados do Azure para MySQL não suporta tabelas sensíveis às maiúsculas e minúsculas.

  **Limitação:** Este erro acontece quando tem duas tabelas com o mesmo nome na base de dados de origem. A Azure Database for MySQL não suporta tabelas sensíveis a casos.

  **Solução :** Atualize os nomes da tabela para serem únicos e tente novamente.

- **Erro:** A base de dados-alvo {base de dados} está vazia. Migre o esquema.

  **Limitação:** Este erro ocorre quando a base de dados Azure alvo para a base de dados MySQL não tem o esquema necessário. A migração de esquemas é necessária para permitir a migração de dados para o seu alvo.

  **Solução alternativa:** [Migrar o esquema](./tutorial-mysql-azure-mysql-online.md#migrate-the-sample-schema) da sua base de dados de origem para a base de dados-alvo.

## <a name="other-limitations"></a>Outras limitações

- Uma cadeia de palavra-passe que tem suportes encaracolados de abertura e fecho { } no início e no fim da cadeia de palavra-passe não é suportada. Esta limitação aplica-se tanto à ligação à fonte MySQL como à Base de Dados Azure para o MySQL.
- Os seguintes DDLs não são suportados:
  - Todos os DDLs de partição
  - Mesa de lançamento
  - Mesa de renomeação
- Utilizando a *tabela alter <table_name> adicionar coluna <column_name>* declaração para adicionar colunas ao início ou ao meio de uma mesa não é suportado. THe *alterar a tabela <table_name> adicionar coluna <column_name>* adiciona a coluna na extremidade da tabela.
- Os índices criados apenas em parte dos dados da coluna não são suportados. A seguinte afirmação é um exemplo que cria um índice usando apenas uma parte dos dados da coluna:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- No Serviço de Migração da Base de Dados Azure, o limite das bases de dados para migrar numa única atividade migratória é de quatro.

- O Azure DMS não suporta a ação referencial CASCADE, que ajuda a eliminar ou atualizar automaticamente uma linha de correspondência na tabela infantil quando uma linha é eliminada ou atualizada na tabela dos pais. Para obter mais informações, na documentação do MySQL, consulte a secção Ações Referenciais do artigo [Restrições DE CHAVE ESTRANGEIRA](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html). O Azure DMS requer que deixe cair os constrangimentos de chaves estrangeiras no servidor de base de dados-alvo durante a carga inicial de dados, e não pode utilizar ações referenciais. Se a sua carga de trabalho depender da atualização de uma tabela de crianças relacionada através desta ação referencial, recomendamos que efetue uma [lixeira e restaure.](../mysql/concepts-migrate-dump-restore.md) 

- **Erro:** Tamanho da linha demasiado grande (> 8126). Alterar algumas colunas para SMS ou BLOB pode ajudar. No formato de linha atual, o prefixo BLOB de 0 bytes é armazenado em linha.

  **Limitação**: Este erro acontece quando está a migrar para a Base de Dados Azure para o MySQL utilizando o motor de armazenamento InnoDB e qualquer tamanho de fila de mesa é demasiado grande (>bytes 8126).

  **Resumo :** Atualizar o esquema da tabela que tem um tamanho de linha superior a 8126 bytes. Não recomendamos que se mude o modo rígido porque os dados serão truncados. Mudar a page_size não é apoiado.