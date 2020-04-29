---
title: Criar utilizadores - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como pode criar novas contas de utilizador para interagir com uma Base de Dados Azure para PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2019
ms.openlocfilehash: 127d484d6cfc35368803069f9c3d602e787baa56
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80384352"
---
# <a name="create-users-in-azure-database-for-postgresql---single-server"></a>Criar utilizadores na Base de Dados Azure para PostgreSQL - Servidor Único

Este artigo descreve como pode criar utilizadores dentro de uma Base de Dados Azure para servidor PostgreSQL.

Se quiser aprender como criar e gerir utilizadores de subscrição do Azure e seus privilégios, pode visitar o artigo de controlo de [acesso baseado em funções do Azure (RBAC)](../role-based-access-control/built-in-roles.md) ou rever [como personalizar funções.](../role-based-access-control/custom-roles.md)

## <a name="the-server-admin-account"></a>A conta de administrador do servidor

Quando criou pela primeira vez a sua Base de Dados Azure para postgreSQL, forneceu um nome de utilizador e palavra-passe de administrador do servidor. Para mais informações, pode seguir o [Quickstart](quickstart-create-server-database-portal.md) para ver a abordagem passo a passo. Uma vez que o nome de utilizador do servidor é um nome personalizado, pode localizar o nome de utilizador do servidor escolhido a partir do portal Azure.

A Base de Dados Azure para servidor PostgreSQL é criada com as 3 funções predefinidas definidas. Pode ver estes papéis dirigindo o comando:`SELECT rolname FROM pg_roles;`

- azure_pg_admin
- azure_superuser
- seu servidor administrador

O utilizador do seu servidor é membro do papel azure_pg_admin. No entanto, a conta de administração do servidor não faz parte do papel azure_superuser. Uma vez que este serviço é um serviço PaaS gerido, apenas a Microsoft faz parte da função de super utilizador.

O motor PostgreSQL utiliza privilégios para controlar o acesso a objetos de base de dados, conforme discutido na documentação do [produto PostgreSQL](https://www.postgresql.org/docs/current/static/sql-createrole.html). Na Base de Dados Azure para PostgreSQL, o utilizador de administração do servidor recebe estes privilégios: LOGIN, NOSUPERUSER, HERDA, CREATEDB, CREATEROLE, NOREPLICATION

A conta de utilizador do servidor pode ser usada para criar utilizadores adicionais e conceder esses utilizadores para a função azure_pg_admin. Além disso, a conta de administração do servidor pode ser usada para criar utilizadores e funções menos privilegiadas que tenham acesso a bases de dados individuais e schemas.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Como criar utilizadores adicionais de administração na Base de Dados Azure para PostgreSQL

1. Obtenha as informações de ligação e o nome do utilizador administrativo.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Pode facilmente encontrar o nome do servidor e informações de início de sessão a partir da página **'Overview'** do servidor ou da página **Propriedades** no portal Azure.

2. Utilize a conta de administração e a palavra-passe para se ligar ao seu servidor de base de dados. Utilize a sua ferramenta de cliente preferida, como pgAdmin ou psql.
   Se não tem a certeza de como se conectar, consulte [o arranque rápido](./quickstart-create-server-database-portal.md)

3. Editar e executar o seguinte código SQL. Substitua o seu novo nome de utilizador pelo valor do espaço reservado <new_user> e substitua a palavra-passe do espaço reservado pela sua própria senha forte. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';

   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Como criar utilizadores de base de dados na Base de Dados Azure para PostgreSQL

1. Obtenha as informações de ligação e o nome do utilizador administrativo.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Pode facilmente encontrar o nome do servidor e informações de início de sessão a partir da página **'Overview'** do servidor ou da página **Propriedades** no portal Azure.

2. Utilize a conta de administração e a palavra-passe para se ligar ao seu servidor de base de dados. Utilize a sua ferramenta de cliente preferida, como pgAdmin ou psql.

3. Editar e executar o seguinte código SQL. Substitua o `<db_user>` valor do espaço reservado pelo seu `<newdb>` novo nome de utilizador pretendido e o valor do espaço reservado pelo seu próprio nome de base de dados. Substitua a senha do espaço reservado pela sua própria senha forte.

   Esta sintaxe de código sql cria uma nova base de dados chamada testdb, por exemplo. Em seguida, cria um novo utilizador no serviço PostgreSQL, e concede privilégios de ligação à nova base de dados para esse utilizador.

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. Utilizando uma conta de administração, poderá ter de conceder privilégios adicionais para proteger os objetos na base de dados. Consulte a [documentação postgresQL](https://www.postgresql.org/docs/current/static/ddl-priv.html) para mais detalhes sobre funções e privilégios na base de dados. Por exemplo:

   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Inicie sessão no seu servidor, especificando a base de dados designada, utilizando o novo nome de utilizador e palavra-passe. Este exemplo mostra a linha de comando psql. Com este comando, é solicitado a palavra-passe para o nome de utilizador. Substitua o nome do seu próprio servidor, nome da base de dados e nome do utilizador.

   ```shell
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Passos seguintes

Abra a firewall para os endereços IP das máquinas dos novos utilizadores para permitir a sua ligação: Criar e gerir a Base de Dados Azure para regras de [firewall PostgreSQL utilizando o portal Azure](howto-manage-firewall-using-portal.md) ou [o Azure CLI](howto-manage-firewall-using-cli.md).

Para obter mais informações sobre a gestão da conta de utilizador, consulte a documentação do produto PostgreSQL para [Papéis e Privilégios](https://www.postgresql.org/docs/current/static/user-manag.html)de Base de Dados, [Grant Syntax](https://www.postgresql.org/docs/current/static/sql-grant.html)e [Privileges](https://www.postgresql.org/docs/current/static/ddl-priv.html).
