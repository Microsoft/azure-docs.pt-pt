---
title: Azure Active Directory
description: Saiba mais sobre como usar Azure Active Directory para autenticação com o banco de dados SQL, Instância Gerenciada e SQL Data Warehouse
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 02/20/2019
ms.openlocfilehash: e0eeb48490c869c4a3b46bfd71fca72e0ab1c2ff
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816543"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>Usar a autenticação Azure Active Directory para autenticação com o SQL

A autenticação Azure Active Directory é um mecanismo de conexão com o [banco de dados SQL](sql-database-technical-overview.md)do azure, [instância gerenciada](sql-database-managed-instance.md)e [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) usando identidades no Azure Active Directory (AD do Azure). 

> [!NOTE]
> Este tópico aplica-se ao servidor SQL do Azure, bem como às bases de dados da Base de Dados SQL e do SQL Data Warehouse que são criadas no servidor SQL do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse.

Com a autenticação do Azure Active Directory, pode gerir centralmente as identidades dos utilizadores da base de dados e de outros serviços da Microsoft num local central. O gerenciamento de identificação central fornece um único local para gerenciar usuários de banco de dados e simplifica o gerenciamento de permissões. Os benefícios incluem o seguinte:

- Ele fornece uma alternativa para SQL Server autenticação.
- Ajuda a interromper a proliferação de identidades de usuário entre servidores de banco de dados.
- Permite a rotação de senha em um único lugar.
- Os clientes podem gerenciar permissões de banco de dados usando grupos externos (AD do Azure).
- Ele pode eliminar o armazenamento de senhas habilitando a autenticação integrada do Windows e outras formas de autenticação com suporte pelo Azure Active Directory.
- A autenticação do Azure AD usa usuários de banco de dados independente para autenticar identidades no nível do banco de dados.
- O Azure AD dá suporte à autenticação baseada em token para aplicativos que se conectam ao banco de dados SQL.
- A autenticação do Azure AD dá suporte ao ADFS (Federação de domínio) ou à autenticação nativa de usuário/senha para um Azure Active Directory local sem sincronização de domínio.
- O Azure Active Directory suporta ligações do SQL Server Management Studio que utilizam a Autenticação Universal do Active Directory, o que inclui a Multi-Factor Authentication (MFA).  A MFA inclui uma autenticação segura com um conjunto de opções de verificação simples: telefonema, mensagem de texto, cartões inteligentes com PIN ou notificação de aplicação móvel. Para obter mais informações, veja [SSMS support for Azure AD MFA with SQL Database and SQL Data Warehouse](sql-database-ssms-mfa-authentication.md) (Suporte do SQL Server Management Studio para a MFA do Azure Active Directory com Base de Dados SQL e SQL Data Warehouse).
- O Azure Active Directory suporta ligações semelhante do SQL Server Data Tools (SSDT), que utilizam a Autenticação Interativa do Active Directory. Para obter mais informações, veja [Azure Active Directory support in SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory)(Suporte do Azure Active Directory no SQL Server Data Tools [SSDT]).

> [!NOTE]  
> Não há suporte para a conexão com SQL Server em execução em uma VM do Azure usando uma conta de Azure Active Directory. Em vez disso, use uma conta de Active Directory de domínio.  

As etapas de configuração incluem os procedimentos a seguir para configurar e usar Azure Active Directory autenticação.

