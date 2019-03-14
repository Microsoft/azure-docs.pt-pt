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
ms.openlocfilehash: 75549302ca5d942a8a56cd9f72bc34297078a1e1
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57790820"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-mysql"></a>Limitações de migração/problemas conhecidos com migrações online BD do Azure para MySQL

Problemas conhecidos e limitações associadas a migrações online do MySQL para a base de dados do Azure para MySQL são descritas nas seções a seguir. 

## <a name="online-migration-configuration"></a>Configuração de migração online
- A versão do MySQL Server de origem tem de ser a versão 5.6.35, 5.7.18 ou posterior
- Suporta a base de dados do Azure para MySQL:
    - Edição de Comunidade do MySQL
    - Motor InnoDB
- Migração de versões do mesmo. Migrando MySQL 5.6 para a base de dados do Azure para MySQL 5.7 não é suportada.
- Ativar o registo binário em My. ini (Windows) ou cnf (Unix)
    - Definir Server_id para qualquer número maior ou igual a 1, por exemplo, Server_id = 1 (apenas para o MySQL 5.6)
    - Definir log-bin = <path> (apenas para o MySQL 5.6)
    - Definir binlog_format = linha
    - Expire_logs_days = 5 (recomendado - apenas para o MySQL 5.6)
- Utilizador tem de ter a função ReplicationAdmin.
- Agrupamentos definidos para a base de dados do MySQL de origem são os mesmos que os que definiu no destino da base de dados do Azure para MySQL.
- Esquema tem de corresponder entre a base de dados de origem MySQL e base de dados de destino na base de dados do Azure para MySQL.
- Esquema na base de dados do Azure para MySQL destino não tem de ter as chaves estrangeiras. Utilize a seguinte consulta para remover chaves externas:
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
- Esquema na base de dados do Azure para MySQL destino não pode ter qualquer gatilhos. Para colocar acionadores na base de dados de destino:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Limitações de tipo de dados
- **Limitação**: Se houver um tipo de dados JSON à base de dados MySQL, migração irá falhar durante a sincronização contínua.

    **Solução**: Modificar o tipo de dados JSON para texto médio ou longtext base de dados MySQL.

- **Limitação**: Se não houver nenhuma chave primária em tabelas, a sincronização contínua irá falhar.
 
    **Solução**: Defina temporariamente uma chave primária para a tabela para a migração continuar. Pode remover a chave primária após a conclusão da migração de dados.

## <a name="lob-limitations"></a>Limitações de LOB
Grandes colunas de objeto (LOB) são colunas que poderiam ter um aumento de tamanho grandes. Para o MySQL, texto médio, Longtext, BLOBs, Mediumblob, Longblob, etc. são alguns dos tipos de dados do LOB.

- **Limitação**: Se os tipos de dados LOB são utilizados como chaves primárias, a migração falhará.

    **Solução**: Substitua a chave primária com outros tipos de dados ou colunas que não são LOB.

- **Limitação**: Se o comprimento da coluna de objeto grande (LOB) é maior do que 32 KB, dados podem ser truncados no destino. Pode verificar o comprimento de coluna LOB usando esta consulta:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Solução**: Se tiver o objeto LOB que é maior do que 32 KB, contacte a equipa de engenharia na [faça migrações de base de dados do Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com). 

## <a name="other-limitations"></a>Outras limitações
- Uma cadeia de caracteres de palavra-passe que tenha chavetas {chavetas} no início e fim da cadeia de palavra-passe não é suportada. Esta limitação aplica-se para ambas as ligar à origem MySQL e o destino da base de dados do Azure para MySQL.
- Os DDLs seguintes não são suportadas:
    - Todas as partições DDLs
    - Remover tabela
    - Mudar o nome de tabela
- Utilizar o *alterar tabela < nome_tabela > Adicionar coluna < column_name >* instrução para adicionar colunas para o início ou até o meio de uma tabela não é suportada. O *alterar tabela < nome_tabela > Adicionar coluna < column_name >* adiciona a coluna no final da tabela.
- Não são suportados índices criados apenas uma parte dos dados da coluna. A seguinte instrução é um exemplo que cria um índice utilizando apenas uma parte dos dados de coluna:
    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- O DMS, o limite de bases de dados para migrar na atividade de uma migração única é quatro.
