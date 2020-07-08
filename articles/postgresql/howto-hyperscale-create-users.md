---
title: Criar utilizadores - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Este artigo descreve como pode criar novas contas de utilizador para interagir com uma Base de Dados Azure para PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 1/8/2019
ms.openlocfilehash: b8d47d1036473af1b367cc0266aae3ea1bceeada
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84343936"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Criar utilizadores na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)

> [!NOTE]
> O termo "utilizadores" refere-se aos utilizadores dentro de um grupo de servidores De Hiperescala (Citus). Para saber em vez disso sobre os utilizadores de subscrição Azure e seus privilégios, visite o artigo de [controlo de acesso baseado em funções Azure (RBAC)](../role-based-access-control/built-in-roles.md) ou reveja como [personalizar funções.](../role-based-access-control/custom-roles.md)

## <a name="the-server-admin-account"></a>A conta de administrador do servidor

O motor PostgreSQL utiliza [funções](https://www.postgresql.org/docs/current/sql-createrole.html) para controlar o acesso a objetos de base de dados, e um grupo de servidores hiperescala (Citus) recém-criado vem com várias funções pré-definidas:

* As [funções pós-SQL predefinidos](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

Uma vez que o Hyperscale é um serviço PaaS gerido, apenas a Microsoft pode iniciar súbsento com a `postgres` função de super utilizador. Para um acesso administrativo limitado, a Hyperscale fornece o `citus` papel.

Permissões para o `citus` papel:

* Leia todas as variáveis de configuração, mesmo as variáveis normalmente visíveis apenas para os superusers.
* Leia todas as visões pg \_ stat \_ \* e use várias extensões relacionadas com estatísticas -- mesmo vistas ou extensões normalmente visíveis apenas para superusers.
* Execute funções de monitorização que possam ter bloqueios ACCESS SHARE nas tabelas, potencialmente por muito tempo.
* [Criar extensões PostgreSQL](concepts-hyperscale-extensions.md) (porque o papel é membro de `azure_pg_admin` ).

Notavelmente, o `citus` papel tem algumas restrições:

* Não se pode criar papéis
* Não é possível criar bases de dados

## <a name="how-to-create-additional-user-roles"></a>Como criar funções de utilizador adicionais

Como mencionado, a `citus` conta de administração carece de permissão para criar utilizadores adicionais. Para adicionar um utilizador, utilize a interface do portal Azure.

1. Aceda à página **Roles** para o seu grupo de servidor Hyperscale e clique **+ Adicionar**+

   ![A página de papéis](media/howto-hyperscale-create-users/1-role-page.png)

2. Insira o nome da função e a palavra-passe. Clique em **Guardar**.

   ![Adicionar papel](media/howto-hyperscale-create-users/2-add-user-fields.png)

O utilizador será criado no nó coordenador do grupo de servidores e propagado a todos os nós do trabalhador. As funções criadas através do portal Azure têm o atributo, o `LOGIN` que significa que são verdadeiros utilizadores que podem iniciar súbs na base de dados.

## <a name="how-to-modify-privileges-for-user-role"></a>Como modificar privilégios para o papel de utilizador

As novas funções de utilizador são geralmente utilizadas para fornecer acesso à base de dados com privilégios restritos. Para modificar os privilégios do utilizador, utilize comandos PostgreSQL padrão, utilizando uma ferramenta como o PgAdmin ou o PSQL. (Ver [ligação com psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) no arranque rápido de Hiperescala (Citus).)

Por exemplo, para permitir `db_user` a `mytable` leitura, conceder a permissão:

```sql
GRANT SELECT ON mytable TO db_user;
```

A Hiperescala (Citus) propaga declarações de SUBVENÇão de uma única mesa através de todo o cluster, aplicando-as em todos os nós dos trabalhadores. Também propaga GRANTs que são de todo o sistema (por exemplo, para todas as tabelas num esquema):

```sql
-- applies to the coordinator node and propagates to workers
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>Como eliminar uma função de utilizador ou alterar a sua palavra-passe

Para atualizar um utilizador, visite a página **Roles** para o seu grupo de servidor Hyperscale e clique nas elipses... ao lado do utilizador. **...** As elipses abrirão um menu para eliminar o utilizador ou redefinir a sua palavra-passe.

   ![Editar um papel](media/howto-hyperscale-create-users/edit-role.png)

O `citus` papel é privilegiado e não pode ser apagado.

## <a name="next-steps"></a>Próximos passos

Abra a firewall para os endereços IP das máquinas dos novos utilizadores para permitir a sua ligação: [Criar e gerir as regras de firewall hyperscale (Citus) utilizando o portal Azure](howto-hyperscale-manage-firewall-using-portal.md).

Para obter mais informações sobre a gestão da conta de utilizador da base de dados, consulte a documentação do produto PostgreSQL:

* [Papéis e privilégios de base de dados](https://www.postgresql.org/docs/current/static/user-manag.html)
* [Sintaxe GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Privilégios](https://www.postgresql.org/docs/current/static/ddl-priv.html)
