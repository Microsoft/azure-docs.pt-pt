---
title: Criar usuários-banco de dados do Azure para MariaDB
description: Este artigo descreve como você pode criar novas contas de usuário para interagir com um banco de dados do Azure para o MariaDB Server.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: cbfcb097b4fda30bdeed940a5acb609b02f5d788
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74764274"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>Criar usuários no banco de dados do Azure para MariaDB 
Este artigo descreve como você pode criar usuários no banco de dados do Azure para MariaDB.

Ao criar pela primeira vez o banco de dados do Azure para MariaDB, você forneceu um nome de usuário e uma senha de logon de administrador do servidor. Para obter mais informações, você pode seguir o [início rápido](quickstart-create-mariadb-server-database-using-azure-portal.md). Você pode localizar o nome de usuário de logon do administrador do servidor no portal do Azure.

O usuário administrador do servidor Obtém determinados privilégios para o servidor, conforme listado: selecionar, inserir, atualizar, excluir, criar, descartar, recarregar, processar, REFERENCIAr, INDEXar, alterar, mostrar bancos de dados, criar tabelas TEMPORÁRIAs, bloquear tabelas, executar, replicação SUBORDINAda, replicação CLIENTE, CRIAR EXIBIÇÃO, MOSTRAR EXIBIÇÃO, CRIAR ROTINA, ALTERAR ROTINA, CRIAR USUÁRIO, EVENTO, GATILHO

Depois que o banco de dados do Azure para o servidor MariaDB for criado, você poderá usar a primeira conta de usuário administrador do servidor para criar usuários adicionais e conceder acesso de administrador a eles. Além disso, a conta do administrador do servidor pode ser usada para criar usuários com menos privilégios que tenham acesso a esquemas de banco de dados individuais.

## <a name="create-additional-admin-users"></a>Criar usuários administradores adicionais
1. Obtenha as informações de conexão e o nome de usuário do administrador.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Você pode encontrar facilmente o nome do servidor e as informações de entrada na página **visão geral** do servidor ou na página **Propriedades** no portal do Azure. 

2. Use a conta de administrador e a senha para se conectar ao servidor de banco de dados. Use sua ferramenta de cliente preferida, como MySQL Workbench, MySQL. exe, HeidiSQL ou outros. 
   Se você não tiver certeza de como se conectar, consulte [usar o MySQL Workbench para se conectar e consultar dados](./connect-workbench.md)

3. Edite e execute o código SQL a seguir. Substitua seu novo nome de usuário pelo valor de espaço reservado `new_master_user`. Essa sintaxe concede os privilégios listados em todos os esquemas de banco de dados ( *.* ) ao nome de usuário (new_master_user neste exemplo). 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. Verificar as concessões 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="create-database-users"></a>Criar usuários de banco de dados

1. Obtenha as informações de conexão e o nome de usuário do administrador.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Você pode encontrar facilmente o nome do servidor e as informações de entrada na página **visão geral** do servidor ou na página **Propriedades** no portal do Azure. 

2. Use a conta de administrador e a senha para se conectar ao servidor de banco de dados. Use sua ferramenta de cliente preferida, como MySQL Workbench, MySQL. exe, HeidiSQL ou outros. 
   Se você não tiver certeza de como se conectar, consulte [usar o MySQL Workbench para se conectar e consultar dados](./connect-workbench.md)

3. Edite e execute o código SQL a seguir. Substitua o valor do espaço reservado `db_user` pelo seu novo nome de usuário pretendido e o valor de espaço reservado `testdb` com seu próprio nome de banco de dados.

   Essa sintaxe de código SQL cria um novo banco de dados chamado TestDB para fins de exemplo. Em seguida, ele cria um novo usuário no banco de dados do Azure para o serviço MariaDB e concede a todos os privilégios para o novo esquema de banco de dados (TestDB.\*) para esse usuário. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. Verifique as concessões no banco de dados.
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Faça logon no servidor, especificando o banco de dados designado, usando o novo nome de usuário e senha. Este exemplo mostra a linha de comando do MySQL. Com esse comando, será solicitada a senha para o nome de usuário. Substitua seu próprio nome do servidor, nome do banco de dados e nome de usuário.

   ```bash
   mysql --host mydemoserver.mariadb.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
   Para obter mais informações sobre o gerenciamento de contas de usuário, consulte a documentação do MariaDB para [Gerenciamento de contas de usuário](https://mariadb.com/kb/en/library/user-account-management/), sintaxe de [concessão](https://mariadb.com/kb/en/library/grant/)e [privilégios](https://mariadb.com/kb/en/library/grant/#privilege-levels).

## <a name="next-steps"></a>Passos seguintes
Abra o firewall para os endereços IP dos computadores dos novos usuários para permitir que eles se conectem: [criar e gerenciar as regras de firewall do banco de dados do Azure para MariaDB usando o portal do Azure](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->
