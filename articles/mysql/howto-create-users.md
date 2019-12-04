---
title: Criar usuários-banco de dados do Azure para MySQL
description: Este artigo descreve como você pode criar novas contas de usuário para interagir com um servidor de banco de dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: e76e63030cc8e10c857d361cca69e1d35ba8c2c1
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770480"
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Criar usuários no banco de dados do Azure para servidor MySQL 
Este artigo descreve como você pode criar usuários em um servidor de banco de dados do Azure para MySQL.

Quando você criou o banco de dados do Azure para MySQL pela primeira vez, forneceu um nome de usuário e uma senha de logon de administrador do servidor. Para obter mais informações, você pode seguir o [início rápido](quickstart-create-mysql-server-database-using-azure-portal.md). Você pode localizar o nome de usuário de logon do administrador do servidor no portal do Azure.

O usuário administrador do servidor Obtém determinados privilégios para o servidor, conforme listado: selecionar, inserir, atualizar, excluir, criar, descartar, recarregar, processar, REFERENCIAr, INDEXar, alterar, mostrar bancos de dados, criar tabelas TEMPORÁRIAs, bloquear tabelas, executar, replicação SUBORDINAda, replicação CLIENTE, CRIAR EXIBIÇÃO, MOSTRAR EXIBIÇÃO, CRIAR ROTINA, ALTERAR ROTINA, CRIAR USUÁRIO, EVENTO, GATILHO

Depois que o banco de dados do Azure para servidor MySQL for criado, você poderá usar a primeira conta de usuário administrador do servidor para criar usuários adicionais e conceder acesso de administrador a eles. Além disso, a conta do administrador do servidor pode ser usada para criar usuários com menos privilégios que tenham acesso a esquemas de banco de dados individuais.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Como criar usuários administrativos adicionais no banco de dados do Azure para MySQL
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

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Como criar usuários de banco de dados no banco de dados do Azure para MySQL

1. Obtenha as informações de conexão e o nome de usuário do administrador.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Você pode encontrar facilmente o nome do servidor e as informações de entrada na página **visão geral** do servidor ou na página **Propriedades** no portal do Azure. 

2. Use a conta de administrador e a senha para se conectar ao servidor de banco de dados. Use sua ferramenta de cliente preferida, como MySQL Workbench, MySQL. exe, HeidiSQL ou outros. 
   Se você não tiver certeza de como se conectar, consulte [usar o MySQL Workbench para se conectar e consultar dados](./connect-workbench.md)

3. Edite e execute o código SQL a seguir. Substitua o valor do espaço reservado `db_user` pelo seu novo nome de usuário pretendido e o valor de espaço reservado `testdb` com seu próprio nome de banco de dados.

   Essa sintaxe de código SQL cria um novo banco de dados chamado TestDB para fins de exemplo. Em seguida, ele cria um novo usuário no serviço MySQL e concede a todos os privilégios para o novo esquema de banco de dados (TestDB.\*) para esse usuário. 

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

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>Passos seguintes
Abra o firewall para os endereços IP dos computadores dos novos usuários para permitir que eles se conectem: [criar e gerenciar regras de firewall do banco de dados do Azure para MySQL usando o portal do Azure](howto-manage-firewall-using-portal.md) ou [CLI do Azure](howto-manage-firewall-using-cli.md).

Para obter mais informações sobre o gerenciamento de contas de usuário, consulte a documentação do produto MySQL para [Gerenciamento de contas de usuário](https://dev.mysql.com/doc/refman/5.7/en/user-account-management.html), sintaxe de [concessão](https://dev.mysql.com/doc/refman/5.7/en/grant.html)e [privilégios](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
