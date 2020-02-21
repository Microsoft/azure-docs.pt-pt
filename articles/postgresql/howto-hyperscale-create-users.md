---
title: Criar utilizadores - Hyperscale (Citus) - Base de Dados Azure para PostgreSQL
description: Este artigo descreve como pode criar novas contas de utilizador para interagir com uma Base de Dados Azure para PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 1/8/2019
ms.openlocfilehash: 674fd4372bdf7c3782d18aaf04b48eb0067a9b2e
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484932"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Criar utilizadores na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)

> [!NOTE]
> O termo "utilizadores" refere-se aos utilizadores dentro de um grupo de servidores de Hiperescala (Citus). Para conhecer os utilizadores de subscrição do Azure e os seus privilégios, visite o artigo de controlo de [acesso baseado em funções do Azure (RBAC)](../role-based-access-control/built-in-roles.md) ou reveja [como personalizar funções.](../role-based-access-control/custom-roles.md)

## <a name="the-server-admin-account"></a>A conta de administrador do servidor

O motor PostgreSQL utiliza [funções](https://www.postgresql.org/docs/current/sql-createrole.html) para controlar o acesso a objetos de base de dados, e um grupo de servidores de Hiperescala (Citus) recém-criado vem com várias funções pré-definidas:

* As [funções predefinidas postgresQL](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

Uma vez que a Hyperscale é um serviço PaaS gerido, só a Microsoft pode iniciar sessão com a função `postgres` super utilizador. Para um acesso administrativo limitado, a Hyperscale proporciona o papel `citus`.

Permissões para o papel `citus`:

* Leia todas as variáveis de configuração, mesmo variáveis normalmente visíveis apenas para superutilizadores.
* Leia todos os pontos\_de vista\_\* e use várias extensões relacionadas com estatísticas -- mesmo vistas ou extensões normalmente visíveis apenas para superutilizadores.
* Execute funções de monitorização que possam ter fechaduras DE PARTILHA DE ACESSO nas mesas, potencialmente durante muito tempo.
* [Crie extensões PostgreSQL](concepts-hyperscale-extensions.md) (porque o papel é membro da `azure_pg_admin`).

Nomeadamente, o papel `citus` tem algumas restrições:

* Não pode criar papéis
* Não pode criar bases de dados

## <a name="how-to-create-additional-user-roles"></a>Como criar funções adicionais de utilizador

Como referido, a conta de administração `citus` carece de autorização para criar utilizadores adicionais. Para adicionar um utilizador, utilize a interface do portal Azure.

1. Vá à página **De Papéis** para o seu grupo de servidores Hyperscale e clique **em + Adicionar:**

   ![A página de papéis](media/howto-hyperscale-create-users/1-role-page.png)

2. Introduza o nome do papel e a palavra-passe. Clique em **Guardar**.

   ![Adicionar papel](media/howto-hyperscale-create-users/2-add-user-fields.png)

O utilizador será criado no nó coordenador do grupo de servidores, e propagado a todos os nós dos trabalhadores. As funções criadas através do portal Azure têm o atributo `LOGIN`, o que significa que são verdadeiros utilizadores que podem entrar na base de dados.

## <a name="how-to-modify-privileges-for-user-role"></a>Como modificar privilégios para o papel do utilizador

As novas funções de utilizador são geralmente utilizadas para fornecer acesso à base de dados com privilégios restritos. Para modificar os privilégios do utilizador, utilize comandos PostgreSQL padrão, utilizando uma ferramenta como pgAdmin ou psql. (Ver [ligação com o psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) no arranque rápido de Hiperescala (Citus).

Por exemplo, para permitir que `db_user` leiam `mytable`, conceda a permissão:

```sql
GRANT SELECT ON mytable TO db_user;
```

A hiperescala (Citus) propaga declarações de SUBVENÇDE de mesa única através de todo o cluster, aplicando-as em todos os nós dos trabalhadores. No entanto, os GRANTs que são de todo o sistema (por exemplo, para todas as tabelas num esquema) precisam de ser executados em cada nó de data.  Utilize a função `run_command_on_workers()` ajudante:

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>Como eliminar uma função de utilizador ou alterar a sua palavra-passe

Para atualizar um utilizador, visite a página **'Funções'** para o seu grupo de servidores Hyperscale e clique nas elipses... ao lado do utilizador. As elipses abrirão um menu para eliminar o utilizador ou redefinir a sua palavra-passe.

   ![Editar um papel](media/howto-hyperscale-create-users/edit-role.png)

O papel `citus` é privilegiado e não pode ser apagado.

## <a name="next-steps"></a>Passos Seguintes

Abra a firewall para os endereços IP das máquinas dos novos utilizadores para que possam ligar: Criar e gerir as regras de [firewall da Hiperescala (Citus) utilizando o portal Azure](howto-hyperscale-manage-firewall-using-portal.md).

Para obter mais informações sobre a gestão da conta de utilizador da base de dados, consulte a documentação do produto PostgreSQL:

* [Papéis e Privilégios de Base de Dados](https://www.postgresql.org/docs/current/static/user-manag.html)
* [Sintaxe grant](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Privilégios](https://www.postgresql.org/docs/current/static/ddl-priv.html)
