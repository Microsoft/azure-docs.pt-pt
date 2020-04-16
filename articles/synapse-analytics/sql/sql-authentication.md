---
title: Autenticação SQL
description: Saiba mais sobre a autenticação SQL no Azure Synapse Analytics.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 2b80efa30ac7e04b9eb21dd6f8a39ab4ee90adf6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424854"
---
# <a name="sql-authentication"></a>Autenticação SQL

A Azure Synapse Analytics tem dois fatores de forma SQL que lhe permitem controlar o seu consumo de recursos. Este artigo explica como os dois fatores de forma controlam a autenticação do utilizador.

Para autorizar o Synapse SQL, pode utilizar dois tipos de autorização:

- Autorização da AAD
- Autorização SQL

A autorização aAD baseia-se no Diretório Ativo do Azure e permite-lhe ter um único lugar para a gestão do utilizador. A autorização SQL permite que aplicações antigas utilizem synapse SQL de uma forma bem familiar.

## <a name="administrative-accounts"></a>Contas administrativas

Existem duas contas administrativas (**Administrador de servidor** e **Administrador do Active Directory**) que atuam como administradores. Para identificar estas contas de administrador para o seu servidor SQL, abra o portal Azure e navegue para o separador Propriedades do seu SYnapse SQL.

![Administradores do SQL Server](./media/sql-authentication/sql-admins.png)

- **Administrador de servidor**

  Quando criar um Azure Synapse Analytics, deve designar um **login**de administrador do Servidor . O servidor SQL cria essa conta como um início de sessão na base de dados mestra. Liga-se através da autenticação do SQL Server (nome de utilizador e palavra-passe). Só pode existir uma destas contas.

- **Administrador do Azure Active Directory**

  Também é possível configurar uma conta do Azure Active Directory, seja esta individual ou de grupo de segurança, como administrador. É opcional configurar um administrador da AD Azure, mas um administrador da AD Azure **deve** ser configurado se quiser utilizar contas Azure AD para ligar ao Synapse SQL.

As contas de **administração** do Server e **azure d.D.** têm as seguintes características:

- São as únicas contas que podem ligar-se automaticamente a qualquer Base de Dados SQL no servidor. (Para ligar a uma base de dados de utilizador, as outras contas têm de ser o proprietário da base de dados ou ter uma conta de utilizador na base de dados de utilizador.)
- Estas contas introduzem bases de dados de utilizador como o utilizador `dbo` e têm todas as permissões nas bases de dados de utilizador. (O proprietário de uma base de dados de utilizador também introduz a base de dados como o utilizador `dbo`.)
- Não introduza `master` a `dbo` base de dados como utilizador e tenha permissões limitadas em mestrado.
- **Não** são membros da função padrão do servidor fixo Do Servidor `sysadmin` SQL, que não está disponível na base de dados SQL.  
- Pode criar, alterar e largar bases de dados, logins, utilizadores em regras de firewall IP de nível de servidor.
- Pode adicionar e remover `dbmanager` `loginmanager` membros para as funções e e funções.
- Pode ver `sys.sql_logins` a tabela do sistema.

## <a name="sql-on-demand-preview"></a>SQL on-demand (pré-visualização)

Para gerir os utilizadores que têm acesso à SQL a pedido, pode utilizar as instruções abaixo.

Para criar um login na SQL a pedido, utilize a seguinte sintaxe:

