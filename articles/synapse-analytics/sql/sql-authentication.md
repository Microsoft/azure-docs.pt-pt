---
title: Autenticação SQL
description: Saiba mais sobre a autenticação SQL no Azure Synapse Analytics.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 505c0de5a508bd97b10091451116ec3670a20493
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677554"
---
# <a name="sql-authentication"></a>Autenticação SQL

O Azure Synapse Analytics tem dois fatores de forma SQL que lhe permitem controlar o consumo de recursos. Este artigo explica como os dois fatores de forma controlam a autenticação do utilizador.

Para autorizar a Synapse SQL, pode utilizar dois tipos de autorização:

- Autorização do Azure Active Directory
- Autorização de SQL

O Azure Ative Directory permite-lhe ter um lugar único para a gestão do utilizador. A autorização SQL permite que as aplicações antigas utilizem o SQL synapse de uma forma bem familiar.

## <a name="administrative-accounts"></a>Contas administrativas

Existem duas contas administrativas (**Administrador de servidor** e **Administrador do Active Directory**) que atuam como administradores. Para identificar estas contas de administrador para o seu servidor SQL, abra o portal Azure e navegue no separador Propriedades do seu SQL Synapse.

![Administradores do SQL Server](./media/sql-authentication/sql-admins.png)

- **Administrador de servidor**

  Quando criar um Azure Synapse Analytics, deve nomear um **login de administração do Servidor**. O servidor SQL cria essa conta como um início de sessão na base de dados mestra. Liga-se através da autenticação do SQL Server (nome de utilizador e palavra-passe). Só pode existir uma destas contas.

- **Administrador do Azure Active Directory**

  Também é possível configurar uma conta do Azure Active Directory, seja esta individual ou de grupo de segurança, como administrador. É opcional configurar um administrador AD Azure, mas um administrador AD Azure **deve** ser configurado se quiser usar contas AD Azure para ligar ao Sinaapse SQL.

As contas **de administração do Servidor** e **Azure AD** têm as seguintes características:

- São as únicas contas que podem ligar-se automaticamente a qualquer Base de Dados SQL no servidor. (Para ligar a uma base de dados de utilizador, as outras contas têm de ser o proprietário da base de dados ou ter uma conta de utilizador na base de dados de utilizador.)
- Estas contas introduzem bases de dados de utilizador como o utilizador `dbo` e têm todas as permissões nas bases de dados de utilizador. (O proprietário de uma base de dados de utilizador também introduz a base de dados como o utilizador `dbo`.)
- Não introduza a `master` base de dados como `dbo` utilizador e tenha permissões limitadas em mestre.
- **Não** são membros da função de servidor fixo SQL Server `sysadmin` padrão, que não está disponível na Base de Dados SQL.  
- Pode criar, alterar e largar bases de dados, logins, utilizadores em regras de firewall ip de nível principal e servidor.
- Pode adicionar e remover membros aos `dbmanager` `loginmanager` papéis e funções.
- Pode ver a tabela do `sys.sql_logins` sistema.

## <a name="serverless-sql-pool"></a>[Piscina SQL sem servidor](#tab/serverless)

Para gerir os utilizadores que tenham acesso à piscina SQL sem servidor, pode utilizar as instruções abaixo.

Para criar um login para a piscina SQL sem servidor, utilize a seguinte sintaxe:

