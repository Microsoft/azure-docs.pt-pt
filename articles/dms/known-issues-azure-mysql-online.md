---
title: 'Questões conhecidas: Migrações online para base de dados Azure para MySQL'
titleSuffix: Azure Database Migration Service
description: Conheça questões conhecidas e limitações de migração com migrações online para a Base de Dados Azure para mySQL ao utilizar o Serviço de Migração de Bases de Dados Azure.
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
ms.date: 02/20/2020
ms.openlocfilehash: 8c3de28ea934302086a5b14e61482e6a4ab9a7ca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80235283"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>Problemas de migração online & limitações ao Azure DB para o MySQL com o Serviço de Migração da Base de Dados Azure

Questões e limitações conhecidas associadas às migrações online do MySQL para a Base de Dados Azure para mySQL são descritas nas seguintes secções.

## <a name="online-migration-configuration"></a>Configuração de migração on-line


- A versão de origem MySQL Server deve ser a versão 5.6.35, 5.7.18 ou mais tarde
- Suportes azure Database para MySQL:
  - Edição comunitária MySQL
  - Motor InnoDB
- Migração da mesma versão. A migração do MySQL 5.6 para a Base de Dados Azure para MySQL 5.7 não é suportada.
- Ativar a exploração de registo binário no meu.ini (Windows) ou my.cnf (Unix)
  - Definir Server_id a qualquer número maior ou igual a 1, por exemplo, Server_id=1 (apenas para MySQL 5.6)
  - Definir log-bin \<= caminho> (apenas para MySQL 5.6)
  - Definir binlog_format = linha
  - Expire_logs_days = 5 (recomendado - apenas para MySQL 5.6)
- O utilizador deve ter a função ReplicationAdmin.
- As colagens definidas para a base de dados MySQL de origem são as mesmas que as definidas na base de dados azure-alvo para o MySQL.
- O Schema deve coincidir entre a base de dados mySQL de origem e a base de dados de alvos na Base de Dados Azure para o MySQL.
- Schema na base de dados azure-alvo para mySQL não deve ter chaves estrangeiras. Utilize a seguinte consulta para deixar cair chaves estrangeiras:
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
- Schema na base de dados azure-alvo para mySQL não deve ter nenhum gatilho. Para deixar cair os gatilhos na base de dados do alvo:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Limitações do tipo de dados

- **Limitação**: Se houver um tipo de dados JSON na base de dados MySQL de origem, a migração falhará durante o sincronizado contínuo.

    **Supor :** Modificar o tipo de dados JSON para texto médio ou texto longo na base de dados MySQL de origem.

- **Limitação**: Se não houver chave primária nas tabelas, a sincronização contínua falhará.

    **Supor:** Estabeleça temporariamente uma chave primária para que a migração continue. Pode remover a chave principal após a migração de dados estar completa.

## <a name="lob-limitations"></a>Limitações lob

Colunas de objetogrande (LOB) são colunas que podem crescer em tamanho grande. Para MySQL, Texto Médio, Longtext, Blob, Mediumblob, Longblob, etc., são alguns dos tipos de dados de LOB.

- **Limitação**: Se os tipos de dados lob forem utilizados como chaves primárias, a migração falhará.

    **Sucinta :** Substitua a chave primária por outros tipos de dados ou colunas que não sejam LOB.

- **Limitação**: Se o comprimento da coluna de objectogrande (LOB) for superior a 32 KB, os dados podem ser truncados no alvo. Pode verificar o comprimento da coluna LOB utilizando esta consulta:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Suposição :** Se tiver um objeto LOB superior a 32 KB, contacte a equipa de engenharia da [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Limitações ao migrar on-line a partir de AWS RDS MySQL

Quando tentar realizar uma migração on-line de AWS RDS MySQL para Azure Database para MySQL, poderá encontrar os seguintes erros.

- **Erro:** A{0}base de dados ' tem chaves estrangeiras no alvo. Corrija o destino e inicie uma nova atividade de migração de dados. Executar abaixo o script no alvo para listar as chaves estrangeiras(s)

  **Limitação**: Se tiver chaves estrangeiras no seu esquema, a carga inicial e o sincronizado contínuo da migração falharão.
  **Seleção**: Execute o seguinte script na bancada mySQL para extrair o script de chave estrangeira drop e adicionar script chave estrangeira:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Erro:** A{0}base de dados ' ' não existe no servidor. O servidor de origem MySQL fornecido diferencia maiúsculas de minúsculas. Verifique o nome da base de dados.

  **Limitação**: Ao migrar uma base de dados MySQL para Azure utilizando a Interface da Linha de Comando (CLI), os utilizadores podem ter atingido este erro. O serviço não conseguiu localizar a base de dados do servidor de origem, o que pode ser porque pode ter fornecido um nome de base de dados incorreto ou a base de dados não existe no servidor listado. Os nomes das bases de dados são sensíveis aos casos.

  **Seleção**: Forneça o nome exato da base de dados e tente novamente.

- **Erro:** Existem tabelas com o mesmo nome na base de dados '{database}'. A Base de Dados do Azure para MySQL não suporta tabelas sensíveis às maiúsculas e minúsculas.

  **Limitação**: Este erro acontece quando tem duas tabelas com o mesmo nome na base de dados de origem. A Base de Dados Azure para mySQL não suporta tabelas sensíveis a casos.

  **Sinuoso**: Atualize os nomes da tabela para serem únicos e, em seguida, tente novamente.

- **Erro:** A base de dados de destino {database} está vazia. Migre o esquema.

  **Limitação**: Este erro ocorre quando a base de dados azure-alvo para base de dados MySQL não tem o esquema necessário. A migração de schema é necessária para permitir a migração de dados para o seu alvo.

  **Seleção**: [Emigrar o esquema](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#migrate-the-sample-schema) da sua base de dados de origem para a base de dados do alvo.

## <a name="other-limitations"></a>Outras limitações

- Não é suportada uma cadeia de palavra-passe que tenha suportes encaracolados de abertura e fecho { } no início e no fim da cadeia de palavra-passe. Esta limitação aplica-se tanto à ligação com a fonte MySQL como à base de dados azure target para mySQL.
- Os seguintes DDLs não são suportados:
  - Todos os DDLs de partição
  - Mesa de lançamento
  - Tabela de renome
- A utilização da *tabela de alterações <table_name> adicionar coluna <column_name>* declaração para adicionar colunas ao início ou ao meio de uma mesa não é suportada. THe *altertabela <table_name> adicionar coluna <column_name>* adiciona a coluna na extremidade da mesa.
- Os índices criados apenas em parte dos dados da coluna não são suportados. A seguinte afirmação é um exemplo que cria um índice utilizando apenas uma parte dos dados da coluna:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- No Serviço de Migração de Bases de Dados Azure, o limite das bases de dados para migrar numa única atividade migratória é de quatro.

- **Erro:** Tamanho da linha muito grande (> 8126). Mudar algumas colunas para Texto ou BLOB pode ajudar. No formato atual da linha, o prefixo BLOB de 0 bytes é armazenado em linha.

  **Limitação**: Este erro ocorre quando se está a migrar para a Base de Dados Azure para o MySQL utilizando o motor de armazenamento InnoDB e qualquer tamanho de linha de mesa é demasiado grande (>8126 bytes).

  **Supor**: Atualize o esquema da tabela com um tamanho de linha superior a 8126 bytes. Não recomendamos alterar o modo rigoroso porque os dados serão truncados. Mudar a page_size não é suportado.
