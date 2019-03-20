---
title: Proteger uma base de dados no SQL Data Warehouse | Documentos da Microsoft
description: Sugestões para proteger uma base de dados no armazém de dados SQL do Azure para o desenvolvimento de soluções.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 179925fc7411a1ccf3de02d7b6298cc66f93bc66
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58175722"
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>Proteger uma base de dados no SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Descrição geral da segurança](sql-data-warehouse-overview-manage-security.md)
> * [Autenticação](sql-data-warehouse-authentication.md)
> * [Encriptação (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Encryption (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Este artigo explica as noções básicas de proteger a sua base de dados do Azure SQL Data Warehouse. Em particular, isso é de artigo começou com recursos para limitar o acesso, proteger os dados e monitorizar as atividades numa base de dados.

## <a name="connection-security"></a>Segurança da Ligação
A Segurança da Ligação diz respeito à forma como restringe e protege as ligações à sua base de dados através de regras de firewall e de encriptação da ligação.

As regras de firewall são utilizadas pelo servidor e pela base de dados para rejeitar as tentativas de ligação de endereços IP que não estejam explicitamente na lista de permissões. Para permitir ligações a partir de seu aplicativo ou o endereço IP público do computador do cliente, tem primeiro de criar uma regra de firewall ao nível do servidor com o portal do Azure, a REST API ou o PowerShell. Como melhor prática, deve restringir o máximo possível os intervalos de endereços IP permitidos na firewall do servidor.  Para aceder ao Azure SQL Data Warehouse do computador local, certifique-se de que a firewall na sua rede e o computador local permite comunicação de saída na porta TCP 1433.  

SQL Data Warehouse utiliza regras de firewall ao nível do servidor. Não suporta regras de firewall ao nível da base de dados. Para obter mais informações, consulte [firewall de base de dados do Azure SQL][Azure SQL Database firewall], [sp_set_firewall_rule][sp_set_firewall_rule].

Ligações para o SQL Data Warehouse são criptografadas por padrão.  Modificação de definições de ligação para desativar a encriptação são ignoradas.

## <a name="authentication"></a>Authentication
A autenticação diz respeito à forma como prova a sua identidade quando se liga à base de dados. Atualmente, o SQL Data Warehouse suporta autenticação do SQL Server com um nome de utilizador e palavra-passe e com o Azure Active Directory. 

Quando criou o servidor lógico para a sua base de dados, especificou um início de sessão "administrador do servidor" com um nome de utilizador e palavra-passe. Com estas credenciais, pode autenticar qualquer base de dados nesse servidor como o proprietário da base de dados, ou "dbo" através da autenticação do SQL Server.

No entanto, como melhor prática, os utilizadores da sua organização devem utilizar uma conta diferente para autenticar. Desta forma pode limitar as permissões concedidas à aplicação e reduzir os riscos de atividades maliciosas, caso o código da aplicação seja vulnerável a um ataque de injeção de SQL. 

Para criar um utilizador autenticado do SQL Server, ligue para o **mestre** no seu servidor com o início de sessão de administrador do servidor de base de dados e criar um novo início de sessão do servidor.  Além disso, é uma boa idéia para criar um utilizador na base de dados mestra para utilizadores do Azure SQL Data Warehouse. Criar um utilizador no ramo principal permite que um usuário fazer logon usando ferramentas como o SSMS sem especificar um nome de base de dados.  Ele também permite que utilize o object explorer para ver todas as bases de dados num SQL server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Em seguida, ligar a sua **base de dados do SQL Data Warehouse** com o seu início de sessão de administrador do servidor e crie um utilizador de base de dados com base no início de sessão do servidor que criou.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Para conceder uma permissão de utilizador para efetuar operações adicionais, tais como criar inícios de sessão ou criar novas bases de dados, atribua o utilizador para o `Loginmanager` e `dbmanager` funções na base de dados mestra. Para obter mais informações sobre estas funções adicionais e autenticar para a base de dados SQL, consulte [gestão de bases de dados e inícios de sessão na base de dados do Azure SQL][Managing databases and logins in Azure SQL Database].  Para obter mais informações, consulte [ligar ao SQL Data Warehouse por através do Azure autenticação do Active Directory][Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication].

## <a name="authorization"></a>Autorização
A autorização diz respeito ao que pode fazer dentro de uma base de dados do Azure SQL Data Warehouse. Privilégios de autorização são determinados pelas permissões e associações das funções. Como melhor prática, deverá conceder aos utilizadores o mínimo de privilégios necessários. Para gerir funções, pode utilizar os seguintes procedimentos armazenados:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

A conta de administrador do servidor que está a ligar é membro de db_owner, que tem autoridade para fazer todas as operações na base de dados. Guarde esta conta para a implementação de atualizações de esquema e outras operações de gestão. Utilize a conta "ApplicationUser" com permissões mais limitadas para se ligar da sua aplicação à base de dados com o mínimo de privilégios necessários para a sua aplicação.

Existem formas de limitar ainda mais que um utilizador pode fazer com o Azure SQL Data Warehouse:

* Granular [permissões] [ Permissions] permitem-lhe controlar as operações que pode em colunas individuais, tabelas, vistas, esquemas, procedimentos e outros objetos na base de dados. Utilize permissões granulares para ter mais controle e conceder as permissões mínimas necessárias. 
* [Funções de base de dados] [ Database roles] diferente de db_datareader e db_datawriter podem ser utilizados para criar contas de utilizador de aplicação mais poderosas ou contas de gestão menos poderosas. As funções de base de dados fixa incorporadas fornecem uma forma fácil para conceder permissões, mas podem resultar em conceder mais permissões do que são necessários.
* [Procedimentos armazenados] [ Stored procedures] pode ser utilizado para limitar as ações que podem ser efetuadas na base de dados.

O exemplo a seguir concede acesso de leitura a um esquema definido pelo utilizador.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Gerir bases de dados e servidores lógicos a partir do portal do Azure ou utilizando a API do Azure Resource Manager é controlada pelas atribuições de função da sua conta de utilizador do portal. Para obter mais informações, consulte [controlo de acesso baseado em funções no portal do Azure][Role-based access control in Azure portal].

## <a name="encryption"></a>Encriptação
O Azure SQL Data Warehouse dados encriptação transparente (TDE) ajuda a proteger contra ameaças de atividades maliciosas, ao encriptar e desencriptar os dados inativos.  Quando criptografa a sua base de dados, cópias de segurança associadas e ficheiros de registo de transação são criptografados sem exigir alterações às suas aplicações. TDE criptografa o armazenamento de uma base de dados completa com uma chave simétrica denominada a chave de encriptação da base de dados. 

Na base de dados SQL, a chave de encriptação da base de dados está protegida por um certificado de servidor interno. O certificado de servidor interno é exclusivo para cada servidor de base de dados SQL. Microsoft gira automaticamente estes certificados, pelo menos a cada 90 dias. O algoritmo de encriptação utilizado pelo SQL Data Warehouse é AES-256. Para obter uma descrição geral de TDE, consulte [encriptação de dados transparente][Transparent Data Encryption].

Pode criptografar a sua base de dados com o [portal do Azure] [ Encryption with Portal] ou [T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>Passos Seguintes
Para obter detalhes e exemplos sobre a ligação ao seu armazém de dados SQL com diferentes protocolos, consulte [ligar ao SQL Data Warehouse][Connect to SQL Data Warehouse].

<!--Image references-->

<!--Article references-->
[Connect to SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Encryption with Portal]: ./sql-data-warehouse-encryption-tde.md
[Encryption with TSQL]: ./sql-data-warehouse-encryption-tde-tsql.md
[Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Azure SQL Database firewall]: https://msdn.microsoft.com/library/ee621782.aspx
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[Database roles]: https://msdn.microsoft.com/library/ms189121.aspx
[Managing databases and logins in Azure SQL Database]: https://msdn.microsoft.com/library/ee336235.aspx
[Permissions]: https://msdn.microsoft.com/library/ms191291.aspx
[Stored procedures]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: https://msdn.microsoft.com/library/bb934049.aspx
[Azure portal]: https://portal.azure.com/

<!--Other Web references-->
[Role-based access control in Azure portal]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure
