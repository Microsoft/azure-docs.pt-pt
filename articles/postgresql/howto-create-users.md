---
title: Criar usuários no banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como você pode criar novas contas de usuário para interagir com um banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2019
ms.openlocfilehash: 91ba485347aeb19ce9b173bd4cec944a655a56dc
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203508"
---
# <a name="create-users-in-azure-database-for-postgresql---single-server"></a>Criar usuários no banco de dados do Azure para PostgreSQL-servidor único
Este artigo descreve como você pode criar usuários em um servidor de banco de dados do Azure para PostgreSQL. 

Se você quiser saber mais sobre como criar e gerenciar usuários de assinatura do Azure e seus privilégios, você pode visitar o [artigo RBAC (controle de acesso baseado em função) do Azure](../role-based-access-control/built-in-roles.md) ou examinar [como personalizar funções](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>A conta de administrador do servidor
Quando você criou o banco de dados do Azure para PostgreSQL pela primeira vez, forneceu um nome de usuário e uma senha de administrador do servidor. Para obter mais informações, você pode seguir o [início rápido](quickstart-create-server-database-portal.md) para ver a abordagem passo a passo. Como o nome de usuário do administrador do servidor é um nome personalizado, você pode localizar o nome de usuário do administrador do servidor escolhido no portal do Azure.

O banco de dados do Azure para servidor PostgreSQL é criado com as 3 funções padrão definidas. Você pode ver essas funções executando o comando:`SELECT rolname FROM pg_roles;`
- azure_pg_admin
- azure_superuser
- usuário administrador do servidor

O usuário administrador do servidor é membro da função azure_pg_admin. No entanto, a conta do administrador do servidor não faz parte da função azure_superuser. Como esse serviço é um serviço de PaaS gerenciado, somente a Microsoft faz parte da função de superusuário. 

O mecanismo PostgreSQL usa privilégios para controlar o acesso a objetos de banco de dados, conforme discutido na [documentação do produto PostgreSQL](https://www.postgresql.org/docs/current/static/sql-createrole.html). No banco de dados do Azure para PostgreSQL, o usuário administrador do servidor recebe estes privilégios: LOGON, SUPERUSUÁRIO, HERANÇA, CREATEDB, CREATEROLE, NOREPLICATION

A conta de usuário administrador do servidor pode ser usada para criar usuários adicionais e conceder a esses usuários a função azure_pg_admin. Além disso, a conta do administrador do servidor pode ser usada para criar usuários e funções com menos privilégios que tenham acesso a bancos de dados individuais e esquemas.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Como criar usuários administrativos adicionais no banco de dados do Azure para PostgreSQL
1. Obtenha as informações de conexão e o nome de usuário do administrador.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Você pode encontrar facilmente o nome do servidor e as informações de entrada na página **visão geral** do servidor ou na página **Propriedades** no portal do Azure. 

2. Use a conta de administrador e a senha para se conectar ao servidor de banco de dados. Use sua ferramenta de cliente preferida, como pgAdmin ou psql.
   Se você não tiver certeza de como se conectar, consulte [o guia de início rápido](./quickstart-create-server-database-portal.md)

3. Edite e execute o código SQL a seguir. Substitua seu novo nome de usuário pelo valor de espaço reservado < new_user > e substitua a senha de espaço reservado por sua própria senha forte. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Como criar usuários de banco de dados no banco de dados do Azure para PostgreSQL

1. Obtenha as informações de conexão e o nome de usuário do administrador.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Você pode encontrar facilmente o nome do servidor e as informações de entrada na página **visão geral** do servidor ou na página **Propriedades** no portal do Azure. 

2. Use a conta de administrador e a senha para se conectar ao servidor de banco de dados. Use sua ferramenta de cliente preferida, como pgAdmin ou psql.

3. Edite e execute o código SQL a seguir. Substitua o valor `<db_user>` de espaço reservado pelo novo nome de usuário pretendido e `<newdb>` pelo valor de espaço reservado pelo seu próprio nome de banco de dados. Substitua a senha do espaço reservado por sua própria senha forte. 

   Essa sintaxe de código SQL cria um novo banco de dados chamado TestDB, por exemplo, para fins. Em seguida, ele cria um novo usuário no serviço PostgreSQL e concede privilégios de conexão ao novo banco de dados para esse usuário. 

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. Usando uma conta de administrador, talvez seja necessário conceder privilégios adicionais para proteger os objetos no banco de dados. Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/current/static/ddl-priv.html) para obter mais detalhes sobre funções e privilégios de banco de dados. Por exemplo: 
   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Faça logon no servidor, especificando o banco de dados designado, usando o novo nome de usuário e senha. Este exemplo mostra a linha de comando psql. Com esse comando, será solicitada a senha para o nome de usuário. Substitua seu próprio nome do servidor, nome do banco de dados e nome de usuário.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Passos seguintes
Abra o firewall para os endereços IP dos computadores dos novos usuários para permitir que eles se conectem: [Crie e gerencie as regras de firewall do banco de dados do Azure para PostgreSQL usando o portal do Azure](howto-manage-firewall-using-portal.md) ou [CLI do Azure](howto-manage-firewall-using-cli.md).

Para obter mais informações sobre o gerenciamento de contas de usuário, consulte documentação do produto PostgreSQL para [funções e privilégios de banco de dados](https://www.postgresql.org/docs/current/static/user-manag.html), [sintaxe de concessão](https://www.postgresql.org/docs/current/static/sql-grant.html)e [privilégios](https://www.postgresql.org/docs/current/static/ddl-priv.html).