1. Crie e popule o Azure AD.
2. Opcional: associe ou altere o Active Directory que está associado atualmente à sua assinatura do Azure.
3. Crie um administrador de Azure Active Directory para o servidor de banco de dados SQL do Azure, o Instância Gerenciada ou o [SQL data warehouse do Azure](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Configure seus computadores cliente.
5. Crie usuários de banco de dados independente em seu banco de dados mapeado para identidades do Azure AD.
6. Conecte-se ao banco de dados usando identidades do Azure AD.

> [!NOTE]
> Para saber como criar e popular o Azure AD e, em seguida, configurar o Azure AD com o banco de dados SQL do Azure, Instância Gerenciada e SQL Data Warehouse, consulte [Configurar o Azure AD com o banco de dados SQL do Azure](sql-database-aad-authentication-configure.md).

## <a name="trust-architecture"></a>Arquitetura de confiança

O diagrama de alto nível a seguir resume a arquitetura da solução de uso da autenticação do Azure AD com o banco de dados SQL do Azure. Os mesmos conceitos se aplicam a SQL Data Warehouse. Para dar suporte à senha de usuário nativo do Azure AD, somente a parte da nuvem e o banco de dados SQL do Azure AD/Azure são considerados. Para dar suporte à autenticação federada (ou usuário/senha para credenciais do Windows), a comunicação com o bloco do ADFS é necessária. As setas indicam os caminhos de comunicação.

![diagrama de autenticação do AAD][1]

O diagrama a seguir indica a Federação, a confiança e as relações de hospedagem que permitem que um cliente se conecte a um banco de dados enviando um token. O token é autenticado por um Azure AD e é confiável para o banco de dados. O cliente 1 pode representar um Azure Active Directory com usuários nativos ou um Azure AD com usuários federados. O cliente 2 representa uma possível solução, incluindo os usuários importados; Neste exemplo proveniente de um Azure Active Directory federado com o ADFS sendo sincronizado com Azure Active Directory. É importante entender que o acesso a um banco de dados usando a autenticação do Azure AD requer que a assinatura de hospedagem esteja associada ao Azure AD. A mesma assinatura deve ser usada para criar o SQL Server que hospeda o banco de dados SQL do Azure ou SQL Data Warehouse.

![relação de assinatura][2]

## <a name="administrator-structure"></a>Estrutura do administrador

Ao usar a autenticação do Azure AD, há duas contas de administrador para o servidor do banco de dados SQL e Instância Gerenciada; o administrador de SQL Server original e o administrador do Azure AD. Os mesmos conceitos se aplicam a SQL Data Warehouse. Somente o administrador com base em uma conta do Azure AD pode criar o primeiro usuário de banco de dados independente do Azure AD em um banco de dados de usuário. O logon de administrador do Azure AD pode ser um usuário do Azure AD ou um grupo do Azure AD. Quando o administrador é uma conta de grupo, ele pode ser usado por qualquer membro do grupo, habilitando vários administradores do Azure AD para a instância de SQL Server. O uso da conta de grupo como administrador aprimora a capacidade de gerenciamento, permitindo adicionar e remover centralmente membros do grupo no Azure AD sem alterar os usuários ou as permissões no banco de dados SQL. Somente um administrador do Azure AD (um usuário ou grupo) pode ser configurado a qualquer momento.

![estrutura de administração][3]

## <a name="permissions"></a>Permissões

Para criar novos usuários, você deve ter a permissão `ALTER ANY USER` no banco de dados. A permissão `ALTER ANY USER` pode ser concedida a qualquer usuário de banco de dados. A permissão `ALTER ANY USER` também é mantida pelas contas de administrador do servidor e usuários de banco de dados com a permissão `CONTROL ON DATABASE` ou `ALTER ON DATABASE` para esse banco de dados e por membros da função de banco de dados `db_owner`.

Para criar um usuário de banco de dados independente no banco de dados SQL do Azure, Instância Gerenciada ou SQL Data Warehouse, você deve se conectar ao banco de dados ou instância usando uma identidade do Azure AD. Para criar o primeiro usuário de banco de dados independente, você deve se conectar ao banco de dados usando um administrador do Azure AD (que é o proprietário do banco de dados). Isso é demonstrado em [configurar e gerenciar a autenticação de Azure Active Directory com o banco de dados SQL ou SQL data warehouse](sql-database-aad-authentication-configure.md). Qualquer autenticação do Azure AD só será possível se o administrador do Azure AD tiver sido criado para o banco de dados SQL do Azure ou o SQL Data Warehouse Server. Se o administrador do Azure Active Directory tiver sido removido do servidor, Azure Active Directory usuários existentes criados anteriormente dentro do SQL Server não poderão mais se conectar ao banco de dados usando suas credenciais Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Recursos e limitações do Azure AD

- Os seguintes membros do Azure AD podem ser provisionados no Azure SQL Server ou SQL Data Warehouse:

  - Membros nativos: um membro criado no Azure AD no domínio gerenciado ou em um domínio de cliente. Para obter mais informações, consulte [Adicionar seu próprio nome de domínio ao Azure ad](../active-directory/active-directory-domains-add-azure-portal.md).
  - Membros do domínio federado: um membro criado no Azure AD com um domínio federado. Para obter mais informações, consulte [Microsoft Azure agora dá suporte à Federação com o Active Directory do Windows Server](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/).
  - Membros importados de outros AD do Azure que são membros de domínio nativos ou federados.
  - Active Directory grupos criados como grupos de segurança.

- Os usuários do Azure AD que fazem parte de um grupo que tem a função de servidor `db_owner` não podem usar a sintaxe **[CREATE DATABASE no escopo da credencial](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** em relação ao banco de dados SQL do Azure e ao Azure SQL data warehouse. Você verá o seguinte erro:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Conceda a `db_owner` função diretamente ao usuário individual do Azure AD para mitigar o problema de **criação de credencial de escopo do banco de dados** .

- Essas funções do sistema retornam valores nulos quando executadas em entidades de segurança do Azure AD:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="managed-instances"></a>Instâncias Geridas

- As entidades de segurança do servidor do Azure AD (logons) e os usuários têm suporte como um recurso de visualização para [instâncias gerenciadas](sql-database-managed-instance.md).
- A definição de entidades de segurança do Azure AD (logons) mapeados para um grupo do Azure AD como proprietário do banco de dados não tem suporte em [instâncias gerenciadas](sql-database-managed-instance.md).
    - Uma extensão disso é que, quando um grupo é adicionado como parte da função de servidor `dbcreator`, os usuários desse grupo podem se conectar ao Instância Gerenciada e criar novos bancos de dados, mas não poderão acessar o banco. Isso ocorre porque o novo proprietário do banco de dados é SA e não o usuário do Azure AD. Esse problema não se manifestará se o usuário individual for adicionado à função de servidor `dbcreator`.
- A execução de gerenciamento e trabalhos do SQL Agent tem suporte para entidades de segurança do servidor do Azure AD (logons).
- As operações de backup e restauração do banco de dados podem ser executadas por entidades de segurança do servidor do Azure AD (logons).
- Há suporte para a auditoria de todas as instruções relacionadas às entidades de segurança do servidor do Azure AD (logons) e aos eventos de autenticação.
- Há suporte para a conexão de administrador dedicada para entidades de segurança de servidor do Azure AD (logons) que são membros da função de servidor sysadmin.
    - Com suporte por meio do utilitário SQLCMD e SQL Server Management Studio.
- Os gatilhos de logon têm suporte para eventos de logon provenientes de entidades de segurança de servidor do Azure AD (logons).
- Service Broker e o email de BD podem ser configurados usando uma entidade de servidor do Azure AD (logon).


## <a name="connecting-using-azure-ad-identities"></a>Conectando-se usando identidades do Azure AD

Azure Active Directory autenticação dá suporte aos seguintes métodos de conexão a um banco de dados usando identidades do Azure AD:

- Azure Active Directory senha
- Azure Active Directory integrado
- Azure Active Directory universal com MFA
- Usando a autenticação de token de aplicativo

Os seguintes métodos de autenticação têm suporte para entidades de segurança do servidor do Azure AD (logons) (**Visualização pública**):

- Azure Active Directory senha
- Azure Active Directory integrado
- Azure Active Directory universal com MFA


### <a name="additional-considerations"></a>Considerações adicionais

- Para melhorar a capacidade de gerenciamento, recomendamos que você provisione um grupo dedicado do Azure AD como administrador.   
- Somente um administrador do Azure AD (um usuário ou grupo) pode ser configurado para um servidor de banco de dados SQL do Azure ou o Azure SQL Data Warehouse a qualquer momento.
  - A adição de entidades de segurança de servidor do Azure AD (logons) para instâncias gerenciadas (**Visualização pública**) permite a possibilidade de criar várias entidades de segurança de servidor do Azure AD (logons) que podem ser adicionadas à função de `sysadmin`.
- Somente um administrador do Azure AD para SQL Server pode inicialmente se conectar ao servidor do banco de dados SQL do Azure, Instância Gerenciada ou Azure SQL Data Warehouse usando uma conta de Azure Active Directory. O administrador de Active Directory pode configurar os usuários de banco de dados do Azure AD subsequentes.   
- É recomendável definir o tempo limite de conexão para 30 segundos.   
- SQL Server 2016 Management Studio e SQL Server Data Tools para Visual Studio 2015 (versão 14.0.60311.1 de abril de 2016 ou posterior) dão suporte à autenticação Azure Active Directory. (A autenticação do Azure AD tem suporte pelo **Provedor de Dados .NET Framework para SqlServer**; pelo menos a versão .NET Framework 4,6). Portanto, as versões mais recentes dessas ferramentas e aplicativos da camada de dados (DAC e. BACPAC) pode usar a autenticação do Azure AD.   
- A partir da versão 15.0.1, o [utilitário sqlcmd](/sql/tools/sqlcmd-utility) e o [utilitário bcp](/sql/tools/bcp-utility) suportam Active Directory autenticação interativa com MFA.
- O SQL Server Data Tools para Visual Studio 2015 requer pelo menos a versão de abril de 2016 das ferramentas de dados (versão 14.0.60311.1). Atualmente, os usuários do Azure AD não são mostrados no Pesquisador de objetos do SSDT. Como alternativa, exiba os usuários em [Sys. database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
- [O Microsoft JDBC Driver 6,0 para SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) dá suporte à autenticação do Azure AD. Além disso, consulte [definindo as propriedades de conexão](https://msdn.microsoft.com/library/ms378988.aspx).   
- O polybase não pode autenticar usando a autenticação do Azure AD.   
- A autenticação do Azure AD tem suporte para o banco de dados SQL pelas folhas portal do Azure **importar banco de dados** e exportar banco de **dados** . Também há suporte para importar e exportar usando a autenticação do Azure AD no comando do PowerShell.   
- A autenticação do Azure AD tem suporte para o banco de dados SQL, Instância Gerenciada e SQL Data Warehouse pelo uso da CLI. Para obter mais informações, consulte [configurar e gerenciar a autenticação de Azure Active Directory com o banco de dados SQL ou SQL data warehouse](sql-database-aad-authentication-configure.md) e [SQL Server-AZ SQL Server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Passos seguintes

- Para saber como criar e popular o Azure AD e, em seguida, configurar o Azure AD com o banco de dados SQL do Azure ou o Azure SQL Data Warehouse, consulte [configurar e gerenciar a autenticação Azure Active Directory com o banco de dados SQL, instância gerenciada ou SQL data warehouse](sql-database-aad-authentication-configure.md).
- Para obter um tutorial de como usar entidades de segurança de servidor do Azure AD (logons) com instâncias gerenciadas, consulte [entidades de segurança do Azure Ad Server (logons) com instâncias gerenciadas](sql-database-managed-instance-aad-security-tutorial.md)
- Para obter uma descrição geral do acesso e controlo na Base de Dados SQL, veja [Acesso e controlo da Base de Dados SQL](sql-database-control-access.md).
- Para obter uma descrição geral de inícios de sessão, utilizadores e funções de base de dados da Base de Dados SQL, veja [Inícios de sessão, utilizadores e funções de base de dados](sql-database-manage-logins.md).
- Para obter mais informações sobre os principais de bases de dados, veja [Principals (Principais)](https://msdn.microsoft.com/library/ms181127.aspx).
- Para obter mais informações sobre as funções de base de dados, veja [Database roles (Funções de base de dados)](https://msdn.microsoft.com/library/ms189121.aspx).
- Para obter a sintaxe sobre a criação de entidades de segurança do servidor do Azure AD (logons) para instâncias gerenciadas, consulte [criar logon](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).
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
