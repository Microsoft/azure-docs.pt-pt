---
title: Inícios de sessão e utilizadores do SQL do Azure | Microsoft Docs
description: Saiba mais sobre a gestão de segurança de base de dados SQL e SQL Data Warehouse, mais concretamente como gerir a segurança de acesso e o início de sessão da base de dados através da conta do principal ao nível do servidor.
keywords: segurança de base de dados sql,gestão de segurança da base de dados,segurança de início de sessão,segurança de base de dados,acesso de base de dados
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 03/26/2019
ms.openlocfilehash: b1e952d9af474e2318ef91a6bdcc2605a3c30018
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497929"
---
# <a name="controlling-and-granting-database-access-to-sql-database-and-sql-data-warehouse"></a>Controlar e conceder acesso de base de dados para a base de dados SQL e SQL Data Warehouse

Após a configuração de regras de firewall, pode ligar ao Azure [base de dados SQL](sql-database-technical-overview.md) e [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) como uma das contas administrador, como o proprietário da base de dados ou como um utilizador de base de dados na base de dados.  

> [!NOTE]  
> Este tópico aplica-se ao servidor SQL do Azure e a base de dados SQL e SQL Data Warehouse bases de dados criadas no servidor SQL do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse. 
> [!TIP]
> Para obter um tutorial, veja [proteger a sua base de dados do SQL Azure](sql-database-security-tutorial.md). Este tutorial não se aplica aos **instância gerida da base de dados SQL do Azure**.

## <a name="unrestricted-administrative-accounts"></a>Contas administrativas sem restrições

Existem duas contas administrativas (**Administrador de servidor** e **Administrador do Active Directory**) que atuam como administradores. Para identificar estas contas de administrador para o servidor SQL, abra o portal do Azure e navegue para o separador de propriedades do seu SQL server ou a base de dados SQL.

![Administradores do SQL Server](media/sql-database-manage-logins/sql-admins.png)

