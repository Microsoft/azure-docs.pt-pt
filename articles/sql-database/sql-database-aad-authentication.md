---
title: Autenticação do Active Directory do Azure - SQL do Azure | Documentos da Microsoft
description: Saiba mais sobre como utilizar o Azure Active Directory para a autenticação com base de dados SQL, instância gerida e SQL Data Warehouse
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 02/20/2019
ms.openlocfilehash: 1318cd3d1c0c51889cc70b6836d06d6d6ee70c24
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57308385"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>Utilizar autenticação do Active Directory do Azure para a autenticação com o SQL

Autenticação do Azure Active Directory é um mecanismo de ligar ao Azure [base de dados SQL](sql-database-technical-overview.md), [instância gerida](sql-database-managed-instance.md), e [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) ao utilizar as identidades no Azure Do Active Directory (Azure AD). 

> [!NOTE]
> Este tópico aplica-se ao servidor SQL do Azure, bem como às bases de dados da Base de Dados SQL e do SQL Data Warehouse que são criadas no servidor SQL do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse.

Com a autenticação do Azure AD, pode gerenciar centralmente as identidades dos utilizadores de base de dados e outros serviços da Microsoft num local central. Gerenciamento de ID central fornece um único local para gerir utilizadores de base de dados e simplifica a gestão de permissões. Os benefícios incluem o seguinte:

- Ele fornece uma alternativa à autenticação do SQL Server.
- Ajuda a parar a proliferação de identidades de utilizador em servidores de base de dados.
- Permite a rotação da palavra-passe num único local.
- Os clientes podem gerir permissões de base de dados através de grupos externos (Azure AD).
- Isso pode eliminar armazenar palavras-passe através da autenticação integrada do Windows e outras formas de autenticação suportado pelo Azure Active Directory.
- Autenticação do Azure AD utiliza utilizadores de base de dados contida para autenticar identidades ao nível da base de dados.
- O Azure AD suporta a autenticação baseada em tokens para as aplicações ligarem à base de dados SQL.
- Autenticação do Azure AD suporta o ADFS (Federação do domínio) ou autenticação de utilizador/palavra-passe nativo para um Azure Active Directory local sem sincronização de domínio.
- O Azure AD suporta ligações do SQL Server Management Studio, que utilizam a autenticação do Active Directory Universal, que inclui o multi-factor Authentication (MFA).  MFA inclui uma autenticação segura com uma variedade de opções de verificação simples — telefonema, mensagem de texto, smart cards com pin ou notificação de aplicação móvel. Para obter mais informações, consulte [suporte do SSMS para o Azure AD MFA com base de dados SQL e SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).
- O Azure AD suporta ligações semelhante do SQL Server Data Tools (SSDT) que utilizam a autenticação interativa do Active Directory. Para obter mais informações, consulte [suporte do Azure Active Directory no SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory).

> [!NOTE]  
> Ligar ao SQL Server em execução numa VM do Azure não é suportada através de uma conta do Azure Active Directory. Utilize um conta do Active Directory do domínio em vez disso.  

Os passos de configuração incluem os seguintes procedimentos para configurar e utilizar a autenticação do Azure Active Directory.

