---
title: Criar utilizadores - Azure Database for PostgreSQL - Single Server
description: Este artigo descreve como pode criar novas contas de utilizador para interagir com uma Base de Dados Azure para PostgreSQL - Servidor Único.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2019
ms.openlocfilehash: c2d0cfc15457d45701f129ae329295064d773a09
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604112"
---
# <a name="create-users-in-azure-database-for-postgresql---single-server"></a>Criar utilizadores na Base de Dados Azure para PostgreSQL - Servidor Único

Este artigo descreve como pode criar utilizadores dentro de uma Base de Dados Azure para servidor PostgreSQL.

Se quiser aprender como criar e gerir os utilizadores de subscrição Azure e seus privilégios, pode visitar o artigo do [Azure role-based access control (Azure RBAC)](../role-based-access-control/built-in-roles.md) ou rever [como personalizar funções.](../role-based-access-control/custom-roles.md)

## <a name="the-server-admin-account"></a>A conta de administrador do servidor

Quando criou a sua Base de Dados Azure para PostgreSQL, forneceu um nome de utilizador e senha de administração do servidor. Para mais informações, pode seguir o [Quickstart](quickstart-create-server-database-portal.md) para ver a abordagem passo a passo. Uma vez que o nome de utilizador do administrador do servidor é um nome personalizado, pode localizar o nome de utilizador do administrador do servidor escolhido a partir do portal Azure.

A Base de Dados Azure para o servidor PostgreSQL é criada com as 3 funções padrão definidas. Pode ver estas funções executando o comando: `SELECT rolname FROM pg_roles;`

- azure_pg_admin
- azure_superuser
- o seu utilizador de administração de servidor

O utilizador de administração do servidor é um membro do papel azure_pg_admin. No entanto, a conta de administração do servidor não faz parte do papel azure_superuser. Uma vez que este serviço é um serviço PaaS gerido, apenas a Microsoft faz parte do papel do super utilizador.

O motor PostgreSQL utiliza privilégios para controlar o acesso a objetos de base de dados, conforme discutido na documentação do [produto PostgreSQL.](https://www.postgresql.org/docs/current/static/sql-createrole.html) Na Base de Dados Azure para PostgreSQL, o utilizador de administração do servidor recebe estes privilégios: LOGIN, NOSUPERUSER, HER, CREATEDB, CREATEROLE, NOREPLICATION

A conta de utilizador de administração do servidor pode ser usada para criar utilizadores adicionais e conceder a esses utilizadores a função azure_pg_admin. Além disso, a conta de administração do servidor pode ser usada para criar utilizadores e funções menos privilegiadas que tenham acesso a bases de dados e esquemas individuais.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Como criar utilizadores de administração adicionais na Base de Dados Azure para PostgreSQL

1. Obtenha a informação de ligação e o nome de utilizador administrativo.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Pode encontrar facilmente o nome do servidor e informações de inscrição a partir da página **'Vista Geral'** do servidor ou da página **Propriedades** no portal Azure.

2. Utilize a conta de administração e a palavra-passe para ligar ao servidor de base de dados. Utilize a sua ferramenta cliente preferida, como pgAdmin ou psql.
   Se não tem certeza de como se conectar, consulte [o arranque rápido](./quickstart-create-server-database-portal.md)

3. Editar e executar o seguinte código SQL. Substitua o seu novo nome de utilizador pelo valor do espaço reservado <new_user> e substitua a palavra-passe do espaço reservado pela sua própria senha forte. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';

   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Como criar utilizadores de bases de dados na Base de Dados Azure para PostgreSQL

1. Obtenha a informação de ligação e o nome de utilizador administrativo.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Pode encontrar facilmente o nome do servidor e informações de inscrição a partir da página **'Vista Geral'** do servidor ou da página **Propriedades** no portal Azure.

2. Utilize a conta de administração e a palavra-passe para ligar ao servidor de base de dados. Utilize a sua ferramenta cliente preferida, como pgAdmin ou psql.

3. Editar e executar o seguinte código SQL. Substitua o valor do espaço reservado `<db_user>` pelo novo nome de utilizador pretendido e o valor do espaço reservado pelo seu próprio nome de base `<newdb>` de dados. Substitua a palavra-passe do espaço reservado pela sua própria senha forte.

   Esta sintaxe de código sql cria uma nova base de dados chamada testdb, por exemplo. Em seguida, cria um novo utilizador no serviço PostgreSQL, e concede privilégios de ligação à nova base de dados para esse utilizador.

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. Utilizando uma conta de administração, poderá ter de conceder privilégios adicionais para proteger os objetos na base de dados. Consulte a [documentação postgreSQL](https://www.postgresql.org/docs/current/static/ddl-priv.html) para mais detalhes sobre funções e privilégios de base de dados. Por exemplo:

   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Faça login no seu servidor, especificando a base de dados designada, utilizando o novo nome de utilizador e senha. Este exemplo mostra a linha de comando psql. Com este comando, é solicitado a palavra-passe para o nome de utilizador. Substitua o nome do seu próprio servidor, nome de base de dados e nome de utilizador.

   ```shell
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Passos seguintes

Abra a firewall para os endereços IP das máquinas dos novos utilizadores para permitir a sua ligação: [Criar e gerir a Base de Dados Azure para regras de firewall PostgreSQL utilizando o portal Azure](howto-manage-firewall-using-portal.md) ou [Azure CLI](howto-manage-firewall-using-cli.md).

Para obter mais informações sobre a gestão da conta de utilizador, consulte a documentação do produto PostgreSQL para [Funções e Privilégios de Base de Dados,](https://www.postgresql.org/docs/current/static/user-manag.html) [Sintes e](https://www.postgresql.org/docs/current/static/sql-grant.html) [Privilégios](https://www.postgresql.org/docs/current/static/ddl-priv.html)GRANT .