```sql
CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
-- or
CREATE LOGIN Mary@domainname.net FROM EXTERNAL PROVIDER;
```
Uma vez que o login exista, pode criar utilizadores nas bases de dados individuais dentro do ponto final de pedido da SQL e conceder permissões necessárias a estes utilizadores. Para criar uma utilização, pode utilizar a seguinte sintaxe:
```sql
CREATE USER Mary FROM LOGIN Mary;
-- or
CREATE USER Mary FROM LOGIN Mary@domainname.net;
-- or
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Assim que o login e o utilizador forem criados, pode utilizar a sintaxe regular do SQL Server para conceder direitos.

## <a name="sql-pool"></a>Piscina SQL

### <a name="administrator-access-path"></a>Caminho de acesso do administrador

Quando a firewall ao nível do servidor está configurada corretamente, o **administrador de servidor SQL** e o **administrador do Azure Active Directory** podem ligar-se com ferramentas de cliente como o SQL Server Management Studio ou os SQL Server Data Tools. Só as ferramentas mais recentes proporcionam todas as funcionalidades e capacidades. 

O diagrama seguinte mostra uma configuração típica para as duas contas de administrador:
 
![configuração das duas contas de administração](./media/sql-authentication/1sql-db-administrator-access.png)

Quando utilizar uma porta aberta na firewall ao nível do servidor, os administradores podem ligar-se a qualquer Base de Dados SQL.

### <a name="database-creators"></a>Criadores de base de dados

Uma dessas funções administrativas é o papel **de gestor.** Os membros desta função podem criar novas bases de dados. Para utilizar esta função, crie um utilizador na base de dados `master` e, em seguida, adicione o utilizador à função de base de dados **dbmanager**. 

Para criar uma base de dados, o utilizador deve ser um `master` utilizador baseado num login do SQL Server na base de dados ou no utilizador da base de dados com base num utilizador do Diretório Ativo Do Azure.

1. Utilizando uma conta de `master` administrador, ligue-se à base de dados.
2. Crie um login de autenticação do Servidor SQL, utilizando a declaração [CREATE LOGIN.](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Instrução de exemplo:

   ```sql
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```

   > [!NOTE]
   > Utilize uma palavra-passe forte ao criar um utilizador de base de dados contido ou de início de sessão. Para obter mais informações, veja [Strong Passwords (Palavras-passe Fortes)](/sql/relational-databases/security/strong-passwords?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

   Para melhorar o desempenho, os início de sessão (principais ao nível do servidor) são temporariamente colocados em cache ao nível da base de dados. Para atualizar a cache de autenticação, veja [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

3. Na `master` base de dados, crie um utilizador utilizando a declaração CREATE [USER.](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) O utilizador pode ser um utilizador de autenticação De cabeça de dados Do Diretório Ativo Do Azure (se configurar o seu ambiente para autenticação AD Azure), ou uma autenticação do Servidor SQL que continha um utilizador de base de dados, ou um utilizador de autenticação do Servidor SQL baseado num login de autenticação do Servidor SQL (criado na fase anterior.) Declarações de amostra:

   ```sql
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER; -- To create a user with Azure Active Directory
   CREATE USER Ann WITH PASSWORD = '<strong_password>'; -- To create a SQL Database contained database user
   CREATE USER Mary FROM LOGIN Mary;  -- To create a SQL Server user based on a SQL Server authentication login
   ```

4. Adicione o novo utilizador à função `master` de base de dados **dbmanager** na utilização da declaração [ALTER ROLE.](/sql/t-sql/statements/alter-role-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Instruções de exemplo:

   ```sql
   ALTER ROLE dbmanager ADD MEMBER Mary;
   ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
   ```

   > [!NOTE]
   > O dbmanager é uma função de base de dados na base de dados mestra, pelo que só pode adicionar um utilizador de base de dados à função dbmanager. Não pode adicionar um início de sessão ao nível do servidor à função de nível de base de dados.

5. Se for necessário, configure uma regra de firewall para permitir que o novo utilizador se ligue. (O novo utilizador poderá ser abrangido por uma regra de firewall existente.)

Agora o utilizador pode `master` ligar-se à base de dados e criar novas bases de dados. A conta que cria a base de dados torna-se na proprietária da base de dados.

### <a name="login-managers"></a>Gestores de início de sessão

A outra função administrativa é a função de gestor de início de sessão. Os membros desta função podem criar novos inícios de sessão na base de dados mestra. Se pretender, pode seguir os mesmos passos (criar um início de sessão e um utilizador e adicionar um utilizador à função **loginmanager**) para permitir que um utilizador crie novos inícios de sessão na base de dados mestra. Normalmente, os inícios de sessão não são necessários, uma vez que a Microsoft recomenda a utilização de utilizadores de base de dados contidos, o que autentica ao nível da base de dados em vez de utilizar os utilizadores com base em inícios de sessão. Para obter mais informações, veja [Contained Database Users - Making Your Database Portable (Utilizadores de Base de Dados Contidos - Tornar a Sua Base de Dados Portátil)](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="non-administrator-users"></a>Utilizadores não administradores

Geralmente, as contas de não administrador não precisam de acesso à base de dados mestra. Utilize a instrução [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx) para criar os utilizadores de base de dados contidos na base de dados ao nível da base de dados. 

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

Para dar aos utilizadores mais controlo da base de dados, faça-os membros da **função db_owner** base de dados fixa.

Na Base de Dados Azure `ALTER ROLE` SQL, utilize a declaração.

```sql
ALTER ROLE db_owner ADD MEMBER Mary;
```

Na piscina SQL utilize [sp_addrolemember EXEC](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

```sql
EXEC sp_addrolemember 'db_owner', 'Mary';
```

> [!NOTE]
> Uma razão comum para criar um utilizador de base de dados baseado num login do servidor da Base de Dados SQL é para utilizadores que precisam de acesso a várias bases de dados. Uma vez que os utilizadores de bases de dados contidos são entidades individuais, cada base de dados mantém o seu próprio utilizador e a sua própria palavra-passe. Isto pode causar sobrecarga, uma vez que o utilizador deve então lembrar-se de cada palavra-passe para cada base de dados, e pode tornar-se insustentável quando tem de alterar várias palavras-passe para muitas bases de dados. No entanto, ao utilizar logins de servidor SQL e alta disponibilidade (geo-replicação ativa e grupos de failover), os logins do Servidor SQL devem ser definidos manualmente em cada servidor. Caso contrário, o utilizador da base de dados deixará de ser mapeado para o login do servidor após a ocorrência de uma falha, e não poderá aceder à falha da publicação da base de dados. 

Para obter mais informações sobre a configuração de logins para geo-replicação, consulte configure e gere nciásse a segurança da Base de [Dados Azure SQL para geo-restaurar ou falhar](../../sql-database/sql-database-geo-replication-security-config.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="configuring-the-database-level-firewall"></a>Configurar a firewall ao nível da base de dados

Como melhor prática, os utilizadores não administradores só devem ter acesso às bases de dados que utilizam através da firewall. Em vez de autorizar os endereços IP deles através da firewall ao nível do servidor e conceder acesso a todas as bases de dados, utilize a instrução [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para configurar a firewall ao nível da base de dados. Não é possível configurar a firewall ao nível da base de dados no portal.

### <a name="non-administrator-access-path"></a>Caminho de acesso para não administradores

Quando a firewall ao nível da base de dados está devidamente configurada, os utilizadores da base de dados podem ligar através de ferramentas de cliente, como o SQL Server Management Studio ou o SQL Server Data Tools. Só as ferramentas mais recentes proporcionam todas as funcionalidades e capacidades. O diagrama seguinte mostra um caminho típico de acesso para não administradores.

![Caminho de acesso para não administradores](./media/sql-authentication/2sql-db-nonadmin-access.png)

## <a name="groups-and-roles"></a>Grupos e funções

A gestão de acessos eficaz utiliza permissões atribuídas a grupos e funções em vez de utilizadores individuais.

- Quando utilizar a autenticação do Azure Active Directory, coloque os utilizadores do Azure Active Directory num grupo do Azure Active Directory. Crie um utilizador de base de dados contido para o grupo. Coloque um ou mais utilizadores de base de dados numa [função de base de dados](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e, em seguida, atribua [permissões](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) à função de base de dados.

- Ao utilizar a autenticação do SQL Server, crie utilizadores de base de dados contida na base de dados. Coloque um ou mais utilizadores de base de dados numa [função de base de dados](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e, em seguida, atribua [permissões](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) à função de base de dados.

As funções de base de dados podem ser as funções incorporadas, tais como **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** e **db_denydatareader**. **db_owner** é, geralmente, utilizada para conceder permissão total apenas a alguns utilizadores. As outras funções de base de dados fixas são úteis para colocar bases de dados simples em desenvolvimento rapidamente, mas não são recomendadas para a maioria das bases de dados de produção. 

Por exemplo, a função de base de dados fixa **db_datareader** concede acesso de leitura a todas as tabelas na base de dados, o que, regra geral, é mais do que o estritamente necessário. 

É muito melhor utilizar a instrução [CREATE ROLE](https://msdn.microsoft.com/library/ms187936.aspx) para criar as suas próprias funções de base de dados definidas pelo utilizador e conceder, cuidadosamente, a cada função o menor número de permissões necessárias para a atividade em causa. Quando um utilizador é membro de várias funções, agrega as permissões de todas essas funções.

## <a name="permissions"></a>Permissões

Existem mais de 100 permissões que podem ser individualmente concedidas ou negadas na Base de Dados SQL. Muitas destas permissões são aninhadas. Por exemplo, a permissão `UPDATE` num esquema inclui a permissão `UPDATE` em cada tabela dentro desse esquema. Tal como na maioria dos sistemas de permissões, a recusa de uma permissão sobrepõe-se a uma atribuição. 

Devido à natureza aninhada e ao número de permissões, desenhar um sistema de permissões que proteja as bases de dados adequadamente pode envolver um cuidadoso estudo. 

Comece com a lista de permissões em [Permissions (Database Engine) (Permissões [Motor de Base de Dados])](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) e reveja o [gráfico em tamanho de cartaz](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) das permissões.

### <a name="considerations-and-restrictions"></a>Considerações e restrições

Ao gerir logins e utilizadores na Base de Dados SQL, considere os seguintes pontos:

- Deve estar ligado **master** à base de `CREATE/ALTER/DROP DATABASE` dados principal ao executar as declarações.
- O utilizador da base de dados que corresponde ao início de sessão do **Administrador de servidor** não pode ser alterado ou removido.
- O inglês dos E.U.A. é o idioma predefinido do início de sessão do **Administrador de servidor**.
- Só os administradores (início de sessão do **Administrador de servidor** ou administrador do Azure AD) e os membros da função de base de dados **dbmanager**ba base de dados **mestra** têm permissão para executar as declarações `CREATE DATABASE` e `DROP DATABASE`.
- Tem de estar ligado à base de dados mestra ao executar as instruções `CREATE/ALTER/DROP LOGIN`. No entanto, não é aconselhável utilizar inícios de sessão. Utilize os utilizadores de base de dados contida.
- Para ligar a uma base de dados do utilizador, tem de fornecer o nome da base de dados na cadeia de ligação.
- Apenas o início de sessão principal ao nível do servidor e os membros da função de base de dados **loginmanager** na base de dados **mestra** têm permissão para executar as instruções `CREATE LOGIN`, `ALTER LOGIN`, e `DROP LOGIN`.
- Ao executar as instruções `CREATE/ALTER/DROP LOGIN` e `CREATE/ALTER/DROP DATABASE` numa aplicação ADO.NET, não deve utilizar comandos parametrizados. Para obter mais informações, consulte [Comandos e Parâmetros](/dotnet/framework/data/adonet/commands-and-parameters?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
- Ao executar as instruções `CREATE/ALTER/DROP DATABASE` e `CREATE/ALTER/DROP LOGIN`, cada uma das seguintes declarações tem de ser a única instrução num batch do Transact-SQL. Caso contrário, ocorrerá um erro. Por exemplo, o Transact-SQL seguinte verifica se a base de dados existe. Se existir, é chamada uma instrução `DROP DATABASE` para remover a base de dados. Uma vez que a instrução `DROP DATABASE` não é a única instrução no batch, executar a seguinte instrução do Transact-SQL ocorre um erro.

  ```sql
  IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
  DROP DATABASE [database_name];
  GO
  ```
  
  Em vez disso, utilize a seguinte declaração transact-SQL:
  
  ```sql
  DROP DATABASE IF EXISTS [database_name]
  ```

- Ao executar a `CREATE USER` com a opção `FOR/FROM LOGIN`, tem de ser a única instrução num batch do Transact-SQL.
- Ao executar a `ALTER USER` com a opção `WITH LOGIN`, tem de ser a única instrução num batch do Transact-SQL.
- Para `CREATE/ALTER/DROP`, um utilizador necessita da permissão `ALTER ANY USER` na base de dados.
- Quando o proprietário de uma função de base de dados tenta adicionar ou remover outro utilizador de base de dados de ou para essa função de base de dados, pode ocorrer o seguinte erro: **o utilizador ou a função "Nome" não existe nesta base de dados.** Este erro ocorre porque o utilizador não está visível para o proprietário. Para resolver este problema, conceda uma permissão de `VIEW DEFINITION` ao proprietário da função. 

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja [Contained Database Users - Making Your Database Portable (Utilizadores de Base de Dados Contidos - Tornar a Sua Base de Dados Portátil)](https://msdn.microsoft.com/library/ff929188.aspx).
 
