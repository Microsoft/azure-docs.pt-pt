---
title: Conceder acesso à base de dados do Azure SQL e SQL Data Warehouse | Documentos da Microsoft
description: Saiba como conceder acesso à base de dados do Microsoft Azure SQL e SQL Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 05/08/2019
ms.openlocfilehash: 783a8f0bc25717f1c2bf78a9c0d40b209a07939b
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65473350"
---
# <a name="azure-sql-database-and-sql-data-warehouse-access-control"></a>Controlo de acesso de base de dados SQL e SQL Data Warehouse do Azure

Para fornecer segurança, do Azure [base de dados SQL](sql-database-technical-overview.md) e [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) controlar o acesso com regras de firewall que limitam a conectividade por endereço IP, mecanismos de autenticação que exigem que os utilizadores forneçam a respetiva identidade e mecanismos de autorização que limitam os utilizadores a ações específicas e os dados. 

> [!IMPORTANT]
> Para obter uma descrição geral das funcionalidades de segurança da Base de Dados SQL, veja [Descrição geral da segurança de SQL](sql-database-security-overview.md). Para obter um tutorial, veja [proteger a sua base de dados do SQL Azure](sql-database-security-tutorial.md). Para uma descrição geral dos recursos de segurança do SQL Data Warehouse, consulte [descrição geral da segurança do SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)

## <a name="firewall-and-firewall-rules"></a>Firewall e regras de firewall

A Base de Dados SQL do Microsoft Azure disponibiliza um serviço de bases de dados relacionais para o Azure e outras aplicações baseadas na Internet. Para ajudar a proteger os seus dados, as firewalls impedem todos os acessos ao seu servidor de base de dados enquanto não especificar que computadores têm acesso. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido. Para obter mais informações, veja [Descrição geral das regras de firewall da Base de Dados SQL do Azure](sql-database-firewall-configure.md).

O serviço da Base de Dados SQL do Azure só está disponível através da porta TCP 1433. Para aceder a uma Base de Dados SQL a partir do seu computador, certifique-se de que a firewall do computador de cliente permite comunicação TCP de saída na porta TCP 1433. Se não for necessário para outras aplicações, bloqueie as ligações de entrada na porta TCP 1433. 

