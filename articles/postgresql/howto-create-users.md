---
title: Criar utilizadores na base de dados do Azure para o servidor PostgreSQL
description: Este artigo descreve como pode criar novas contas de usuário para interagir com uma base de dados do Azure para o servidor PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/16/2018
ms.openlocfilehash: 33c107c46b314136fa3d43f8e7881e096afa374c
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351087"
---
# <a name="create-users-in-azure-database-for-postgresql-server"></a>Criar utilizadores na base de dados do Azure para o servidor PostgreSQL 
Este artigo descreve como pode criar os utilizadores numa base de dados do Azure para o servidor PostgreSQL.

## <a name="the-server-admin-account"></a>A conta de administrador do servidor
Ao criar a base de dados do Azure para PostgreSQL, que forneceu um nome de utilizador de administrador do servidor e a palavra-passe. Para obter mais informações, pode seguir a [guia de introdução](quickstart-create-server-database-portal.md) para ver a abordagem passo a passo. Uma vez que o nome de utilizador de administrador do servidor é um nome personalizado, pode localizar o nome de utilizador de administrador de servidor escolhido do portal do Azure.

A base de dados do Azure para o servidor PostgreSQL é criado com as funções de 3 padrão definidas. Pode ver estas funções, executando o comando: `SELECT rolname FROM pg_roles;`
- azure_pg_admin
- azure_superuser
- o utilizador de administrador do servidor

O utilizador de administrador do servidor é um membro da função azure_pg_admin. No entanto, a conta de administrador do servidor não é parte da função azure_superuser. Uma vez que este serviço é um serviço gerido de PaaS, apenas a Microsoft faz parte da função de Superutilizador. 

O motor de PostgreSQL utiliza privilégios para controlar o acesso a objetos de base de dados, como explicado no [documentação de produto do PostgreSQL](https://www.postgresql.org/docs/current/static/sql-createrole.html). Na base de dados do Azure para PostgreSQL, o utilizador de administrador do servidor é concedido esses privilégios: LOGIN, NOSUPERUSER, INHERIT, CREATEDB, CREATEROLE, NOREPLICATION

A conta de utilizador de administrador do servidor pode ser utilizada para criar mais utilizadores e conceder aos utilizadores para a função de azure_pg_admin. Além disso, pode servir-se a conta de administrador do servidor para criar com menos privilégios de utilizadores e funções que têm acesso a bancos de dados individuais e esquemas.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Como criar os utilizadores administradores adicionais na base de dados do Azure para PostgreSQL
1. Obtenha o nome de utilizador de administração e de informações da ligação.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Pode encontrar facilmente o nome do servidor e informações de início de sessão do servidor **descrição geral** página ou o **propriedades** página no portal do Azure. 

2. Utilize a conta de administrador e a palavra-passe para ligar ao seu servidor de base de dados. Utilize a sua ferramenta de cliente preferido, como pgAdmin ou psql.
   Se tiver a certeza de como se pode ligar, consulte o artigo [o guia de introdução](./quickstart-create-server-database-portal.md)

3. Edite e execute o seguinte código SQL. Substitua o novo nome de utilizador para o valor do marcador de posição < new_user > e substitua a palavra-passe do marcador de posição pela sua própria palavra-passe segura. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Como criar utilizadores de base de dados na base de dados do Azure para PostgreSQL

1. Obtenha o nome de utilizador de administração e de informações da ligação.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Pode encontrar facilmente o nome do servidor e informações de início de sessão do servidor **descrição geral** página ou o **propriedades** página no portal do Azure. 

2. Utilize a conta de administrador e a palavra-passe para ligar ao seu servidor de base de dados. Utilize a sua ferramenta de cliente preferido, como pgAdmin ou psql.

3. Edite e execute o seguinte código SQL. Substitua o valor do marcador de posição `<db_user>` com o seu novo nome de utilizador pretendido e seu valor do marcador de posição `<newdb>` com seu próprio nome de base de dados. Substitua a palavra-passe do marcador de posição pela sua própria palavra-passe segura. 

   Essa sintaxe de código sql cria uma nova base de dados com o nome testdb, para fins de exemplo. Em seguida, ele cria um novo utilizador no serviço de PostgreSQL e concede ligue privilégios para a nova base de dados para esse utilizador. 

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. Utilizar uma conta de administrador, poderá ter de conceder privilégios adicionais para proteger os objetos na base de dados. Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/current/static/ddl-priv.html) para obter mais detalhes nas funções de base de dados e privilégios. Por exemplo: 
   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Inicie sessão no seu servidor, especificar a base de dados designado, usando o novo nome de utilizador e palavra-passe. Este exemplo mostra a linha de comandos psql. Com este comando, lhe for pedida a palavra-passe para o nome de utilizador. Substitua o seu próprio nome do servidor, o nome de base de dados e o nome de utilizador.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Passos Seguintes
Abra a firewall para os endereços IP de máquinas de novos utilizadores para que estes possam ligar-se: [Criar e gerir a base de dados do Azure para as regras de firewall do PostgreSQL com o portal do Azure](howto-manage-firewall-using-portal.md) ou [CLI do Azure](howto-manage-firewall-using-cli.md).

Para obter mais informações sobre a gestão de conta de utilizador, consulte a documentação de produto do PostgreSQL para [funções de base de dados e privilégios](https://www.postgresql.org/docs/current/static/user-manag.html), [sintaxe de concessão](https://www.postgresql.org/docs/current/static/sql-grant.html), e [privilégios](https://www.postgresql.org/docs/current/static/ddl-priv.html).