```sql
CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
-- or
CREATE LOGIN Mary@domainname.net FROM EXTERNAL PROVIDER;
```
Uma vez que o login exista, pode criar utilizadores nas bases de dados individuais dentro do ponto final do pool SQL sem servidor e conceder permissões necessárias a estes utilizadores. Para criar uma utilização, pode utilizar a seguinte sintaxe:
```sql
CREATE USER Mary FROM LOGIN Mary;
-- or
CREATE USER Mary FROM LOGIN Mary@domainname.net;
-- or
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Uma vez criado o login e o utilizador, pode utilizar a sintaxe regular do SQL Server para conceder direitos.

## <a name="sql-pool"></a>[Conjunto de SQL](#tab/provisioned)

### <a name="administrator-access-path"></a>Caminho de acesso do administrador

Quando a firewall ao nível do servidor está configurada corretamente, o **administrador de servidor SQL** e o **administrador do Azure Active Directory** podem ligar-se com ferramentas de cliente como o SQL Server Management Studio ou os SQL Server Data Tools. Só as ferramentas mais recentes proporcionam todas as funcionalidades e capacidades. 

O diagrama a seguir mostra uma configuração típica para as duas contas do administrador:
 
![configuração das duas contas da administração](./media/sql-authentication/1sql-db-administrator-access.png)

Quando utilizar uma porta aberta na firewall ao nível do servidor, os administradores podem ligar-se a qualquer Base de Dados SQL.

### <a name="database-creators"></a>Criadores de base de dados

Uma destas funções administrativas é o papel **de dbmanager.** Os membros desta função podem criar novas bases de dados. Para utilizar esta função, crie um utilizador na base de dados `master` e, em seguida, adicione o utilizador à função de base de dados **dbmanager**. 

Para criar uma base de dados, o utilizador deve ser um utilizador baseado num login do SQL Server na `master` base de dados ou no utilizador da base de dados contido com base num utilizador do Azure Ative Directory.

1. Utilizando uma conta de administrador, ligue-se à `master` base de dados.
2. Crie um login de autenticação SQL Server, utilizando a declaração [DE LOGIN CREATE.](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true) Instrução de exemplo:

   ```sql
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```

   > [!NOTE]
   > Utilize uma palavra-passe forte ao criar um utilizador de base de dados contido ou de início de sessão. Para obter mais informações, veja [Strong Passwords (Palavras-passe Fortes)](/sql/relational-databases/security/strong-passwords?view=azure-sqldw-latest&preserve-view=true).

   Para melhorar o desempenho, os início de sessão (principais ao nível do servidor) são temporariamente colocados em cache ao nível da base de dados. Para atualizar a cache de autenticação, veja [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql?view=azure-sqldw-latest&preserve-view=true).

3. Crie um utilizador de bases de dados utilizando a declaração [DO UTILIZADOR CREATE.](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest&preserve-view=true) O utilizador pode ser um utilizador de autenticação do Azure Ative Directory (se configurar o seu ambiente para a autenticação AD AZure), ou um utilizador de autenticação sql Server contido na base de dados, ou um utilizador de autenticação do SQL Server com base num login de autenticação do SQL Server (criado no passo anterior).) Declarações de amostras:

   ```sql
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER; -- To create a user with Azure Active Directory
   CREATE USER Ann WITH PASSWORD = '<strong_password>'; -- To create a SQL Database contained database user
   CREATE USER Mary FROM LOGIN Mary;  -- To create a SQL Server user based on a SQL Server authentication login
   ```

4. Adicione o novo utilizador, à função de base de dados **dbmanager** na `master` utilização do procedimento [de sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=azure-sqldw-latest&preserve-view=true) (note que a declaração [ALTER ROLE](/sql/t-sql/statements/alter-role-transact-sql?view=azure-sqldw-latest&preserve-view=true) não é suportada no SQL a provisionado). Instruções de exemplo:

   ```sql
   EXEC sp_addrolemember 'dbmanager', 'Mary'; 
   EXEC sp_addrolemember 'dbmanager', 'mike@contoso.com]'; 
   ```

   > [!NOTE]
   > O dbmanager é uma função de base de dados na base de dados mestra, pelo que só pode adicionar um utilizador de base de dados à função dbmanager. Não pode adicionar um início de sessão ao nível do servidor à função de nível de base de dados.

5. Se for necessário, configure uma regra de firewall para permitir que o novo utilizador se ligue. (O novo utilizador poderá ser abrangido por uma regra de firewall existente.)

Agora o utilizador pode ligar-se à `master` base de dados e criar novas bases de dados. A conta que cria a base de dados torna-se na proprietária da base de dados.

### <a name="login-managers"></a>Gestores de início de sessão

A outra função administrativa é a função de gestor de início de sessão. Os membros desta função podem criar novos inícios de sessão na base de dados mestra. Se pretender, pode seguir os mesmos passos (criar um início de sessão e um utilizador e adicionar um utilizador à função **loginmanager**) para permitir que um utilizador crie novos inícios de sessão na base de dados mestra. Normalmente, os logins não são necessários, uma vez que a Microsoft recomenda a utilização de utilizadores de bases de dados contidos, que autenticam ao nível da base de dados em vez de utilizarem os utilizadores com base em logins. Para obter mais informações, veja [Contained Database Users - Making Your Database Portable (Utilizadores de Base de Dados Contidos – Tornar a Sua Base de Dados Portátil)](/sql/relational-databases/security/contained-database-users-making-your-database-portable?view=azure-sqldw-latest&preserve-view=true).

---

## <a name="non-administrator-users"></a>Utilizadores não administradores

Geralmente, as contas não-administradoras não precisam de acesso à base de dados principal. Utilize a instrução [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql) para criar os utilizadores de base de dados contidos na base de dados ao nível da base de dados. 

O utilizador pode ser um utilizador de base de dados contido do Azure Active Directory (se tiver configurado o ambiente para autenticação do Azure AD), um utilizador de base de dados contido de autenticação do SQL Server ou um utilizador de autenticação do SQL Server com base num início de sessão de autenticação do SQL Server (criado no passo anterior).  

Para criar utilizadores, ligue à base de dados e execute instruções semelhantes aos exemplos seguintes:

```sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Inicialmente, apenas um dos administradores ou o proprietário da base de dados pode criar utilizadores. Para permitir que outros utilizadores criem utilizadores novos, conceda-lhes a permissão `ALTER ANY USER`, mediante uma instrução como:

