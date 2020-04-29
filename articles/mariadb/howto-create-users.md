---
title: Criar utilizadores - Base de Dados Azure para MariaDB
description: Este artigo descreve como pode criar novas contas de utilizador para interagir com uma Base de Dados Azure para o servidor MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/2/2020
ms.openlocfilehash: 1b79a49b2fb87ebf180aaaa40447f40c5a982c2e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80632292"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>Criar utilizadores no Azure Database for MariaDB 
Este artigo descreve como pode criar utilizadores na Base de Dados Azure para o MariaDB.

Quando criou a sua Base de Dados Azure para o MariaDB, forneceu um servidor de nome de utilizador de login e palavra-passe. Para mais informações, pode seguir o [Quickstart](quickstart-create-mariadb-server-database-using-azure-portal.md). Pode localizar o nome de utilizador de login do seu servidor a partir do portal Azure.

O utilizador do servidor obtém certos privilégios para o seu servidor como listado: SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER

Uma vez criada a Base de Dados Azure para o servidor MariaDB, pode utilizar a primeira conta de utilizador do servidor para criar utilizadores adicionais e conceder acesso à administração dos mesmos. Além disso, a conta de administração do servidor pode ser usada para criar utilizadores menos privilegiados que tenham acesso a esquemas de base de dados individuais.

> [!NOTE]
> O papel super privilegiado e dBA não são apoiados. Reveja os [privilégios](concepts-limits.md#privilege-support) no artigo de limitações para entender o que não é suportado no serviço.

## <a name="create-additional-admin-users"></a>Criar utilizadores adicionais de administração
1. Obtenha as informações de ligação e o nome do utilizador administrativo.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Pode facilmente encontrar o nome do servidor e informações de início de sessão a partir da página **'Overview'** do servidor ou da página **Propriedades** no portal Azure. 

2. Utilize a conta de administração e a palavra-passe para se ligar ao seu servidor de base de dados. Utilize a sua ferramenta de cliente preferida, como mySQL Workbench, mysql.exe, HeidiSQL, ou outros. 
   Se não tiver a certeza de como se conectar, consulte [Use MySQL Workbench para ligar e consultar dados](./connect-workbench.md)

3. Editar e executar o seguinte código SQL. Substitua o seu novo nome `new_master_user`de utilizador pelo valor do espaço reservado . Esta sintaxe concede os privilégios listados em todas as bases de dados (*.*) ao nome do utilizador (new_master_user neste exemplo). 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. Verifique as subvenções 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="create-database-users"></a>Criar utilizadores de bases de dados

1. Obtenha as informações de ligação e o nome do utilizador administrativo.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Pode facilmente encontrar o nome do servidor e informações de início de sessão a partir da página **'Overview'** do servidor ou da página **Propriedades** no portal Azure. 

2. Utilize a conta de administração e a palavra-passe para se ligar ao seu servidor de base de dados. Utilize a sua ferramenta de cliente preferida, como mySQL Workbench, mysql.exe, HeidiSQL, ou outros. 
   Se não tiver a certeza de como se conectar, consulte [Use MySQL Workbench para ligar e consultar dados](./connect-workbench.md)

3. Editar e executar o seguinte código SQL. Substitua o `db_user` valor do espaço reservado pelo seu `testdb` novo nome de utilizador pretendido e o valor do espaço reservado pelo seu próprio nome de base de dados.

   Esta sintaxe de código sql cria uma nova base de dados chamada testdb, por exemplo. Em seguida, cria um novo utilizador na Base de Dados Azure para o serviço MariaDB, e concede todos os privilégios à nova base de dados (testdb.\*) para esse utilizador. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. Verifique os subsídios dentro da base de dados.
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Inicie sessão no servidor, especificando a base de dados designada, utilizando o novo nome de utilizador e palavra-passe. Este exemplo mostra a linha de comando mysql. Com este comando, é solicitado a palavra-passe para o nome de utilizador. Substitua o nome do seu próprio servidor, nome da base de dados e nome do utilizador.

   ```bash
   mysql --host mydemoserver.mariadb.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
   Para obter mais informações sobre a gestão da conta de utilizador, consulte a documentação mariaDB para gestão da [conta utilizador,](https://mariadb.com/kb/en/library/user-account-management/) [GRANT Syntax](https://mariadb.com/kb/en/library/grant/)e [Privileges](https://mariadb.com/kb/en/library/grant/#privilege-levels).

## <a name="next-steps"></a>Passos seguintes
Abra a firewall para os endereços IP das máquinas dos novos utilizadores para que possam ligar: Criar e gerir a Base de Dados Azure para as regras de [firewall MariaDB utilizando o portal Azure](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->
