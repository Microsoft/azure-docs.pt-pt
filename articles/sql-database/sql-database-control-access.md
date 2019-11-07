---
title: Concedendo acesso ao banco de dados SQL do Azure e SQL Data Warehouse
description: Saiba como conceder acesso a Banco de Dados SQL do Microsoft Azure e SQL Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 05/08/2019
ms.openlocfilehash: c115cd7e4d531bfdc7ddbacd4f6eff2a892ea3c3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690748"
---
# <a name="azure-sql-database-and-sql-data-warehouse-access-control"></a>Banco de dados SQL do Azure e controle de acesso de SQL Data Warehouse

Para fornecer segurança, [banco de dados SQL](sql-database-technical-overview.md) do Azure e acesso de controle de [SQL data warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) com regras de firewall limitando a conectividade por endereço IP, mecanismos de autenticação que exigem que os usuários comprovem sua identidade e mecanismos de autorização limitar os usuários a ações e dados específicos. 

> [!IMPORTANT]
> Para obter uma descrição geral das funcionalidades de segurança da Base de Dados SQL, veja [Descrição geral da segurança de SQL](sql-database-security-overview.md). Para obter um tutorial, consulte [proteger seu banco de dados SQL do Azure](sql-database-security-tutorial.md). Para obter uma visão geral dos recursos de segurança do SQL Data Warehouse, consulte [visão geral da segurança do SQL data warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)

## <a name="firewall-and-firewall-rules"></a>Firewall e regras de firewall

A Base de Dados SQL do Microsoft Azure disponibiliza um serviço de bases de dados relacionais para o Azure e outras aplicações baseadas na Internet. Para ajudar a proteger os seus dados, as firewalls impedem todos os acessos ao seu servidor de base de dados enquanto não especificar que computadores têm acesso. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido. Para obter mais informações, veja [Descrição geral das regras de firewall da Base de Dados SQL do Azure](sql-database-firewall-configure.md).

O serviço da Base de Dados SQL do Azure só está disponível através da porta TCP 1433. Para aceder a uma Base de Dados SQL a partir do seu computador, certifique-se de que a firewall do computador de cliente permite comunicação TCP de saída na porta TCP 1433. Se não for necessário para outras aplicações, bloqueie as ligações de entrada na porta TCP 1433. 