```sql
GRANT ALTER ANY USER TO Mary;
```

Para dar aos utilizadores adicionais o controlo total da base de dados, faça-os um membro da **db_owner** papel de base de dados fixa.

Na Base de Dados Azure SQL ou na sinapse sem servidor, utilize a `ALTER ROLE` declaração.

```sql
ALTER ROLE db_owner ADD MEMBER Mary;
```

Na piscina de SQL dedicada utilize [sp_addrolemember EXEC](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=azure-sqldw-latest&preserve-view=true).

```sql
EXEC sp_addrolemember 'db_owner', 'Mary';
```

> [!NOTE]
> Uma razão comum para criar um utilizador de base de dados com base num login de servidor é para utilizadores que precisam de acesso a várias bases de dados. Uma vez que os utilizadores de bases de dados contidos são entidades individuais, cada base de dados mantém o seu próprio utilizador e a sua própria senha. Isto pode causar sobrecarga, uma vez que o utilizador deve então lembrar cada palavra-passe de cada base de dados, e pode tornar-se insustentável quando tem de alterar várias palavras-passe para muitas bases de dados. No entanto, ao utilizar logins do SQL Server e alta disponibilidade (grupos de geo-replicação e falha), os logins do SQL Server devem ser definidos manualmente em cada servidor. Caso contrário, o utilizador da base de dados deixará de ser mapeado para o login do servidor após a ocorrência de uma falha, e não poderá aceder ao registo de registo de base de dados por falha. 

Para obter mais informações sobre a configuração de logins para a geo-replicação, consulte  [configurar e gerir a segurança da Base de Dados Azure SQL para geo-restauro ou falha](../../azure-sql/database/active-geo-replication-security-configure.md).

### <a name="configuring-the-database-level-firewall"></a>Configurar a firewall ao nível da base de dados

Como melhor prática, os utilizadores não administradores só devem ter acesso às bases de dados que utilizam através da firewall. Em vez de autorizar os endereços IP deles através da firewall ao nível do servidor e conceder acesso a todas as bases de dados, utilize a instrução [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database?view=azure-sqldw-latest&preserve-view=true) para configurar a firewall ao nível da base de dados. Não é possível configurar a firewall ao nível da base de dados no portal.

### <a name="non-administrator-access-path"></a>Caminho de acesso para não administradores

Quando a firewall ao nível da base de dados está devidamente configurada, os utilizadores da base de dados podem ligar através de ferramentas de cliente, como o SQL Server Management Studio ou o SQL Server Data Tools. Só as ferramentas mais recentes proporcionam todas as funcionalidades e capacidades. O diagrama seguinte mostra um caminho típico de acesso para não administradores.

![Caminho de acesso para não administradores](./media/sql-authentication/2sql-db-nonadmin-access.png)

## <a name="groups-and-roles"></a>Grupos e funções

A gestão de acessos eficaz utiliza permissões atribuídas a grupos e funções em vez de utilizadores individuais.

- Quando utilizar a autenticação do Azure Active Directory, coloque os utilizadores do Azure Active Directory num grupo do Azure Active Directory. Crie um utilizador de base de dados contido para o grupo. Coloque um ou mais utilizadores de base de dados numa [função de base de dados](/sql/relational-databases/security/authentication-access/database-level-roles?view=azure-sqldw-latest&preserve-view=true) e, em seguida, atribua [permissões](/sql/relational-databases/security/permissions-database-engine?view=azure-sqldw-latest&preserve-view=true) à função de base de dados.

- Ao utilizar a autenticação do SQL Server, crie utilizadores de base de dados contida na base de dados. Coloque um ou mais utilizadores de base de dados numa [função de base de dados](/sql/relational-databases/security/authentication-access/database-level-roles?view=azure-sqldw-latest&preserve-view=true) e, em seguida, atribua [permissões](/sql/relational-databases/security/permissions-database-engine?view=azure-sqldw-latest&preserve-view=true) à função de base de dados.

As funções de base de dados podem ser as funções incorporadas, tais como **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** e **db_denydatareader**. **db_owner** é, geralmente, utilizada para conceder permissão total apenas a alguns utilizadores. As outras funções de base de dados fixas são úteis para colocar bases de dados simples em desenvolvimento rapidamente, mas não são recomendadas para a maioria das bases de dados de produção. 

Por exemplo, a função de base de dados fixa **db_datareader** concede acesso de leitura a todas as tabelas na base de dados, o que, regra geral, é mais do que o estritamente necessário. 