- **Administrador do servidor**

  Quando cria um servidor SQL do Azure, tem de designar um **Início de sessão de administrador do servidor**. O servidor SQL cria essa conta como um início de sessão na base de dados mestra. Liga-se através da autenticação do SQL Server (nome de utilizador e palavra-passe). Só pode existir uma destas contas.

  > [!NOTE]
  > Para repor a palavra-passe para o administrador do servidor, vá para o [portal do Azure](https://portal.azure.com), clique em **SQL Servers**, selecione o servidor da lista e, em seguida, clique em **Repor palavra-passe**.

- **Administrador do Active Directory do Azure**

  Também é possível configurar uma conta do Azure Active Directory, seja esta individual ou de grupo de segurança, como administrador. É opcional configurar um administrador do Azure AD, mas o administrador do Azure AD **tem** ser configurado se quiser utilizar contas do Azure AD para ligar à base de dados SQL. Para obter mais informações sobre a configuração do acesso do Azure Active Directory, veja [Connecting to SQL Database or SQL Data Warehouse By Using Azure Active Directory Authentication (Ligar à Base de Dados SQL ou ao SQL Data Warehouse Com a Autenticação do Azure Active Directory)](sql-database-aad-authentication.md) e [Connecting to SQL Database or SQL Data Warehouse By Using Azure Active Directory Authentication (Suporte do SSMS para Azure AD MFA com Base de Dados SQL e SQL Data Warehouse)](sql-database-ssms-mfa-authentication.md).

As contas de **Administrador de servidor** e de **Administrador do Azure AD** têm as seguintes características:

- São as únicas contas que podem ligar automaticamente a qualquer base de dados SQL no servidor. (Para ligar a uma base de dados de utilizador, as outras contas têm de ser o proprietário da base de dados ou ter uma conta de utilizador na base de dados de utilizador.)
- Estas contas introduzem bases de dados de utilizador como o utilizador `dbo` e têm todas as permissões nas bases de dados de utilizador. (O proprietário de uma base de dados de utilizador também introduz a base de dados como o utilizador `dbo`.) 
- Não introduza o `master` da base de dados como o `dbo` utilizador e têm permissões limitadas na mestra. 
- São **não** membros do standard SQL Server `sysadmin` função de servidor fixa, o que não está disponível na base de dados SQL.  
- Pode criar, alterar e remover bases de dados, inícios de sessão, utilizadores nas regras de firewall IP principais e ao nível do servidor.
- Pode adicionar e remover membros para o `dbmanager` e `loginmanager` funções.
- Pode ver o `sys.sql_logins` tabela de sistema.

### <a name="configuring-the-firewall"></a>Configurar a firewall

Quando a firewall ao nível do servidor está configurada para um endereço IP individual ou intervalo de IP, o **administrador de servidor SQL** e o **administrador do Azure Active Directory** podem ligar-se à base de dados mestra e a todas as bases de dados de utilizador. A firewall ao nível do servidor inicial pode ser configurada através do [portal do Azure](sql-database-single-database-get-started.md), com o [PowerShell](sql-database-powershell-samples.md) ou com a [API REST](https://msdn.microsoft.com/library/azure/dn505712.aspx). Depois de estabelecida uma ligação, as regras de firewall IP ao nível do servidor adicionais também podem ser configuradas utilizando [Transact-SQL](sql-database-configure-firewall-settings.md).

### <a name="administrator-access-path"></a>Caminho de acesso do administrador

Quando a firewall ao nível do servidor está configurada corretamente, o **administrador de servidor SQL** e o **administrador do Azure Active Directory** podem ligar-se com ferramentas de cliente como o SQL Server Management Studio ou os SQL Server Data Tools. Só as ferramentas mais recentes proporcionam todas as funcionalidades e capacidades. O diagrama seguinte mostra uma configuração típica para as duas contas de administrador.

![Caminho de acesso do administrador](./media/sql-database-manage-logins/1sql-db-administrator-access.png)

Quando utilizar uma porta aberta na firewall ao nível do servidor, os administradores podem ligar-se a qualquer Base de Dados SQL.

### <a name="connecting-to-a-database-by-using-sql-server-management-studio"></a>Ligar a uma base de dados com o SQL Server Management Studio

Para um passo a passo de criação de um servidor, uma base de dados, as regras de firewall do IP ao nível do servidor e, usando o SQL Server Management Studio para consultar uma base de dados, consulte [começar com servidores de base de dados do Azure SQL, bases de dados e regras de firewall com o portal do Azure e o SQL Server Management Studio](sql-database-single-database-get-started.md).

> [!IMPORTANT]
> É recomendado utilizar sempre a versão mais recente do Management Studio, para permanecer sincronizado com as atualizações do Microsoft Azure e da Base de Dados SQL. [Atualize o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="additional-server-level-administrative-roles"></a>Funções administrativas adicionais ao nível do servidor

>[!IMPORTANT]
>Esta secção não se aplica aos **instância gerida da base de dados SQL do Azure** como essas funções são específicas de **base de dados do Azure SQL**.

Para além das funções administrativas ao nível do servidor abordadas anteriormente, a Base de Dados SQL fornece duas funções administrativas restritas na base de dados mestra às quais podem ser adicionadas contas de utilizador que concedam permissões para criar bases de dados ou gerir inícios de sessão.

### <a name="database-creators"></a>Criadores de base de dados

Uma destas funções administrativas é a função **dbmanager**. Os membros desta função podem criar novas bases de dados. Para utilizar esta função, crie um utilizador na base de dados `master` e, em seguida, adicione o utilizador à função de base de dados **dbmanager**. Para criar uma base de dados, o utilizador tem de ser um utilizador com base num início de sessão do SQL Server no `master` da base de dados ou utilizador de base de dados contida, com base num utilizador do Azure Active Directory.

1. Utilizar uma conta de administrador, ligue para o `master` base de dados.
2. Criar um início de sessão de autenticação do SQL Server, utilizando o [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) instrução. Instrução de exemplo:

   ```sql
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```

   > [!NOTE]
   > Utilize uma palavra-passe forte ao criar um utilizador de base de dados contido ou de início de sessão. Para obter mais informações, veja [Strong Passwords (Palavras-passe Fortes)](https://msdn.microsoft.com/library/ms161962.aspx).

   Para melhorar o desempenho, os início de sessão (principais ao nível do servidor) são temporariamente colocados em cache ao nível da base de dados. Para atualizar a cache de autenticação, veja [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

3. Na `master` bases de dados, crie um utilizador com o [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) instrução. O utilizador pode ser um utilizador de base de dados contido do Azure Active Directory (se tiver configurado o ambiente para autenticação do Azure AD), um utilizador de base de dados contido de autenticação do SQL Server ou um utilizador de autenticação do SQL Server com base num início de sessão de autenticação do SQL Server (criado no passo anterior). Instruções de exemplo:

   ```sql
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER; -- To create a user with Azure Active Directory
   CREATE USER Ann WITH PASSWORD = '<strong_password>'; -- To create a SQL Database contained database user
   CREATE USER Mary FROM LOGIN Mary;  -- To create a SQL Server user based on a SQL Server authentication login
   ```

4. Adicionar novo utilizador, para o **dbmanager** função de base de dados no `master` com o [ALTER ROLE](https://msdn.microsoft.com/library/ms189775.aspx) instrução. Instruções de exemplo:

   ```sql
   ALTER ROLE dbmanager ADD MEMBER Mary; 
   ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
   ```

   > [!NOTE]
   > O dbmanager é uma função de base de dados na base de dados mestra, pelo que só pode adicionar um utilizador de base de dados à função dbmanager. Não pode adicionar um início de sessão ao nível do servidor à função de nível de base de dados.

5. Se for necessário, configure uma regra de firewall para permitir que o novo utilizador se ligue. (O novo utilizador poderá ser abrangido por uma regra de firewall existente.)

Agora, o utilizador pode ligar-se para o `master` de base de dados e pode criar novas bases de dados. A conta que cria a base de dados torna-se na proprietária da base de dados.

### <a name="login-managers"></a>Gestores de início de sessão

A outra função administrativa é a função de gestor de início de sessão. Os membros desta função podem criar novos inícios de sessão na base de dados mestra. Se pretender, pode seguir os mesmos passos (criar um início de sessão e um utilizador e adicionar um utilizador à função **loginmanager**) para permitir que um utilizador crie novos inícios de sessão na base de dados mestra. Normalmente, os inícios de sessão não são necessários, uma vez que a Microsoft recomenda a utilização de utilizadores de base de dados contidos, o que autentica ao nível da base de dados em vez de utilizar os utilizadores com base em inícios de sessão. Para obter mais informações, veja [Contained Database Users - Making Your Database Portable (Utilizadores de Base de Dados Contidos - Tornar a Sua Base de Dados Portátil)](https://msdn.microsoft.com/library/ff929188.aspx).

## <a name="non-administrator-users"></a>Utilizadores não administradores

Geralmente, as contas de não administrador não precisam de acesso à base de dados mestra. Utilize a instrução [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx) para criar os utilizadores de base de dados contidos na base de dados ao nível da base de dados. O utilizador pode ser um utilizador de base de dados contido do Azure Active Directory (se tiver configurado o ambiente para autenticação do Azure AD), um utilizador de base de dados contido de autenticação do SQL Server ou um utilizador de autenticação do SQL Server com base num início de sessão de autenticação do SQL Server (criado no passo anterior). Para obter mais informações, veja [Contained Database Users - Making Your Database Portable (Utilizadores de Base de Dados Contidos - Tornar a Sua Base de Dados Portátil)](https://msdn.microsoft.com/library/ff929188.aspx). 

Para criar utilizadores, ligue à base de dados e execute instruções semelhantes aos exemplos seguintes:

```sql
CREATE USER Mary FROM LOGIN Mary; 
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Inicialmente, apenas um dos administradores ou o proprietário da base de dados pode criar utilizadores. Para permitir que outros utilizadores criem utilizadores novos, conceda-lhes a permissão `ALTER ANY USER`, mediante uma instrução como:

```sql
GRANT ALTER ANY USER TO Mary;
```

Para conceder aos utilizadores adicionais controlo total da base de dados, torná-los um membro do **db_owner** função de base de dados fixa.

Em utilização de base de dados do Azure SQL o `ALTER ROLE` instrução.

```sql
ALTER ROLE db_owner ADD MEMBER Mary;
```

Na utilização do Azure SQL Data Warehouse [EXEC sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql).
```sql
EXEC sp_addrolemember 'db_owner', 'Mary';
```


> [!NOTE]
> É uma das razões comuns para criar um utilizador de base de dados com base num início de sessão do servidor de base de dados SQL para os utilizadores que necessitam de aceder a várias bases de dados. Uma vez que continha os utilizadores de base de dados são entidades individuais, cada base de dados mantém sua própria de utilizador e a sua própria palavra-passe. Isso pode causar overhead à medida que o utilizador tem, em seguida, lembre-se de cada palavra-passe para cada base de dados, e ele pode se tornar untenable quando precisar alterar várias palavras-passe para muitas bases de dados. No entanto, ao utilizar inícios de sessão do SQL Server e de elevada disponibilidade (georreplicação ativa e grupos de ativação pós-falha), os inícios de sessão do SQL Server tem de ser definidos manualmente em cada servidor. Caso contrário, o utilizador de base de dados será já não está mapeado para o início de sessão do servidor depois de ocorre uma ativação pós-falha e não será capaz de aceder a ativação pós-falha de postagem de base de dados. Para obter mais informações sobre como configurar os inícios de sessão para georreplicação, veja [configurar e gerir a segurança de base de dados do Azure SQL para o restauro geográfico ou de ativação pós-falha](sql-database-geo-replication-security-config.md).

### <a name="configuring-the-database-level-firewall"></a>Configurar a firewall ao nível da base de dados

Como melhor prática, os utilizadores não administradores só devem ter acesso às bases de dados que utilizam através da firewall. Em vez de autorizar os endereços IP deles através da firewall ao nível do servidor e conceder acesso a todas as bases de dados, utilize a instrução [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) para configurar a firewall ao nível da base de dados. Não é possível configurar a firewall ao nível da base de dados no portal.

### <a name="non-administrator-access-path"></a>Caminho de acesso para não administradores

Quando a firewall ao nível da base de dados está devidamente configurada, os utilizadores da base de dados podem ligar através de ferramentas de cliente, como o SQL Server Management Studio ou o SQL Server Data Tools. Só as ferramentas mais recentes proporcionam todas as funcionalidades e capacidades. O diagrama seguinte mostra um caminho típico de acesso para não administradores.

![Caminho de acesso para não administradores](./media/sql-database-manage-logins/2sql-db-nonadmin-access.png)

## <a name="groups-and-roles"></a>Grupos e funções

A gestão de acessos eficaz utiliza permissões atribuídas a grupos e funções em vez de utilizadores individuais. 

- Quando utilizar a autenticação do Azure Active Directory, coloque os utilizadores do Azure Active Directory num grupo do Azure Active Directory. Crie um utilizador de base de dados contido para o grupo. Coloque um ou mais utilizadores de base de dados numa [função de base de dados](https://msdn.microsoft.com/library/ms189121) e, em seguida, atribua [permissões](https://msdn.microsoft.com/library/ms191291.aspx) à função de base de dados.

- Ao utilizar a autenticação do SQL Server, crie utilizadores de base de dados contida na base de dados. Coloque um ou mais utilizadores de base de dados numa [função de base de dados](https://msdn.microsoft.com/library/ms189121) e, em seguida, atribua [permissões](https://msdn.microsoft.com/library/ms191291.aspx) à função de base de dados.

As funções de base de dados podem ser as funções incorporadas, tais como **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** e **db_denydatareader**. **db_owner** é, geralmente, utilizada para conceder permissão total apenas a alguns utilizadores. As outras funções de base de dados fixas são úteis para colocar bases de dados simples em desenvolvimento rapidamente, mas não são recomendadas para a maioria das bases de dados de produção. Por exemplo, a função de base de dados fixa **db_datareader** concede acesso de leitura a todas as tabelas na base de dados, o que, regra geral, é mais do que o estritamente necessário. É muito melhor utilizar a instrução [CREATE ROLE](https://msdn.microsoft.com/library/ms187936.aspx) para criar as suas próprias funções de base de dados definidas pelo utilizador e conceder, cuidadosamente, a cada função o menor número de permissões necessárias para a atividade em causa. Quando um utilizador é membro de várias funções, agrega as permissões de todas essas funções.

## <a name="permissions"></a>Permissões

Existem mais de 100 permissões que podem ser individualmente concedidas ou negadas na Base de Dados SQL. Muitas destas permissões são aninhadas. Por exemplo, a permissão `UPDATE` num esquema inclui a permissão `UPDATE` em cada tabela dentro desse esquema. Tal como na maioria dos sistemas de permissões, a recusa de uma permissão sobrepõe-se a uma atribuição. Devido à natureza aninhada e ao número de permissões, desenhar um sistema de permissões que proteja as bases de dados adequadamente pode envolver um cuidadoso estudo. Comece com a lista de permissões em [Permissions (Database Engine) (Permissões [Motor de Base de Dados])](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) e reveja o [gráfico em tamanho de cartaz](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) das permissões.


### <a name="considerations-and-restrictions"></a>Considerações e restrições

Ao gerir inícios de sessão e utilizadores na Base de Dados SQL, considere o seguinte:

- Tem de estar ligado à base de dados **mestra** ao executar as instruções `CREATE/ALTER/DROP DATABASE`.   
- O utilizador da base de dados que corresponde ao início de sessão do **Administrador de servidor** não pode ser alterado ou removido. 
- O inglês dos E.U.A. é o idioma predefinido do início de sessão do **Administrador de servidor**.
- Só os administradores (início de sessão do **Administrador de servidor** ou administrador do Azure AD) e os membros da função de base de dados **dbmanager**ba base de dados **mestra** têm permissão para executar as declarações `CREATE DATABASE` e `DROP DATABASE`.
- Tem de estar ligado à base de dados mestra ao executar as instruções `CREATE/ALTER/DROP LOGIN`. No entanto, não é aconselhável utilizar inícios de sessão. Utilize os utilizadores de base de dados contida.
- Para ligar a uma base de dados do utilizador, tem de fornecer o nome da base de dados na cadeia de ligação.
- Apenas o início de sessão principal ao nível do servidor e os membros da função de base de dados **loginmanager** na base de dados **mestra** têm permissão para executar as instruções `CREATE LOGIN`, `ALTER LOGIN`, e `DROP LOGIN`.
- Ao executar as instruções `CREATE/ALTER/DROP LOGIN` e `CREATE/ALTER/DROP DATABASE` numa aplicação ADO.NET, não deve utilizar comandos parametrizados. Para obter mais informações, consulte [Comandos e Parâmetros](https://msdn.microsoft.com/library/ms254953.aspx).
- Ao executar as instruções `CREATE/ALTER/DROP DATABASE` e `CREATE/ALTER/DROP LOGIN`, cada uma das seguintes declarações tem de ser a única instrução num batch do Transact-SQL. Caso contrário, ocorrerá um erro. Por exemplo, o Transact-SQL seguinte verifica se a base de dados existe. Se existir, é chamada uma instrução `DROP DATABASE` para remover a base de dados. Uma vez que a instrução `DROP DATABASE` não é a única instrução no batch, executar a seguinte instrução do Transact-SQL ocorre um erro.

  ```sql
  IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
  DROP DATABASE [database_name];
  GO
  ```
  
  Em alternativa, utilize a seguinte instrução Transact-SQL:
  
  ```sql
  DROP DATABASE IF EXISTS [database_name]
  ```

- Ao executar a `CREATE USER` com a opção `FOR/FROM LOGIN`, tem de ser a única instrução num batch do Transact-SQL.
- Ao executar a `ALTER USER` com a opção `WITH LOGIN`, tem de ser a única instrução num batch do Transact-SQL.
- Para `CREATE/ALTER/DROP`, um utilizador necessita da permissão `ALTER ANY USER` na base de dados.
- Quando o proprietário de uma função de base de dados tenta adicionar ou remover outro utilizador de base de dados de ou para essa função de base de dados, pode ocorrer o erro seguinte: **O utilizador ou função "Name" não existe nesta base de dados.** Este erro ocorre porque o utilizador não está visível para o proprietário. Para resolver este problema, conceda uma permissão de `VIEW DEFINITION` ao proprietário da função. 


## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre regras de firewall, veja [Firewall da Base de Dados SQL do Azure](sql-database-firewall-configure.md).
- Para obter uma descrição geral de todas as funcionalidades de segurança da Base de Dados SQL, veja a [Descrição geral da segurança de SQL](sql-database-security-overview.md).
- Para obter um tutorial, veja [proteger a sua base de dados do SQL Azure](sql-database-security-tutorial.md).
- Para obter informações sobre vistas e procedimentos armazenados, veja [Creating views and stored procedures (Criar vistas e procedimentos armazenados)](https://msdn.microsoft.com/library/ms365311.aspx)
- Para obter informações sobre como conceder acesso a um objeto de base de dados, veja [Granting Access to a Database Object (Conceder Acesso a um Objeto de Base de Dados)](https://msdn.microsoft.com/library/ms365327.aspx)
