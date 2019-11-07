---
title: Proteger um banco de dados
description: Dicas para proteger um banco de dados no Azure SQL Data Warehouse para desenvolver soluções.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c51a945bae7cc0b03c219bc041d64f4703baef19
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692573"
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>Proteger um banco de dados no SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Visão geral de segurança](sql-data-warehouse-overview-manage-security.md)
> * [Autenticação](sql-data-warehouse-authentication.md)
> * [Criptografia (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Criptografia (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Este artigo apresenta as noções básicas de como proteger seu banco de dados SQL Data Warehouse do Azure. Em particular, este artigo apresenta recursos para limitar o acesso, proteger dados e monitorar atividades em um banco de dado.

## <a name="connection-security"></a>Segurança da Ligação
A Segurança da Ligação diz respeito à forma como restringe e protege as ligações à sua base de dados através de regras de firewall e de encriptação da ligação.

As regras de firewall são utilizadas pelo servidor e pela base de dados para rejeitar as tentativas de ligação de endereços IP que não estejam explicitamente na lista de permissões. Para permitir conexões do endereço IP público do seu aplicativo ou computador cliente, você deve primeiro criar uma regra de firewall no nível de servidor usando o portal do Azure, a API REST ou o PowerShell. Como melhor prática, deve restringir o máximo possível os intervalos de endereços IP permitidos na firewall do servidor.  Para acessar o Azure SQL Data Warehouse do seu computador local, verifique se o firewall na rede e no computador local permite a comunicação de saída na porta TCP 1433.  

SQL Data Warehouse usa regras de firewall no nível de servidor. Ele não dá suporte a regras de firewall no nível de banco de dados. Para obter mais informações, consulte [Firewall do banco de dados SQL do Azure][Azure SQL Database firewall], [sp_set_firewall_rule][sp_set_firewall_rule].

As conexões com seus SQL Data Warehouse são criptografadas por padrão.  A modificação das configurações de conexão para desabilitar a criptografia é ignorada.

## <a name="authentication"></a>Autenticação
A autenticação diz respeito à forma como prova a sua identidade quando se liga à base de dados. O SQL Data Warehouse atualmente dá suporte à autenticação SQL Server com um nome de usuário e senha e com Azure Active Directory. 

Quando criou o servidor lógico para a sua base de dados, especificou um início de sessão "administrador do servidor" com um nome de utilizador e palavra-passe. Usando essas credenciais, você pode se autenticar em qualquer banco de dados desse servidor como o proprietário do banco de dados ou "dbo" por meio da autenticação SQL Server.

No entanto, como prática recomendada, os usuários da sua organização devem usar uma conta diferente para se autenticar. Dessa forma, você pode limitar as permissões concedidas ao aplicativo e reduzir os riscos de atividades mal-intencionadas caso o código do aplicativo seja vulnerável a um ataque de injeção de SQL. 

Para criar um SQL Server usuário autenticado, conecte-se ao banco de dados **mestre** no servidor com o logon de administrador do servidor e crie um novo logon de servidor.  Além disso, é uma boa ideia criar um usuário no banco de dados mestre do Azure SQL Data Warehouse usuários. A criação de um usuário no mestre permite que um usuário faça logon usando ferramentas como o SSMS sem especificar um nome de banco de dados.  Ele também permite que eles usem o pesquisador de objetos para exibir todos os bancos de dados em um SQL Server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Em seguida, conecte-se ao seu **banco de dados SQL data warehouse** com o logon de administrador do servidor e crie um usuário de banco de dados com base no logon do servidor que você criou.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Para conceder a um usuário permissão para executar operações adicionais, como a criação de logons ou a criação de novos bancos de dados, atribua o usuário às funções `Loginmanager` e `dbmanager` no banco de dado mestre. Para obter mais informações sobre essas funções adicionais e a autenticação em um banco de dados SQL, consulte [Managing bancos de dados e logons in Azure SQL Database][Managing databases and logins in Azure SQL Database].  Para obter mais informações, consulte [conectando-se ao SQL data warehouse usando a autenticação Azure Active Directory][Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication].

## <a name="authorization"></a>Autorização
A autorização refere-se ao que você pode fazer em um banco de dados SQL Data Warehouse do Azure. Os privilégios de autorização são determinados por associações de função e permissões. Como melhor prática, deverá conceder aos utilizadores o mínimo de privilégios necessários. Para gerenciar funções, você pode usar os seguintes procedimentos armazenados:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

A conta de administrador do servidor que está a ligar é membro de db_owner, que tem autoridade para fazer todas as operações na base de dados. Guarde esta conta para a implementação de atualizações de esquema e outras operações de gestão. Utilize a conta "ApplicationUser" com permissões mais limitadas para se ligar da sua aplicação à base de dados com o mínimo de privilégios necessários para a sua aplicação.

Há maneiras de limitar ainda mais o que um usuário pode fazer com o Azure SQL Data Warehouse:

* [As permissões][Permissions] granulares permitem controlar quais operações você pode fazer em colunas individuais, tabelas, exibições, esquemas, procedimentos e outros objetos no banco de dados. Use permissões granulares para ter o máximo de controle e conceder as permissões mínimas necessárias. 
* As [funções de banco de dados][Database roles] que não sejam db_datareader e db_datawriter podem ser usadas para criar contas de usuário de aplicativo mais poderosas ou contas de gerenciamento menos poderosas. As funções internas de banco de dados fixas fornecem uma maneira fácil de conceder permissões, mas podem resultar na concessão de mais permissões do que são necessárias.
* Os [Procedimentos armazenados][Stored procedures] podem ser utilizados para limitar as ações que podem ser realizadas na base de dados.

O exemplo a seguir concede acesso de leitura a um esquema definido pelo usuário.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

O gerenciamento de bancos de dados e servidores lógicos do portal do Azure ou do uso da API Azure Resource Manager é controlado pelas atribuições de função da sua conta de usuário do Portal. Para obter mais informações, consulte [controle de acesso baseado em função no portal do Azure][Role-based access control in Azure portal].

## <a name="encryption"></a>Encriptação
O Azure SQL Data Warehouse Transparent Data Encryption (TDE) ajuda a proteger contra a ameaça de atividades mal-intencionadas Criptografando e descriptografando seus dados em repouso.  Quando você criptografa seu banco de dados, os backups associados e os arquivos de log de transações são criptografados sem a necessidade de qualquer alteração em seus aplicativos. O TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada chave de criptografia do banco de dados. 

No banco de dados SQL, a chave de criptografia de banco de dados é protegida por um certificado de servidor interno. O certificado de servidor interno é exclusivo para cada servidor de banco de dados SQL. A Microsoft gira automaticamente esses certificados pelo menos a cada 90 dias. O algoritmo de criptografia usado pelo SQL Data Warehouse é o AES-256. Para obter uma descrição geral do TDE, consulte [Transparent Data Encryption][Transparent Data Encryption].

Você pode criptografar seu banco de dados usando o [portal do Azure][Encryption with Portal] ou o [T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>Passos seguintes
Para obter detalhes e exemplos sobre como se conectar ao seu SQL Data Warehouse com protocolos diferentes, consulte [conectar-se ao SQL data warehouse][Connect to SQL Data Warehouse].

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