Como parte do processo de ligação, as ligações a partir de máquinas virtuais do Azure são redirecionadas para um endereço IP diferente e a porta exclusiva para cada função de trabalho. O número de porta está no intervalo de 11000 a 11999. Para obter mais informações sobre as portas TCP, consulte [portas para além do 1433 para ADO.NET 4.5 e base de dados2 SQL](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="authentication"></a>Autenticação

A Base de Dados SQL suporta dois tipos de autenticação:

- **Autenticação do SQL**:

  Este método de autenticação utiliza um nome de utilizador e palavra-passe. Quando criou o servidor de base de dados SQL da base de dados, especificou um início de sessão "administrador do servidor" com um nome de utilizador e palavra-passe. Com estas credenciais, pode autenticar-se em qualquer base de dados nesse servidor como o proprietário da base de dados ou "dbo". 
- **O Azure Active Directory Authentication**:

  Este método de autenticação utiliza identidades geridas pelo Azure Active Directory e é suportado para domínios geridos e integrados. Se pretender utilizar a autenticação do Azure Active Directory, tem de criar outro administrador de servidor, denominado "Administrador do Azure AD", que tem permissão para administrar utilizadores e grupos do Azure AD. Este administrador também pode fazer todas as operações que um administrador de servidor normal faz. Veja [Connecting to SQL Database By Using Azure Active Directory Authentication (Utilizar a Autenticação do Azure Active Directory para Ligar à Base de Dados SQL)](sql-database-aad-authentication.md) para obter instruções sobre como criar um administrador do Azure AD e ativar a Autenticação do Azure Active Directory.

O Motor de Base de Dados fecha ligações que permanecem inativas durante mais de 30 minutos. A ligação tem de iniciar sessão novamente antes de poder ser utilizada. As ligações continuamente ativas à Base de Dados SQL precisam de reautorização (realizada pelo motor de base de dados) pelo menos a cada 10 horas. O motor de base de dados tenta a reautorização com a palavra-passe originalmente submetida e sem intervenção do utilizador. Por motivos de desempenho, quando uma palavra-passe é reposta na base de dados SQL, a ligação não é reautenticar, mesmo que a ligação seja reposta devido ao agrupamento de ligações. Isto é diferente do comportamento do SQL Server no local. Se a palavra-passe tiver sido alterada porque a ligação foi inicialmente autorizada, a ligação tem de ser terminada e uma nova ligação efetuada com a nova palavra-passe. Um utilizador com permissão `KILL DATABASE CONNECTION` pode terminar uma ligação explicitamente na Base de Dados SQL com o comando [ELIMINAR](https://docs.microsoft.com/sql/t-sql/language-elements/kill-transact-sql).

As contas de utilizador podem ser criadas na base de dados mestra e podem ser concedidas permissões às mesmas em todas as bases de dados no servidor, ou podem ser criadas na própria base de dados (chamados utilizadores contidos). Para obter informações sobre como criar e gerir inícios de sessão, veja [Gerir inícios de sessão](sql-database-manage-logins.md). Utilize bases de dados contidas para melhorar a portabilidade e a escalabilidade. Para obter mais informações sobre utilizadores contidos, veja [Contained Database Users - Making Your Database Portable (Utilizadores de Bases de Dados Contidas - Tornar a Base de Dados Portátil)](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable), [CREATE USER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql), e [Contained Databases (Bases de Dados Contidas)](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases).

Como melhor prática, a sua aplicação deve utilizar uma conta dediacada para se autenticar. Desta forma, pode limitar as permissões concedidas à aplicação e reduzir os riscos de atividades maliciosas, caso o código da aplicação seja vulnerável a um ataque de injeção SQL. A abordagem recomendada consiste em criar um [utilizador de base de dados contido](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable), que permite que a sua aplicação se autentique diretamente na base de dados. 

## <a name="authorization"></a>Autorização

A autorização diz respeito ao que um utilizador pode fazer dentro de uma Base de Dados SQL do Azure e é controlada pelas [permissões de nível de objeto](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) e [associações de funções](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) de base de dados da sua conta de utilizador. Como melhor prática, deverá conceder aos utilizadores o mínimo de privilégios necessários. A conta de administrador do servidor que está a ligar é membro de db_owner, que tem autoridade para fazer todas as operações na base de dados. Guarde esta conta para a implementação de atualizações de esquema e outras operações de gestão. Utilize a conta "ApplicationUser" com permissões mais limitadas para se ligar da sua aplicação à base de dados com o mínimo de privilégios necessários para a sua aplicação. Para obter mais informações, veja [Gerir inícios de sessão](sql-database-manage-logins.md).

Normalmente, apenas os administradores necessitam de aceder à base de dados `master`. O acesso de rotina a cada base de dados do utilizador deve ser feito através dos utilizadores de base de dados contida não-administradores criados em cada base de dados. Quando utiliza utilizadores de base de dados contida, não precisa de criar inícios de sessão na base de dados `master`. Para obter mais informações, veja [Contained Database Users - Making Your Database Portable (Utilizadores de Base de Dados Contidos - Tornar a Sua Base de Dados Portátil)](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable).

Deverá familiarizar-se com as seguintes funcionalidades que podem ser utilizadas para limitar ou efetuar a elevação de permissões:

- A [representação](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) e a [assinatura de módulo](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) podem ser utilizadas para elevar temporariamente permissões de forma segura.
- A [Segurança ao Nível da Linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) pode ser utilizada para limitar as linhas a que um utilizador pode aceder.
- A [Máscara de Dados](sql-database-dynamic-data-masking-get-started.md) pode ser utilizada para limitar a exposição de dados confidenciais.
- Os [Procedimentos armazenados](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) podem ser utilizados para limitar as ações que podem ser realizadas na base de dados.

## <a name="next-steps"></a>Passos Seguintes

- Para obter uma descrição geral das funcionalidades de segurança da Base de Dados SQL, veja [Descrição geral da segurança de SQL](sql-database-security-overview.md).
- Para saber mais sobre regras de firewall, consulte [regras de Firewall](sql-database-firewall-configure.md).
- Para saber mais sobre utilizadores e inícios de sessão, veja [Gerir inícios de sessão](sql-database-manage-logins.md). 
- Para uma discussão sobre a monitorização proativa, veja [auditoria de base de dados](sql-database-auditing.md) e [deteção de ameaças da base de dados SQL](sql-database-threat-detection.md).
- Para obter um tutorial, veja [proteger a sua base de dados do SQL Azure](sql-database-security-tutorial.md).
