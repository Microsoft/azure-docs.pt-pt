---
title: Criar utilizadores - Azure Database for MySQL
description: Este artigo descreve como pode criar novas contas de utilizador para interagir com uma Base de Dados Azure para o servidor MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/2/2020
ms.openlocfilehash: 606279c0db92ad3aeb76d4f7a1a914f14348dd8f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84608473"
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Criar utilizadores na Base de Dados Azure para o servidor MySQL

Este artigo descreve como pode criar utilizadores numa Base de Dados Azure para o servidor MySQL.

> [!NOTE]
> Comunicação sem preconceitos
>
> A Microsoft suporta um ambiente diversificado e inclusão. Este artigo contém referências à palavra _escravo._ O guia de estilo da Microsoft [para comunicação sem preconceitos](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) reconhece isto como uma palavra de exclusão. A palavra é usada neste artigo para consistência porque atualmente é a palavra que aparece no software. Quando o software for atualizado para remover a palavra, este artigo será atualizado para estar em alinhamento.
>

Quando criou pela primeira vez a sua Base de Dados Azure para o MySQL, forneceu um nome de utilizador de login de administração de servidor e senha. Para mais informações, pode seguir o [Quickstart.](quickstart-create-mysql-server-database-using-azure-portal.md) Pode localizar o nome de utilizador do seu servidor a partir do portal Azure.

O utilizador de administração do servidor obtém certos privilégios para o seu servidor listado: SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY Tables, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE, EVENT, TRIGGER, TRIGGER, TRIGGER, TRIGGER, TRIGGER, TRIGGER, TRIGGER, TRIGGER, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, CREATE, EVENT, TRIGGER, TRIGGER, TRIGGER, TRIGGER, TRIGGER, TRIGGER, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE, EVENT, TRIGGER, TRIGGER, TRIGGER, TRIGGER, TRIGGER, TRIGGER, TRIGGER, TRIGGER, TRIGGER, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, CREATE, EVENT, TRIGGER, TRIGGER, TRIGGER, TRIGGER, TRIGGER, TRIGGER, TRIGGER, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE, EVENT, TRIGGER, TRIGGER, TRIGGER, TRIGGER, TRIGGER, TRIGGER, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW

Uma vez criada a Base de Dados Azure para o servidor MySQL, pode utilizar a primeira conta de utilizador de administração do servidor para criar utilizadores adicionais e conceder acesso administrativo aos mesmos. Além disso, a conta de administração do servidor pode ser usada para criar utilizadores menos privilegiados que tenham acesso a esquemas de bases de dados individuais.

> [!NOTE]
> O papel de SUPER privilégio e DBA não são apoiados. Reveja os [privilégios](concepts-limits.md#privilege-support) no artigo de limitações para entender o que não é suportado no serviço.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Como criar utilizadores de administração adicionais na Base de Dados Azure para o MySQL

1. Obtenha a informação de ligação e o nome de utilizador administrativo.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Pode encontrar facilmente o nome do servidor e informações de inscrição a partir da página **'Vista Geral'** do servidor ou da página **Propriedades** no portal Azure.

2. Utilize a conta de administração e a palavra-passe para ligar ao servidor de base de dados. Utilize a sua ferramenta cliente preferida, como mySQL Workbench, mysql.exe, HeidiSQL, ou outros.
   Se não tiver a certeza de como se conectar, consulte [a workbench MySQL para ligar e consultar dados](./connect-workbench.md)

3. Editar e executar o seguinte código SQL. Substitua o seu novo nome de utilizador pelo valor do espaço reservado `new_master_user` . Esta sintaxe concede os privilégios listados em todos os esquemas de base de dados *(.*) ao nome de utilizador (new_master_user neste exemplo).

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. Verifique as subvenções

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Como criar utilizadores de bases de dados na Base de Dados Azure para o MySQL

1. Obtenha a informação de ligação e o nome de utilizador administrativo.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Pode encontrar facilmente o nome do servidor e informações de inscrição a partir da página **'Vista Geral'** do servidor ou da página **Propriedades** no portal Azure.

2. Utilize a conta de administração e a palavra-passe para ligar ao servidor de base de dados. Utilize a sua ferramenta cliente preferida, como mySQL Workbench, mysql.exe, HeidiSQL, ou outros.
   Se não tiver a certeza de como se conectar, consulte [a workbench MySQL para ligar e consultar dados](./connect-workbench.md)

3. Editar e executar o seguinte código SQL. Substitua o valor do espaço reservado `db_user` pelo novo nome de utilizador pretendido e o valor do espaço reservado pelo seu próprio nome de base `testdb` de dados.

   Esta sintaxe de código sql cria uma nova base de dados chamada testdb para fins de exemplo. Em seguida, cria um novo utilizador no serviço MySQL e concede todos os privilégios ao novo esquema de base de dados (testdb) \* para esse utilizador.

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. Verifique os subsídios dentro da base de dados.

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Faça login no servidor, especificando a base de dados designada, utilizando o novo nome de utilizador e senha. Este exemplo mostra a linha de comando mysql. Com este comando, é solicitado a palavra-passe para o nome de utilizador. Substitua o nome do seu próprio servidor, nome de base de dados e nome de utilizador.

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>Próximos passos

Abra a firewall para os endereços IP das máquinas dos novos utilizadores para permitir a sua ligação: [Criar e gerir as regras de firewall do Azure para o MySQL, utilizando o portal Azure](howto-manage-firewall-using-portal.md) ou O [Azure CLI](howto-manage-firewall-using-cli.md).

Para obter mais informações sobre a gestão da conta de utilizador, consulte a documentação do produto MySQL para [a gestão da conta de utilizador,](https://dev.mysql.com/doc/refman/5.7/en/access-control.html) [grant syntax](https://dev.mysql.com/doc/refman/5.7/en/grant.html)e [Privileges](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
