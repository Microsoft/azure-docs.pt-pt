---
title: Criar utilizadores na base de dados do Azure para MariaDB server
description: Este artigo descreve como pode criar novas contas de usuário para interagir com uma base de dados do Azure para MariaDB server.
author: jasonwhowell
ms.author: jasonh
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 019c4eca564c503913a488a1d1ee5e4046ff320a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58089884"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>Criar utilizadores na base de dados do Azure para MariaDB 
Este artigo descreve como pode criar os utilizadores na base de dados do Azure para MariaDB.

Ao criar a base de dados do Azure para MariaDB, que forneceu um nome de utilizador de início de sessão de administrador do servidor e a palavra-passe. Para obter mais informações, pode seguir a [guia de introdução](quickstart-create-mariadb-server-database-using-azure-portal.md). Pode localizar o seu nome de utilizador do início de sessão do administrador do servidor do portal do Azure.

O utilizador de administrador de servidor obtém determinados privilégios para o seu servidor, conforme listado: SELECIONAR, INSERIR, ATUALIZAR, ELIMINAR, CRIAR, REMOVER, RECARREGUE, PROCESSAR, REFERÊNCIAS, INDEX, ALTER, VEJA AS BASES DE DADOS, CRIAR TABELAS TEMPORÁRIAS, BLOQUEAR TABELAS, EXECUTAR, CRIAR SUBORDINADO DE REPLICAÇÃO, O CLIENTE DE REPLICAÇÃO, VISUALIZAR, MOSTRAR EXIBIÇÃO, CRIAR ROTINA, ALTER ROTINA, CRIAR UTILIZADOR , EVENTO, O ACIONADOR

Depois de criar a base de dados do Azure para MariaDB server, pode utilizar a conta de utilizador de administrador do servidor primeiro para criar mais utilizadores e conceder acesso de administrador aos mesmos. Além disso, pode servir-se a conta de administrador do servidor para criar com menos privilégios de utilizadores que têm acesso aos esquemas de banco de dados individuais.

## <a name="create-additional-admin-users"></a>Criar os utilizadores administradores adicionais
1. Obtenha o nome de utilizador de administração e de informações da ligação.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Pode encontrar facilmente o nome do servidor e informações de início de sessão do servidor **descrição geral** página ou o **propriedades** página no portal do Azure. 

2. Utilize a conta de administrador e a palavra-passe para ligar ao seu servidor de base de dados. Utilize a sua ferramenta de cliente preferido, como o MySQL Workbench, mysql.exe, HeidiSQL ou outras pessoas. 
   Se tiver a certeza de como se pode ligar, consulte o artigo [utilizar o MySQL Workbench para se ligar e consultar dados](./connect-workbench.md)

3. Edite e execute o seguinte código SQL. Substitua o novo nome de utilizador para o valor do marcador de posição `new_master_user`. Essa sintaxe concede os privilégios listados em todos os esquemas de banco de dados (*.*) para o nome de utilizador (new_master_user neste exemplo). 

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

## <a name="create-database-users"></a>Criar utilizadores de base de dados

1. Obtenha o nome de utilizador de administração e de informações da ligação.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Pode encontrar facilmente o nome do servidor e informações de início de sessão do servidor **descrição geral** página ou o **propriedades** página no portal do Azure. 

2. Utilize a conta de administrador e a palavra-passe para ligar ao seu servidor de base de dados. Utilize a sua ferramenta de cliente preferido, como o MySQL Workbench, mysql.exe, HeidiSQL ou outras pessoas. 
   Se tiver a certeza de como se pode ligar, consulte o artigo [utilizar o MySQL Workbench para se ligar e consultar dados](./connect-workbench.md)

3. Edite e execute o seguinte código SQL. Substitua o valor do marcador de posição `db_user` com o seu novo nome de utilizador pretendido e seu valor do marcador de posição `testdb` com seu próprio nome de base de dados.

   Essa sintaxe de código sql cria uma nova base de dados com o nome testdb para fins de exemplo. Em seguida, cria um novo utilizador na base de dados do Azure para MariaDB serviço e concede todos os privilégios para o novo esquema de base de dados (testdb.\*) para esse utilizador. 

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

   ```bash
   mysql --host mydemoserver.mariadb.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
   Para obter mais informações sobre a gestão de conta de utilizador, consulte documentação MariaDB [gestão de contas de utilizador](https://mariadb.com/kb/en/library/user-account-management/), [sintaxe de concessão](https://mariadb.com/kb/en/library/grant/), e [privilégios](https://mariadb.com/kb/en/library/grant/#privilege-levels).

## <a name="next-steps"></a>Passos Seguintes
Abra a firewall para os endereços IP de máquinas de novos utilizadores para que estes possam ligar-se: [Criar e gerir a base de dados do Azure para MariaDB regras de firewall com o portal do Azure](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->
