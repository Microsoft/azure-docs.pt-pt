---
title: Criar usuários no banco de dados do Azure para PostgreSQL-Citus (hiperescala)
description: Este artigo descreve como você pode criar novas contas de usuário para interagir com um banco de dados do Azure para PostgreSQL-Citus (hiperescala).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7187135b29f0a9a790c032330c73bcb1ae27229b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515944"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Criar usuários no banco de dados do Azure para PostgreSQL-Citus (hiperescala)

Este artigo descreve como você pode criar usuários em um grupo de servidores Citus (hiperescala). Para saber mais sobre os usuários de assinatura do Azure e seus privilégios, visite o [artigo RBAC (controle de acesso baseado em função) do Azure](../role-based-access-control/built-in-roles.md) ou examine [como personalizar funções](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>A conta de administrador do servidor

Um grupo de servidores Citus (hiperscale) criado recentemente vem com várias funções predefinidas:

* As [funções PostgreSQL padrão](https://www.postgresql.org/docs/current/default-roles.html)
* *azure_pg_admin*
* *postgres*
* *citus*

O usuário administrador do servidor, *citus*, é membro da função *azure_pg_admin* .
No entanto, ele não faz parte da função *postgres* (super usuário).  Como o hiperscale é um serviço de PaaS gerenciado, somente a Microsoft faz parte da função de superusuário.

O mecanismo PostgreSQL usa privilégios para controlar o acesso a objetos de banco de dados, conforme discutido na [documentação do produto PostgreSQL](https://www.postgresql.org/docs/current/static/sql-createrole.html).
No banco de dados do Azure para PostgreSQL, o usuário administrador do servidor recebe estes privilégios: logon, superusuário, herdar, CreateDB, CreateRole, noreplication

## <a name="how-to-create-additional-users"></a>Como criar usuários adicionais

A conta do administrador do *citus* não tem permissão para criar usuários adicionais. Para adicionar um usuário, use o portal do Azure em vez disso.

1. Vá para a página **funções** do grupo de servidores de hiperescala e clique em **+ Adicionar**:

   ![A página funções](media/howto-hyperscale-create-users/1-role-page.png)

2. Insira o nome da função e a senha. Clique em **Guardar**.

   ![Adicionar função](media/howto-hyperscale-create-users/2-add-user-fields.png)

O usuário será criado no nó de coordenador do grupo de servidores e propagado para todos os nós de trabalho.

## <a name="how-to-delete-a-user-or-change-their-password"></a>Como excluir um usuário ou alterar sua senha

Vá para a página **funções** do grupo de servidores de hiperescala e clique nas reticências **...** ao lado de um usuário. As reticências abrirão um menu para excluir o usuário ou redefinir sua senha.

   ![Editar uma função](media/howto-hyperscale-create-users/edit-role.png)

A função *citus* é privilegiada e não pode ser excluída.

## <a name="how-to-modify-privileges-for-role"></a>Como modificar privilégios para função

As novas funções são normalmente usadas para fornecer acesso ao banco de dados com privilégios restritos. Para modificar privilégios de usuário, use comandos PostgreSQL padrão, usando uma ferramenta como PgAdmin ou psql. (Consulte [conectando-se com psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) no guia de início rápido de hiperescala (Citus).)

Por exemplo, para permitir que *DB_USER* leia *MyTable*, conceda a permissão:

```sql
GRANT SELECT ON mytable TO db_user;
```

O Citus (subscale) propaga instruções GRANT de tabela única por todo o cluster, aplicando-as em todos os nós de trabalho. No entanto, as concessões de todo o sistema (por exemplo, para todas as tabelas em um esquema) precisam ser executadas em cada nó de data.  Use a função auxiliar *run_command_on_workers ()* :

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="next-steps"></a>Passos seguintes

Abra o firewall para os endereços IP dos computadores dos novos usuários para permitir que eles se conectem: [criar e gerenciar regras de firewall de Citus (hiperescala) usando o portal do Azure](howto-hyperscale-manage-firewall-using-portal.md).

Para obter mais informações sobre o gerenciamento de conta de usuário de banco de dados, consulte a documentação do produto PostgreSQL:

* [Funções e privilégios de banco de dados](https://www.postgresql.org/docs/current/static/user-manag.html)
* [Sintaxe de concessão](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Direitos](https://www.postgresql.org/docs/current/static/ddl-priv.html)
