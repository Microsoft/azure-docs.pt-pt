---
title: Criar utilizadores - Base de Dados Azure para MySQL
description: Este artigo descreve como pode criar novas contas de utilizador para interagir com uma Base de Dados Azure para o servidor MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: e76e63030cc8e10c857d361cca69e1d35ba8c2c1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269409"
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Criar utilizadores na Base de Dados Azure para servidor MySQL 
Este artigo descreve como pode criar utilizadores numa Base de Dados Azure para servidor MySQL.

Quando criou a sua Base de Dados Azure para o MySQL, forneceu um servidor de nome de utilizador de login e palavra-passe. Para mais informações, pode seguir o [Quickstart](quickstart-create-mysql-server-database-using-azure-portal.md). Pode localizar o nome de utilizador de login do seu servidor a partir do portal Azure.

O utilizador do servidor obtém certos privilégios para o seu servidor como listado: SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENTE, CRIAR VISTA, MOSTRAR VISTA, CRIAR ROTINA, ALTERAR ROTINA, CRIAR UTILIZADOR, EVENTO, GATILHO

Assim que for criada a Base de Dados Azure para o servidor MySQL, pode utilizar a primeira conta de utilizador do servidor para criar utilizadores adicionais e conceder acesso à administração dos mesmos. Além disso, a conta de administração do servidor pode ser usada para criar utilizadores menos privilegiados que tenham acesso a esquemas de base de dados individuais.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Como criar utilizadores adicionais de administração na Base de Dados Azure para mySQL
1. Obtenha as informações de ligação e o nome do utilizador administrativo.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Pode facilmente encontrar o nome do servidor e informações de início de sessão a partir da página **'Overview'** do servidor ou da página **Propriedades** no portal Azure. 

2. Utilize a conta de administração e a palavra-passe para se ligar ao seu servidor de base de dados. Utilize a sua ferramenta de cliente preferida, como mySQL Workbench, mysql.exe, HeidiSQL, ou outros. 
   Se não tiver a certeza de como se conectar, consulte [Use MySQL Workbench para ligar e consultar dados](./connect-workbench.md)

3. Editar e executar o seguinte código SQL. Substitua o seu novo nome de utilizador pelo valor do espaço reservado `new_master_user`. Esta sintaxe concede os privilégios listados em todas as bases de dados ( *.* ) ao nome do utilizador (new_master_user neste exemplo). 

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

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Como criar utilizadores de base de dados na Base de Dados Azure para MySQL

1. Obtenha as informações de ligação e o nome do utilizador administrativo.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Pode facilmente encontrar o nome do servidor e informações de início de sessão a partir da página **'Overview'** do servidor ou da página **Propriedades** no portal Azure. 

2. Utilize a conta de administração e a palavra-passe para se ligar ao seu servidor de base de dados. Utilize a sua ferramenta de cliente preferida, como mySQL Workbench, mysql.exe, HeidiSQL, ou outros. 
   Se não tiver a certeza de como se conectar, consulte [Use MySQL Workbench para ligar e consultar dados](./connect-workbench.md)

3. Editar e executar o seguinte código SQL. Substitua o valor do espaço reservado `db_user` pelo seu novo nome de utilizador pretendido e o valor do espaço reservado `testdb` pelo seu próprio nome de base de dados.

   Esta sintaxe de código sql cria uma nova base de dados chamada testdb, por exemplo. Em seguida, cria um novo utilizador no serviço MySQL, e concede todos os privilégios à nova base de dados (testdb.\*) para esse utilizador. 

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

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>Passos seguintes
Abra a firewall para os endereços IP das máquinas dos novos utilizadores para que possam ligar: Criar e gerir a Base de Dados Azure para as regras de [firewall MySQL utilizando o portal Azure](howto-manage-firewall-using-portal.md) ou [o Azure CLI](howto-manage-firewall-using-cli.md).

Para obter mais informações sobre a gestão da conta de utilizador, consulte a documentação do produto MySQL para [a gestão](https://dev.mysql.com/doc/refman/5.7/en/user-account-management.html)da conta utilizador, [GRANT Syntax](https://dev.mysql.com/doc/refman/5.7/en/grant.html)e [Privileges](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
