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
ms.date: 11/08/2019
ms.openlocfilehash: 39c1928f1d38276418b2e1a3e766c4b9d8a0d8d2
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73902787"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-mysql"></a>Problemas conhecidos/limitações de migração com migrações online para o banco de BD do Azure para MySQL

Os problemas conhecidos e as limitações associadas a migrações online do MySQL para o banco de dados do Azure para MySQL são descritas nas seções a seguir.

## <a name="online-migration-configuration"></a>Configuração de migração online

- A versão do servidor MySQL de origem deve ser a versão 5.6.35, 5.7.18 ou posterior
- O banco de dados do Azure para MySQL dá suporte a:
  - MySQL Community Edition
  - Mecanismo de InnoDB
- Mesma migração de versão. Não há suporte para a migração do MySQL 5,6 para o banco de dados do Azure para MySQL 5,7.
- Habilitar log binário em meu. ini (Windows) ou My. cnf (Unix)
  - Defina Server_id para qualquer número maior ou igual a 1, por exemplo, Server_id = 1 (somente para MySQL 5,6)
  - Definir log-bin = \<caminho > (somente para MySQL 5,6)
  - Definir binlog_format = linha
  - Expire_logs_days = 5 (recomendado-somente para MySQL 5,6)
- O usuário deve ter a função ReplicationAdmin.
- Os agrupamentos definidos para o banco de dados MySQL de origem são os mesmos definidos no banco de dados do Azure de destino para MySQL.
- O esquema deve corresponder entre banco de dados MySQL de origem e banco de dados de destino no banco de dados do Azure para MySQL.
- O esquema no banco de dados do Azure de destino para MySQL não deve ter chaves estrangeiras. Use a seguinte consulta para remover chaves estrangeiras:
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
- O esquema no banco de dados do Azure de destino para MySQL não deve ter gatilhos. Para descartar gatilhos no banco de dados de destino:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Limitações de DataType

- **Limitação**: se houver um tipo de dados JSON no banco de dados MySQL de origem, a migração falhará durante a sincronização contínua.

    **Solução alternativa**: modifique o tipo de dados JSON para texto médio ou LONGTEXT no banco de dados MySQL de origem.

- **Limitação**: se não houver uma chave primária em tabelas, a sincronização contínua falhará.

    **Solução alternativa**: Defina temporariamente uma chave primária para a tabela para que a migração continue. Você pode remover a chave primária após a conclusão da migração de dados.

## <a name="lob-limitations"></a>Limitações de LOB

As colunas de objeto grande (LOB) são colunas que podem crescer em tamanho grande. Para MySQL, texto médio, longtext, BLOB, mediumblob, longblob, etc., são alguns dos tipos de texto de LOB.

- **Limitação**: se os tipos de dados LOB forem usados como chaves primárias, a migração falhará.

    **Solução alternativa**: substitua a chave primária por outros tipos de texto ou colunas que não são LOB.

- **Limitação**: se o comprimento da coluna de objeto grande (LOB) for maior que 32 KB, os dados poderão ser truncados no destino. Você pode verificar o comprimento da coluna LOB usando esta consulta:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Solução alternativa**: se você tiver um objeto LOB maior que 32 KB, contate a equipe de engenharia em [solicitar migrações de banco de dados do Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Limitações ao migrar online do AWS RDS MySQL

Ao tentar executar uma migração online do AWS RDS MySQL para o banco de dados do Azure para MySQL, você pode vir os erros a seguir.

- **Erro:** O banco de dados '{0}' tem chave (s) estrangeira (ões) no destino. Corrija o destino e inicie uma nova atividade de migração de dados. Execute o script abaixo no destino para listar as chaves estrangeiras

  **Limitação**: se você tiver chaves estrangeiras em seu esquema, a carga inicial e a sincronização contínua da migração falharão.
  **Solução alternativa**: execute o seguinte script no MySQL Workbench para extrair o script de remoção de chave estrangeira e adicionar script de chave estrangeira:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Erro:** O banco de dados '{0}' não existe no servidor. O servidor de origem MySQL fornecido diferencia maiúsculas de minúsculas. Verifique o nome da base de dados.

  **Limitação**: ao migrar um banco de dados MySQL para o Azure usando a CLI (interface de linha de comando), os usuários poderão atingir esse erro. O serviço não pôde localizar o banco de dados no servidor de origem, o que pode ser porque você pode ter fornecido um nome de banco de dados incorreto ou o banco de dados não existe no servidor listado. Observação os nomes de banco de dados diferenciam maiúsculas de minúsculas.

  **Solução alternativa**: forneça o nome exato do banco de dados e tente novamente.

- **Erro:** Há tabelas com o mesmo nome no banco de dados ' {database} '. A Base de Dados do Azure para MySQL não suporta tabelas sensíveis às maiúsculas e minúsculas.

  **Limitação**: esse erro ocorre quando você tem duas tabelas com o mesmo nome no banco de dados de origem. O banco de dados do Azure para MySQL não dá suporte a tabelas com diferenciação de maiúsculas

  **Solução alternativa**: atualize os nomes de tabela para serem exclusivos e tente novamente.

- **Erro:** O banco de dados de destino {Database} está vazio. Migre o esquema.

  **Limitação**: esse erro ocorre quando o banco de dados de destino do banco de dados do Azure para MySQL não tem o esquema necessário. A migração de esquema é necessária para habilitar a migração de dados para o destino.

  **Solução alternativa**: [migre o esquema](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#migrate-the-sample-schema) do banco de dados de origem para o banco de dados de destino.

## <a name="other-limitations"></a>Outras limitações

- Não há suporte para uma cadeia de caracteres de senha que tenha chaves de abertura e fechamento {} no início e no final da cadeia de caracteres de senha. Essa limitação se aplica a ambas se conectar ao MySQL de origem e ao banco de dados do Azure de destino para MySQL.
- Não há suporte para os seguintes DDLs:
  - Todas as DDLs de partição
  - Remover tabela
  - Renomear tabela
- Usando a instrução *alter table < table_name > adicionar coluna < column_name* > para adicionar colunas ao início ou ao meio de uma tabela não é suportada. A instrução *alter table < table_name > adicionar coluna < column_name >* adiciona a coluna no final da tabela.
- Não há suporte para índices criados somente em parte dos dados da coluna. A instrução a seguir é um exemplo que cria um índice usando apenas parte dos dados da coluna:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- No Azure Database Migration Service, o limite de bancos de dados a serem migrados em uma única atividade de migração é de quatro.

- **Erro:** Tamanho de linha muito grande (> 8126). A alteração de algumas colunas para texto ou BLOB pode ajudar. No formato de linha atual, o prefixo de BLOB de 0 bytes é armazenado embutido.

  **Limitação**: esse erro ocorre quando você está migrando para o banco de dados do Azure para MySQL usando o mecanismo de armazenamento InnoDB e qualquer tamanho de linha de tabela é muito grande (> 8126 bytes).

  **Solução alternativa**: Atualize o esquema da tabela que tem um tamanho de linha maior que 8126 bytes. Não é recomendável alterar o modo estrito porque os dados serão truncados. Não há suporte para a alteração do page_size.
