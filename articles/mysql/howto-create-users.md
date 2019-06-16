---
title: Criar utilizadores na base de dados do Azure para o servidor MySQL
description: Este artigo descreve como pode criar novas contas de usuário para interagir com uma base de dados do Azure para o servidor MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 4cd2305ed3d7f88f6c3825d8f7cdb5d81f9a0f6b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61460189"
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Criar utilizadores na base de dados do Azure para o servidor MySQL 
Este artigo descreve como pode criar os utilizadores numa base de dados do Azure para o servidor MySQL.

Ao criar a base de dados do Azure para MySQL, que forneceu um nome de utilizador de início de sessão de administrador do servidor e a palavra-passe. Para obter mais informações, pode seguir a [guia de introdução](quickstart-create-mysql-server-database-using-azure-portal.md). Pode localizar o seu nome de utilizador do início de sessão do administrador do servidor do portal do Azure.

O utilizador de administrador de servidor obtém determinados privilégios para o seu servidor, conforme listado: SELECIONAR, INSERIR, ATUALIZAR, ELIMINAR, CRIAR, REMOVER, RECARREGUE, PROCESSAR, REFERÊNCIAS, INDEX, ALTER, VEJA AS BASES DE DADOS, CRIAR TABELAS TEMPORÁRIAS, BLOQUEAR TABELAS, EXECUTAR, CRIAR SUBORDINADO DE REPLICAÇÃO, O CLIENTE DE REPLICAÇÃO, VISUALIZAR, MOSTRAR EXIBIÇÃO, CRIAR ROTINA, ALTER ROTINA, CRIAR UTILIZADOR , EVENTO, O ACIONADOR

Depois de criar a base de dados do Azure para o servidor MySQL, pode utilizar a conta de utilizador de administrador do servidor primeiro para criar mais utilizadores e conceder acesso de administrador aos mesmos. Além disso, pode servir-se a conta de administrador do servidor para criar com menos privilégios de utilizadores que têm acesso aos esquemas de banco de dados individuais.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Como criar os utilizadores administradores adicionais na base de dados do Azure para MySQL
1. Obtenha o nome de utilizador de administração e de informações da ligação.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Pode encontrar facilmente o nome do servidor e informações de início de sessão do servidor **descrição geral** página ou o **propriedades** página no portal do Azure. 

2. Utilize a conta de administrador e a palavra-passe para ligar ao seu servidor de base de dados. Utilize a sua ferramenta de cliente preferido, como o MySQL Workbench, mysql.exe, HeidiSQL ou outras pessoas. 
   Se tiver a certeza de como se pode ligar, consulte o artigo [utilizar o MySQL Workbench para se ligar e consultar dados](./connect-workbench.md)

3. Edite e execute o seguinte código SQL. Substitua o novo nome de utilizador para o valor do marcador de posição `new_master_user`. Essa sintaxe concede os privilégios listados em todos os esquemas de banco de dados ( *.* ) para o nome de utilizador (new_master_user neste exemplo). 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. Verifique se as concessões 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Como criar utilizadores de base de dados na base de dados do Azure para MySQL

1. Obtenha o nome de utilizador de administração e de informações da ligação.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Pode encontrar facilmente o nome do servidor e informações de início de sessão do servidor **descrição geral** página ou o **propriedades** página no portal do Azure. 

2. Utilize a conta de administrador e a palavra-passe para ligar ao seu servidor de base de dados. Utilize a sua ferramenta de cliente preferido, como o MySQL Workbench, mysql.exe, HeidiSQL ou outras pessoas. 
   Se tiver a certeza de como se pode ligar, consulte o artigo [utilizar o MySQL Workbench para se ligar e consultar dados](./connect-workbench.md)

3. Edite e execute o seguinte código SQL. Substitua o valor do marcador de posição `db_user` com o seu novo nome de utilizador pretendido e seu valor do marcador de posição `testdb` com seu próprio nome de base de dados.

   Essa sintaxe de código sql cria uma nova base de dados com o nome testdb para fins de exemplo. Em seguida, cria um novo utilizador no serviço de MySQL e concede todos os privilégios para o novo esquema de base de dados (testdb.\*) para esse utilizador. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. Verifique se concede na base de dados.
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Inicie sessão servidor, especificar a base de dados designado, usando o novo nome de utilizador e palavra-passe. Este exemplo mostra a linha de comandos do mysql. Com este comando, lhe for pedida a palavra-passe para o nome de utilizador. Substitua o seu próprio nome do servidor, o nome de base de dados e o nome de utilizador.

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>Passos Seguintes
Abra a firewall para os endereços IP de máquinas de novos utilizadores para que estes possam ligar-se: [Criar e gerir a base de dados do Azure para as regras de firewall do MySQL com o portal do Azure](howto-manage-firewall-using-portal.md) ou [CLI do Azure](howto-manage-firewall-using-cli.md).

Para obter mais informações sobre a gestão de conta de utilizador, consulte a documentação de produto do MySQL para [gestão de contas de utilizador](https://dev.mysql.com/doc/refman/5.7/en/user-account-management.html), [sintaxe de concessão](https://dev.mysql.com/doc/refman/5.7/en/grant.html), e [privilégios](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