1. Criar e preencher do Azure AD.
2. Opcional: Associar ou altere o active directory que está atualmente associado a sua subscrição do Azure.
3. Criar um administrador do Azure Active Directory para o servidor de base de dados do Azure SQL, a instância gerida, ou o [do Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Configure os computadores cliente.
5. Crie utilizadores de base de dados contidos na base de dados mapeado para identidades do Azure AD.
6. Ligar à base de dados com identidades do Azure AD.

> [!NOTE]
> Para saber como criar e preencher do Azure AD e, em seguida, configurar o Azure AD com a base de dados do Azure SQL, instância gerida e SQL Data Warehouse, veja [configurar o Azure AD com a base de dados do Azure SQL](sql-database-aad-authentication-configure.md).

## <a name="trust-architecture"></a>Arquitetura de confiança

O diagrama de alto nível seguinte resume a arquitetura da solução de como utilizar a autenticação do Azure AD com a base de dados do Azure SQL. Os mesmos conceitos aplicam-se ao SQL Data Warehouse. Para suportar a palavra-passe de utilizador nativa do Azure AD, é considerado apenas a parte da nuvem e a base de dados do Azure AD/Azure SQL. Para suportar a autenticação de federados (ou utilizador/palavra-passe para as credenciais do Windows), a comunicação com o bloco ADFS é necessária. As setas indicam os caminhos de comunicação.

![Diagrama de autenticação do aad][1]

O diagrama a seguir indica o Federação, confiança e relações de alojamento que permitem que um cliente ligar a uma base de dados ao enviar um token. O token é autenticado por um Azure AD e é considerada fidedigna pela base de dados. Cliente 1 pode representar um Azure Active Directory com utilizadores nativos ou um Azure AD com utilizadores federados. Cliente 2 representa uma solução possível, incluindo os utilizadores importados. Neste exemplo vem de um federados do Active Directory do Azure com o ADFS a ser sincronizada com o Azure Active Directory. É importante compreender que o acesso a uma base de dados utilizando autenticação do Azure AD requer que a subscrição de alojamento está associada ao Azure AD. Da mesma subscrição deve ser usada para criar o SQL Server que aloja a base de dados do Azure SQL ou SQL Data Warehouse.

![relação de subscrição][2]

## <a name="administrator-structure"></a>Estrutura de administrador

Ao utilizar a autenticação do Azure AD, existem duas contas de administrador para o servidor de base de dados SQL e a instância gerida; o administrador do SQL Server original e o administrador do Azure AD. Os mesmos conceitos aplicam-se ao SQL Data Warehouse. Apenas o administrador com base numa conta do Azure AD pode criar o primeiro utilizador de base de dados do Azure AD contido num banco de dados do utilizador. O início de sessão de administrador do Azure AD pode ser um utilizador ou um grupo do Azure AD. Quando o administrador é uma conta de grupo, ele pode ser usado por qualquer membro do grupo, permitindo que vários administradores do Azure AD para a instância do SQL Server. Com a conta de grupo como um administrador melhora a capacidade de gerenciamento, permitindo-lhe adicionar e remover membros do grupo no Azure AD sem alterar os utilizadores ou permissões na base de dados SQL de forma centralizada. Apenas um administrador do Azure AD (um utilizador ou grupo) pode ser configurado em qualquer altura.

![estrutura de administrador][3]

## <a name="permissions"></a>Permissões

Para criar novos utilizadores, tem de ter o `ALTER ANY USER` permissão na base de dados. O `ALTER ANY USER` permissão pode ser concedida a qualquer utilizador de base de dados. O `ALTER ANY USER` permissão também é mantida pelas contas de administrador do servidor e os utilizadores de base de dados com o `CONTROL ON DATABASE` ou `ALTER ON DATABASE` permissões para essa base de dados e por membros do `db_owner` função de base de dados.

Para criar um utilizador de base de dados contidos na base de dados do Azure SQL, instância gerida ou SQL Data Warehouse, tem de ligar a base de dados ou uma instância com uma identidade do Azure AD. Para criar o primeiro utilizador de base de dados contida, tem de ligar à base de dados com o administrador do Azure AD (que é o proprietário da base de dados). Isso é demonstrado na [configurar e gerir a autenticação do Azure Active Directory com a base de dados SQL ou SQL Data Warehouse](sql-database-aad-authentication-configure.md). Qualquer autenticação do Azure AD só é possível se o administrador do Azure AD foi criado para o servidor de base de dados do Azure SQL ou SQL Data Warehouse. Se o administrador do Azure Active Directory foi removido do servidor, os utilizadores do Azure Active Directory existentes criados anteriormente no SQL Server já não podem ligar à base de dados com as respetivas credenciais do Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>As funcionalidades do Azure AD e limitações

- Os seguintes membros do Azure AD podem ser aprovisionados no servidor SQL do Azure ou SQL Data Warehouse:

  - Membros nativos: Membro criado no Azure AD no domínio gerido ou num domínio dos clientes. Para obter mais informações, consulte [adicionar seu próprio nome de domínio para o Azure AD](../active-directory/active-directory-domains-add-azure-portal.md).
  - Membros do domínio federado: Membro criado no Azure AD com um domínio federado. Para obter mais informações, consulte [Microsoft Azure suporta agora a Federação com o Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/).
  - Importados os membros do outro Azure AD que sejam membros do domínio federado ou nativo.
  - Grupos do Active Directory criados como grupos de segurança.

- Utilizadores do Azure AD que fazem parte de um grupo que tenha `db_owner` função de servidor não é possível utilizar o **[CREATE DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** sintaxe em relação a SQL Database do Azure e Azure SQL Data Warehouse. Verá o seguinte erro:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Conceder a `db_owner` função diretamente para o Azure AD individual utilizador para atenuar os **CREATE DATABASE SCOPED CREDENTIAL** problema.

- Estas funções de sistema devolvem valores nulos quando executada em entidades de segurança do Azure AD:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="manage-instances"></a>Gerir instâncias

- Principais de servidor do Azure AD (inícios de sessão) e os utilizadores são suportados como uma funcionalidade de pré-visualização para [instâncias geridas](sql-database-managed-instance.md).
- Definir entidades de segurança de servidor do Azure AD (inícios de sessão) mapeado para um grupo do Azure AD como proprietário de base de dados não é suportado no [instâncias geridas](sql-database-managed-instance.md).
    - Uma extensão, isso é que, quando é adicionado como parte de um grupo a `dbcreator` função de servidor, os utilizadores do grupo esta situação pode ligar à instância gerida e criar novas bases de dados, mas não será possível acessar o banco de dados. Isto acontece porque o novo proprietário de base de dados é SA e não o utilizador do Azure AD. Este problema não se manifestam se o utilizador individual é adicionado ao `dbcreator` função de servidor.
- Execução de tarefas e gestão de agentes de SQL é suportada para principais de servidor do Azure AD (inícios de sessão).
- Base de dados de cópia de segurança e restaurar as operações podem ser executadas por entidades de servidor do Azure AD (inícios de sessão).
- Auditoria de todas as instruções relacionadas com principais de servidor do Azure AD (inícios de sessão) e eventos de autenticação é suportada.
- Ligação de administrador dedicada para os principais de servidor do Azure AD (inícios de sessão) que são membros da função de servidor sysadmin é suportada.
    - Compatíveis por meio do utilitário SQLCMD e SQL Server Management Studio.
- Acionadores de início de sessão são suportados para eventos de início de sessão provenientes de principais de servidor do Azure AD (inícios de sessão).
- Correio de Mediador de serviço e DB pode ser configurados com um principal de servidor do Azure AD (início de sessão).


## <a name="connecting-using-azure-ad-identities"></a>A ligar através de identidades do Azure AD

Autenticação do Azure Active Directory suporta os seguintes métodos de conexão a uma base de dados com identidades do Azure AD:

- Utilizando a autenticação integrada do Windows
- Utilizar um nome principal do Azure AD e uma palavra-passe
- Utilizar a autenticação de token de aplicação

Os seguintes métodos de autenticação são suportados para principais de servidor do Azure AD (inícios de sessão) (**pré-visualização pública**):

- Palavra-passe do Azure Active Directory
- O Azure Active Directory integrado
- O Azure Active Directory Universal com MFA
- O Azure Active Directory interativo


### <a name="additional-considerations"></a>Considerações adicionais

- Para melhorar a capacidade de gestão, recomendamos que aprovisiona um dedicada do Azure AD grupo como um administrador.   
- Apenas um administrador do Azure AD (um utilizador ou grupo) pode ser configurado para um servidor de base de dados do Azure SQL ou o Azure SQL Data Warehouse em qualquer altura.
  - A adição de principais de servidor do Azure AD (inícios de sessão) para instâncias geridas (**pré-visualização pública**) permite que a possibilidade de criar vários Azure principais de servidor de AD (inícios de sessão) que podem ser adicionadas para o `sysadmin` função.
- Inicialmente, apenas um administrador do Azure AD para o SQL Server pode ligar para o servidor de base de dados do Azure SQL, a instância gerida ou o Azure SQL Data Warehouse através de uma conta do Azure Active Directory. Administrador do Active Directory, pode configurar subsequente do Azure AD os utilizadores de base de dados.   
- Recomendamos a definição de tempo limite da conexão para 30 segundos.   
- SQL Server 2016 Management Studio e SQL Server Data Tools para Visual Studio 2015 (versão 14.0.60311.1April 2016 ou posterior) suportam a autenticação do Azure Active Directory. (Autenticação do azure AD é suportada pelos **.NET Framework Data Provider Pro SqlServer**; pelo menos, versão .NET Framework 4.6). Por isso as versões mais recentes destas ferramentas e aplicações de camada de dados (DAC e. BACPAC) pode utilizar a autenticação do Azure AD.   
- Versão 15.0.1, a partir [o utilitário sqlcmd](/sql/tools/sqlcmd-utility) e [o utilitário bcp](/sql/tools/bcp-utility) suportar a autenticação interativa do Active Directory com a MFA.
- SQL Server Data Tools para Visual Studio 2015 requer, pelo menos, a versão de Abril de 2016 das ferramentas de dados (versão 14.0.60311.1). Atualmente, os utilizadores do Azure AD não são apresentados no SSDT Object Explorer. Como solução, ver os usuários [database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
- [Microsoft JDBC Driver 6.0 para o SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) autenticação de suporte do Azure AD. Além disso, veja [definindo as propriedades de ligação](https://msdn.microsoft.com/library/ms378988.aspx).   
- O PolyBase não pode autenticar utilizando a autenticação do Azure AD.   
- Autenticação do Azure AD é suportada para a base de dados SQL através do portal do Azure **importar base de dados** e **exportar a base de dados** painéis. A importação e exportação através da autenticação do Azure AD também é suportado a partir do comando do PowerShell.   
- Autenticação do Azure AD é suportada para a base de dados SQL, instância gerida e SQL Data Warehouse através da utilização da CLI. Para obter mais informações, consulte [configurar e gerir a autenticação do Azure Active Directory com a base de dados SQL ou SQL Data Warehouse](sql-database-aad-authentication-configure.md) e [SQL Server - az o sql server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Passos Seguintes

- Para saber como criar e preencher do Azure AD e, em seguida, configurar o Azure AD com a base de dados do Azure SQL ou armazém de dados SQL do Azure, veja [configurar e gerir a autenticação do Azure Active Directory com a base de dados SQL, instância gerida ou SQL Data Warehouse ](sql-database-aad-authentication-configure.md).
- Para obter um tutorial de como utilizar principais de servidor do Azure AD (inícios de sessão) com instâncias geridas, consulte [principais de servidor (inícios de sessão) do Azure AD com instâncias geridas](sql-database-managed-instance-aad-security-tutorial.md)
- Para obter uma descrição geral do acesso e controlo na Base de Dados SQL, veja [Acesso e controlo da Base de Dados SQL](sql-database-control-access.md).
- Para obter uma descrição geral de inícios de sessão, utilizadores e funções de base de dados da Base de Dados SQL, veja [Inícios de sessão, utilizadores e funções de base de dados](sql-database-manage-logins.md).
- Para obter mais informações sobre os principais de bases de dados, veja [Principals (Principais)](https://msdn.microsoft.com/library/ms181127.aspx).
- Para obter mais informações sobre as funções de base de dados, veja [Database roles (Funções de base de dados)](https://msdn.microsoft.com/library/ms189121.aspx).
- Para a sintaxe sobre a criação do Azure AD principais de servidor (inícios de sessão) para instâncias geridas, consulte [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).
- Para obter mais informações sobre as regras de firewall na Base de Dados SQL, veja [Regras de firewall da Base de Dados SQL](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png