É muito melhor usar a declaração [CREATE ROLE](/sql/t-sql/statements/create-role-transact-sql) para criar as suas próprias funções de base de dados definidas pelo utilizador e conceder cuidadosamente a cada papel as menos permissões necessárias para a necessidade do negócio. Quando um utilizador é membro de várias funções, agrega as permissões de todas essas funções.

## <a name="permissions"></a>Permissões

Existem mais de 100 permissões que podem ser individualmente concedidas ou negadas na Base de Dados SQL. Muitas destas permissões são aninhadas. Por exemplo, a permissão `UPDATE` num esquema inclui a permissão `UPDATE` em cada tabela dentro desse esquema. Tal como na maioria dos sistemas de permissões, a recusa de uma permissão sobrepõe-se a uma atribuição. 

Devido à natureza aninhada e ao número de permissões, desenhar um sistema de permissões que proteja as bases de dados adequadamente pode envolver um cuidadoso estudo. 

Comece com a lista de permissões em [Permissions (Database Engine) (Permissões [Motor de Base de Dados])](/sql/relational-databases/security/permissions-database-engine) e reveja o [gráfico em tamanho de cartaz](/sql/relational-databases/security/media/database-engine-permissions.png) das permissões.

### <a name="considerations-and-restrictions"></a>Considerações e restrições

Ao gerir logins e utilizadores na Base de Dados SQL, considere os seguintes pontos:

- Deve estar ligado à base de **dados principal** ao executar as `CREATE/ALTER/DROP DATABASE` declarações.
- O utilizador da base de dados correspondente ao login **de administração do Servidor** não pode ser alterado ou eliminado.
- O inglês dos E.U.A. é o idioma predefinido do início de sessão do **Administrador de servidor**.
- Só os administradores (início de sessão do **Administrador de servidor** ou administrador do Azure AD) e os membros da função de base de dados **dbmanager** ba base de dados **mestra** têm permissão para executar as declarações `CREATE DATABASE` e `DROP DATABASE`.
- Tem de estar ligado à base de dados mestra ao executar as instruções `CREATE/ALTER/DROP LOGIN`. No entanto, não é aconselhável utilizar inícios de sessão. Utilize os utilizadores de base de dados contida.
- Para ligar a uma base de dados do utilizador, tem de fornecer o nome da base de dados na cadeia de ligação.
- Apenas o início de sessão principal ao nível do servidor e os membros da função de base de dados **loginmanager** na base de dados **mestra** têm permissão para executar as instruções `CREATE LOGIN`, `ALTER LOGIN`, e `DROP LOGIN`.
- Ao executar as `CREATE/ALTER/DROP LOGIN` declarações e `CREATE/ALTER/DROP DATABASE` declarações numa aplicação ADO.NET, não é permitido usar comandos parametrizados. Para obter mais informações, consulte [Comandos e Parâmetros](/dotnet/framework/data/adonet/commands-and-parameters).
- Ao executar as instruções `CREATE/ALTER/DROP DATABASE` e `CREATE/ALTER/DROP LOGIN`, cada uma das seguintes declarações tem de ser a única instrução num batch do Transact-SQL. Caso contrário, ocorrerá um erro. Por exemplo, o Transact-SQL seguinte verifica se a base de dados existe. Se existir, é chamada uma instrução `DROP DATABASE` para remover a base de dados. Uma vez que a instrução `DROP DATABASE` não é a única instrução no batch, executar a seguinte instrução do Transact-SQL ocorre um erro.

  ```sql
  IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
  DROP DATABASE [database_name];
  GO
  ```
  
  Em vez disso, utilize a seguinte declaração Transact-SQL:
  
  ```sql
  DROP DATABASE IF EXISTS [database_name]
  ```

- Ao executar a `CREATE USER` com a opção `FOR/FROM LOGIN`, tem de ser a única instrução num batch do Transact-SQL.
- Ao executar a `ALTER USER` com a opção `WITH LOGIN`, tem de ser a única instrução num batch do Transact-SQL.
- Para `CREATE/ALTER/DROP`, um utilizador necessita da permissão `ALTER ANY USER` na base de dados.
- Quando o proprietário de uma função de base de dados tenta adicionar ou remover outro utilizador de base de dados de ou para essa função de base de dados, pode ocorrer o seguinte erro: **o utilizador ou a função "Nome" não existe nesta base de dados.** Este erro ocorre porque o utilizador não é visível para o proprietário. Para resolver este problema, conceda uma permissão de `VIEW DEFINITION` ao proprietário da função. 

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja [Contained Database Users - Making Your Database Portable (Utilizadores de Base de Dados Contidos – Tornar a Sua Base de Dados Portátil)](/sql/relational-databases/security/contained-database-users-making-your-database-portable).
