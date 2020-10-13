---
title: Criar bases de dados e utilizadores - Azure Database for MySQL
description: Este artigo descreve como criar novas contas de utilizador para interagir com uma Base de Dados Azure para o servidor MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: 3e1f24b3ae6133241660751293f52fec63dfbe73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91766879"
---
# <a name="create-databases-and-users-in-azure-database-for-mysql"></a>Criar bases de dados e utilizadores na Base de Dados Azure para o MySQL

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Este artigo descreve como criar utilizadores na Base de Dados Azure para o MySQL.

> [!NOTE]
> **Comunicação sem preconceitos**
>
> A Microsoft suporta um ambiente diversificado e inclusão. Este artigo contém referências à palavra *escravo.* O guia de estilo da Microsoft [para comunicação sem preconceitos](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) reconhece isto como uma palavra de exclusão. A palavra é usada neste artigo para consistência porque é a palavra que atualmente aparece no software. Quando o software for atualizado para remover a palavra, este artigo será atualizado para estar em alinhamento.
>

Quando criou pela primeira vez a sua Base de Dados Azure para o servidor MySQL, forneceu um nome de utilizador e senha de administração do servidor. Para mais informações, consulte este [Quickstart.](quickstart-create-mysql-server-database-using-azure-portal.md) Pode determinar o nome de utilizador do seu servidor no portal Azure.

O utilizador de administração do servidor tem estes privilégios: 

   SELECIONE, INSIRA, ATUALIZAÇÃO, APAGUE, CRIE, CRIE, RECARREGUE, PROCESSE, REFERÊNCIAS, INDEX, ALTER, MOSTRAR BASES DE DADOS, CRIAR TABELAS TEMPORÁRIAS, BLOQUEAR TABELAS, EXECUTAR, REPLICAR ESCRAVO, REPLICAR CLIENTE, CRIAR VISÃO, MOSTRAR, CRIAR ROTINA, ALTERAR ROTINA, CRIAR UTILIZADOR, EVENTO, TRIGGER


Depois de criar uma Base de Dados Azure para o servidor MySQL, pode utilizar a primeira conta de administração do servidor para criar utilizadores adicionais e conceder acesso administrativo aos mesmos. Também pode utilizar a conta de administração do servidor para criar utilizadores menos privilegiados que tenham acesso a esquemas de bases de dados individuais.

> [!NOTE]
> O papel de super privilégio e DBA não são apoiados. Reveja os [privilégios](concepts-limits.md#privileges--data-manipulation-support) no artigo de limitações para entender o que não é suportado no serviço.
>
> Os plugins de palavra-passe gostam `validate_password` e `caching_sha2_password` não são suportados pelo serviço.


## <a name="to-create-a-database-with-a-non-admin-user-in-azure-database-for-mysql"></a>Criar uma base de dados com um utilizador não administrador na Base de Dados Azure para o MySQL

1. Obtenha a informação de ligação e o nome de utilizador administrativo.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Pode encontrar facilmente o nome do servidor e informações de inscrição na página **'Vista Geral'** do servidor ou na página **Propriedades** no portal Azure.

2. Utilize a conta de administração e a palavra-passe para ligar ao servidor de base de dados. Utilize a sua ferramenta cliente preferida, como a MySQL Workbench, mysql.exe ou HeidiSQL.
   
   Se não tiver a certeza de como ligar, consulte [os dados de ligação e consulta para o Servidor Único](./connect-workbench.md) ou [conecte e consulte dados para o Servidor Flexível.](./flexible-server/connect-workbench.md)

3. Editar e executar o seguinte código SQL. Substitua o valor do espaço reservado `db_user` pelo novo nome de utilizador pretendido. Substitua o valor do espaço reservado `testdb` pelo nome da base de dados.

   Este código SQL cria uma nova base de dados chamada testdb. Em seguida, cria um novo utilizador no serviço MySQL e concede todos os privilégios para o novo esquema de base de dados (testdb. \* ) a esse utilizador.

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. Verifique as subvenções na base de dados:

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Inscreva-se no servidor, especificando a base de dados designada e utilizando o novo nome de utilizador e senha. Este exemplo mostra a linha de comando mysql. Quando utilizar este comando, será solicitado a palavra-passe do utilizador. Use o nome do seu próprio servidor, nome de base de dados e nome de utilizador.

   # <a name="single-server"></a>[Servidor Único](#tab/single-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
   # <a name="flexible-server"></a>[Servidor Flexível](#tab/flexible-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user -p
   ```
 ---

## <a name="to-create-additional-admin-users-in-azure-database-for-mysql"></a>Criar utilizadores de administração adicionais na Base de Dados Azure para o MySQL

1. Obtenha a informação de ligação e o nome de utilizador administrativo.
   Para ligar ao seu servidor de bases de dados, precisa do nome completo do servidor e das credenciais de início de sessão de administrador. Pode encontrar facilmente o nome do servidor e informações de inscrição na página **'Vista Geral'** do servidor ou na página **Propriedades** no portal Azure.

2. Utilize a conta de administração e a palavra-passe para ligar ao servidor de base de dados. Utilize a sua ferramenta cliente preferida, como a MySQL Workbench, mysql.exe ou HeidiSQL.
   
   Se não tiver a certeza de como ligar, consulte [a workbench MySQL para ligar e consultar dados.](./connect-workbench.md)

3. Editar e executar o seguinte código SQL. Substitua o valor do espaço reservado `new_master_user` pelo seu novo nome de utilizador. Esta sintaxe confere ao utilizador os privilégios listados em todos os esquemas de base de dados *(...* `new_master_user`

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. Verifique as subvenções:

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="azure_superuser"></a>azure_superuser

Todas as bases de dados Azure para servidores MySQL são criadas com um utilizador chamado "azure_superuser". Esta é uma conta de sistema criada pela Microsoft para gerir o servidor para realizar monitorização, backups e outras manutenção regulares. Os engenheiros de serviço também podem usar esta conta para aceder ao servidor durante um incidente com autenticação de certificados e devem solicitar o acesso usando processos just-in-time (JIT).

## <a name="next-steps"></a>Passos seguintes

Abra a firewall para os endereços IP das máquinas dos novos utilizadores para permitir a sua ligação:
- [Criar e gerir regras de firewall no Single Server](howto-manage-firewall-using-portal.md) 
- [ Criar e gerir regras de firewall no Servidor Flexível](flexible-server/how-to-connect-tls-ssl.md)

Para obter mais informações sobre a gestão da conta de utilizador, consulte a documentação do produto MySQL para [a gestão da conta do utilizador,](https://dev.mysql.com/doc/refman/5.7/en/access-control.html) [sintaxe GRANT](https://dev.mysql.com/doc/refman/5.7/en/grant.html)e [Privilégios.](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html)