Como parte do processo de ligação, as ligações a partir de máquinas virtuais do Azure são redirecionadas para um endereço IP diferente e a porta exclusiva para cada função de trabalho. O número de porta está no intervalo de 11000 a 11999. Para obter mais informações sobre portas TCP, consulte [portas além de 1433 para ADO.NET 4,5 e SQL Database2](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="authentication"></a>Autenticação

A Base de Dados SQL suporta dois tipos de autenticação:

- **Autenticação do SQL**:

  Esse método de autenticação usa um nome de usuário e senha. Quando você criou o servidor do banco de dados SQL para seu banco de dados, você especificou um logon de "administrador do servidor" com um nome de usuário e senha. Com estas credenciais, pode autenticar-se em qualquer base de dados nesse servidor como o proprietário da base de dados ou "dbo". 
- **Autenticação Azure Active Directory**:

  Esse método de autenticação usa identidades gerenciadas pelo Azure Active Directory e tem suporte para domínios gerenciados e integrados. Se pretender utilizar a autenticação do Azure Active Directory, tem de criar outro administrador de servidor, denominado "Administrador do Azure AD", que tem permissão para administrar utilizadores e grupos do Azure AD. Este administrador também pode fazer todas as operações que um administrador de servidor normal faz. Veja [Connecting to SQL Database By Using Azure Active Directory Authentication (Utilizar a Autenticação do Azure Active Directory para Ligar à Base de Dados SQL)](sql-database-aad-authentication.md) para obter instruções sobre como criar um administrador do Azure AD e ativar a Autenticação do Azure Active Directory.

O Motor de Base de Dados fecha ligações que permanecem inativas durante mais de 30 minutos. A ligação tem de iniciar sessão novamente antes de poder ser utilizada. As ligações continuamente ativas à Base de Dados SQL precisam de reautorização (realizada pelo motor de base de dados) pelo menos a cada 10 horas. O motor de base de dados tenta a reautorização com a palavra-passe originalmente submetida e sem intervenção do utilizador. Por motivos de desempenho, quando uma senha é redefinida no banco de dados SQL, a conexão não é reautenticada, mesmo que a conexão seja redefinida devido ao pool de conexões. Isto é diferente do comportamento do SQL Server no local. Se a palavra-passe tiver sido alterada porque a ligação foi inicialmente autorizada, a ligação tem de ser terminada e uma nova ligação efetuada com a nova palavra-passe. Um utilizador com permissão `KILL DATABASE CONNECTION` pode terminar uma ligação explicitamente na Base de Dados SQL com o comando [ELIMINAR](https://docs.microsoft.com/sql/t-sql/language-elements/kill-transact-sql).

As contas de utilizador podem ser criadas na base de dados mestra e podem ser concedidas permissões às mesmas em todas as bases de dados no servidor, ou podem ser criadas na própria base de dados (chamados utilizadores contidos). Para obter informações sobre como criar e gerir inícios de sessão, veja [Gerir inícios de sessão](sql-database-manage-logins.md). Use bancos de dados independentes para aprimorar a portabilidade e a escalabilidade. Para obter mais informações sobre utilizadores contidos, veja [Contained Database Users - Making Your Database Portable (Utilizadores de Bases de Dados Contidas - Tornar a Base de Dados Portátil)](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable), [CREATE USER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql), e [Contained Databases (Bases de Dados Contidas)](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases).

Como melhor prática, a sua aplicação deve utilizar uma conta dediacada para se autenticar. Desta forma, pode limitar as permissões concedidas à aplicação e reduzir os riscos de atividades maliciosas, caso o código da aplicação seja vulnerável a um ataque de injeção SQL. A abordagem recomendada consiste em criar um [utilizador de base de dados contido](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable), que permite que a sua aplicação se autentique diretamente na base de dados. 

## <a name="authorization"></a>Autorização

A autorização diz respeito ao que um utilizador pode fazer dentro de uma Base de Dados SQL do Azure e é controlada pelas [permissões de nível de objeto](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) e [associações de funções](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) de base de dados da sua conta de utilizador. Como melhor prática, deverá conceder aos utilizadores o mínimo de privilégios necessários. A conta de administrador do servidor que está a ligar é membro de db_owner, que tem autoridade para fazer todas as operações na base de dados. Guarde esta conta para a implementação de atualizações de esquema e outras operações de gestão. Utilize a conta "ApplicationUser" com permissões mais limitadas para se ligar da sua aplicação à base de dados com o mínimo de privilégios necessários para a sua aplicação. Para obter mais informações, veja [Gerir inícios de sessão](sql-database-manage-logins.md).

Normalmente, apenas os administradores necessitam de aceder à base de dados `master`. O acesso de rotina a cada base de dados do utilizador deve ser feito através dos utilizadores de base de dados contida não-administradores criados em cada base de dados. Quando utiliza utilizadores de base de dados contida, não precisa de criar inícios de sessão na base de dados `master`. Para obter mais informações, veja [Contained Database Users - Making Your Database Portable (Utilizadores de Base de Dados Contidos - Tornar a Sua Base de Dados Portátil)](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable).

Deverá familiarizar-se com as seguintes funcionalidades que podem ser utilizadas para limitar ou efetuar a elevação de permissões:

- A [representação](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) e a [assinatura de módulo](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) podem ser utilizadas para elevar temporariamente permissões de forma segura.
- A [Segurança ao Nível da Linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) pode ser utilizada para limitar as linhas a que um utilizador pode aceder.
- A [Máscara de Dados](sql-database-dynamic-data-masking-get-started.md) pode ser utilizada para limitar a exposição de dados confidenciais.
- Os [Procedimentos armazenados](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) podem ser utilizados para limitar as ações que podem ser realizadas na base de dados.

## <a name="next-steps"></a>Passos seguintes

- Para obter uma descrição geral das funcionalidades de segurança da Base de Dados SQL, veja [Descrição geral da segurança de SQL](sql-database-security-overview.md).
- Para saber mais sobre as regras de firewall, consulte [regras de firewall](sql-database-firewall-configure.md).
- Para saber mais sobre utilizadores e inícios de sessão, veja [Gerir inícios de sessão](sql-database-manage-logins.md). 
- Para obter uma discussão sobre o monitoramento proativo, consulte [auditoria de banco de dados](sql-database-auditing.md) e detecção de ameaças do banco de [dados SQL](sql-database-threat-detection.md).
- Para obter um tutorial, consulte [proteger seu banco de dados SQL do Azure](sql-database-security-tutorial.md).
