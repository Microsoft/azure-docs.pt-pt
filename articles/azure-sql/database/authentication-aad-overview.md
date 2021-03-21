---
title: Autenticação do Azure Active Directory
description: Saiba como utilizar o Azure Ative Directy para autenticação com Azure SQL Database, Azure SQL Managed Instance e Synapse SQL in Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 04/23/2020
ms.openlocfilehash: a636c0e2a41b636f30ada14d4f16a022f2890b71
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96454294"
---
# <a name="use-azure-active-directory-authentication"></a>Utilize a autenticação do Diretório Ativo Azure

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

A autenticação Azure Ative Directory (Azure AD) é um mecanismo de ligação à [Base de Dados Azure SQL](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md), e [Synapse SQL in Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) utilizando identidades em Azure AD.

> [!NOTE]
> Este artigo aplica-se à Base de Dados Azure SQL, SQL Managed Instance e Azure Synapse Analytics.

Com a autenticação do Azure Active Directory, pode gerir de forma centralizada as identidades dos utilizadores da base de dados e outros serviços Microsoft numa única localização central. A gestão de IDs centralizada disponibiliza um único local para gerir utilizadores da base de dados e simplifica a gestão de permissões. Os benefícios incluem o seguinte:

- Fornece uma alternativa à autenticação do SQL Server.
- Ajuda a parar a proliferação de identidades dos utilizadores através dos servidores.
- Permite a rotação da palavra-passe num único local.
- Os clientes podem gerir permissões de base de dados utilizando grupos externos (Azure AD).
- Pode eliminar o armazenamento de palavras-passe, permitindo a autenticação integrada do Windows e outras formas de autenticação suportadas pelo Azure Ative Directory.
- A autenticação AZURE AD utiliza utilizadores de bases de dados contidos para autenticar identidades ao nível da base de dados.
- A Azure AD suporta a autenticação baseada em símbolos para aplicações que se ligam à Base de Dados SQL e à SQL Managed Instance.
- Suportes de autenticação AD AZure:
  - Identidades só em nuvem Azure.
  - Identidades híbridas AD AZure que suportam:
    - Autenticação em nuvem com duas opções aliadas a **autenticação** de assinatura única (SSO) e autenticação **de hash de senha** sem emenda.
    - Autenticação federada.
  - Para obter mais informações sobre os métodos de autenticação AZure AD e qual escolher, consulte o seguinte artigo:
    - [Escolha o método de autenticação certo para a sua solução de identidade híbrida Azure Ative Directory](../../active-directory/hybrid/choose-ad-authn.md)

- O Azure AD suporta ligações do SQL Server Management Studio que utilizam a Autenticação Universal do Diretório Ativo, que inclui a Autenticação Multi-Factor. A autenticação multi-factor inclui autenticação forte com um leque de opções de verificação fáceis - chamada telefónica, mensagem de texto, cartões inteligentes com pin ou notificação de aplicações móveis. Para mais informações, consulte [o suporte SSMS para autenticação multi-factor Azure AD com Base de Dados Azure SQL, SQL Managed Instance e Azure Synapse](authentication-mfa-ssms-overview.md)

- O Azure Active Directory suporta ligações semelhante do SQL Server Data Tools (SSDT), que utilizam a Autenticação Interativa do Active Directory. Para obter mais informações, consulte [o suporte do Azure Ative Directory em SqL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory)

> [!NOTE]  
> A ligação a uma instância do SQL Server que está em execução numa máquina virtual (VM) do Azure não é suportada com uma conta do Azure Ative Directory. Em alternativa, utilize um domínio de conta do Active Directory.  

As etapas de configuração incluem os seguintes procedimentos para configurar e utilizar a autenticação do Azure Ative Directory.

1. Criar e povoar Azure AD.
2. Opcional: Associe ou altere o diretório ativo que está atualmente associado à sua Subscrição Azure.
3. Criar um administrador do Azure Ative Directory.
4. Configure os computadores do seu cliente.
5. Crie utilizadores de bases de dados contidos na sua base de dados mapeadas para identidades Azure AD.
6. Conecte-se à sua base de dados utilizando identidades Azure AD.

> [!NOTE]
> Para aprender a criar e povoar a AD AD, e depois configurar a Azure AD com Azure SQL Database, SQL Managed Instance e Synapse SQL in Azure Synapse Analytics, consulte [Configure Azure AD com Base de Dados Azure SQL](authentication-aad-configure.md).

## <a name="trust-architecture"></a>Confie na arquitetura

- Apenas a parte em nuvem de Azure AD, SQL Database, SQL Managed Instance e Azure Synapse é considerada para suportar senhas de utilizador nativas Azure AD.
- Para suportar credenciais de assinatura únicas do Windows (ou user/password para credencial windows), utilize credenciais do Azure Ative Directory a partir de um domínio federado ou gerido que esteja configurado para uma única sessão de sinalização sem emenda para a autenticação de hash de passagem e palavra-passe. Para obter mais informações, consulte [o Azure Ative Directory Seamless Single Sign-On](../../active-directory/hybrid/how-to-connect-sso.md).
- Para suportar a autenticação federada (ou palavra-passe do utilizador/palavra-passe para credenciais do Windows), é necessária a comunicação com o bloco ADFS.

Para obter mais informações sobre as identidades híbridas AD AD, a configuração e sincronização, consulte os seguintes artigos:

- Autenticação de hash de [palavra-passe - Implementar sincronização de hash de palavra-passe com sincronização Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Autenticação pass-through - [Autenticação passativa do Diretório Ativo Azure](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Autenticação federada - [Implantação de Serviços da Federação de Diretórios Ativos em Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) e [Azure AD Connect e federação](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

Para obter uma autenticação federada por amostra com infraestrutura ADFS (ou palavra-passe para credenciais windows), consulte o diagrama abaixo. As setas indicam vias de comunicação.

![diagrama aad auth][1]

O diagrama seguinte indica a federação, confiança e relacionamentos de hospedagem que permitem a um cliente ligar-se a uma base de dados enviando um token. O token é autenticado por um AD Azure, e é fidedigno pela base de dados. O Cliente 1 pode representar um Azure Ative Directy com utilizadores nativos ou um AD Azure com utilizadores federados. O Cliente 2 representa uma solução possível, incluindo utilizadores importados, neste exemplo provenientes de um Diretório Ativo Azure federado com a ADFS a ser sincronizada com o Azure Ative Directory. É importante entender que o acesso a uma base de dados usando a autenticação Azure AD requer que a subscrição do anfitrião esteja associada ao Azure AD. A mesma subscrição deve ser utilizada para criar a Base de Dados Azure SQL, sql Gestd Instance ou recursos Azure Synapse.

![relação de subscrição][2]

## <a name="administrator-structure"></a>Estrutura de administrador

Ao utilizar a autenticação Azure AD, existem duas contas de Administrador: o administrador original da Base de Dados Azure SQL e o administrador AD Azure. Os mesmos conceitos aplicam-se ao Azure Synapse. Apenas o administrador baseado numa conta AZure AD pode criar o primeiro utilizador de base de dados Azure AD contido numa base de dados de utilizador. O login do administrador AZure AD pode ser um utilizador AD Azure ou um grupo AZure AD. Quando o administrador é uma conta de grupo, pode ser utilizado por qualquer membro do grupo, permitindo vários administradores AD Azure para o servidor. A utilização da conta de grupo como administrador aumenta a gestão, permitindo-lhe adicionar e remover membros do grupo centralmente em Azure AD sem alterar os utilizadores ou permissões na Base de Dados SQL ou Azure Synapse. Apenas um administrador AD AZure (um utilizador ou grupo) pode ser configurado a qualquer momento.

![estrutura de administração][3]

## <a name="permissions"></a>Permissões

Para criar novos utilizadores, tem de ter a `ALTER ANY USER` permissão na base de dados. A `ALTER ANY USER` permissão pode ser concedida a qualquer utilizador da base de dados. A `ALTER ANY USER` permissão também é detida pelas contas do administrador do servidor, e pelos utilizadores de bases de dados com a `CONTROL ON DATABASE` base de dados ou `ALTER ON DATABASE` permissão para essa base de dados, e por membros da função de `db_owner` base de dados.

Para criar um utilizador de base de dados contido na Base de Dados Azure SQL, SQL Managed Instance ou Azure Synapse, tem de se ligar à base de dados ou instância utilizando uma identidade AD Azure. Para criar o primeiro utilizador de base de dados contido, tem de se ligar à base de dados utilizando um administrador AD Azure (que é o proprietário da base de dados). Isto é demonstrado em [Configurar e gerir a autenticação do Azure Ative Directory com base de dados SQL ou Azure Synapse.](authentication-aad-configure.md) A autenticação AZure AD só é possível se o administrador AD AD Azure tiver sido criado para a Base de Dados Azure SQL, SQL Managed Instance ou Azure Synapse. Se o administrador do Azure Ative Directory foi removido do servidor, os utilizadores existentes do Azure Ative Directory criados anteriormente dentro do SQL Server já não podem ligar-se à base de dados utilizando as suas credenciais de Diretório Azure Ative.

## <a name="azure-ad-features-and-limitations"></a>Funcionalidades e limitações da AD AZure

- Os seguintes membros da Azure AD podem ser previstos para a Base de Dados Azure SQL:

  - Membros nativos: Um membro criado em Azure AD no domínio gerido ou no domínio do cliente. Para obter mais informações, consulte [Adicionar o seu próprio nome de domínio ao Azure AD](../../active-directory/fundamentals/add-custom-domain.md).
  - Membros de um domínio ative directory federados com O Diretório Ativo Azure em um domínio gerido configurado para um único sign-on sem emenda com autenticação de hash de passagem ou palavra-passe. Para obter mais informações, consulte [o Microsoft Azure agora suporta a federação com o Windows Server Ative Directory](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory//) e [o Azure Ative Directory Seamless Single Sign-On](../../active-directory/hybrid/how-to-connect-sso.md).
  - Membros importados de outros AD's Azure que são membros de domínio nativo ou federado.
  - Grupos de diretório ativo criados como grupos de segurança.

- Os utilizadores de AD Azure que fazem parte de um grupo que tem função de `db_owner` servidor não podem utilizar a sintaxe **[CREDENTIAL CREATE DATABASE SCOPED](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** contra a Base de Dados Azure SQL e Azure Synapse. Verá o seguinte erro:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Conceder a `db_owner` função diretamente ao utilizador Azure AD individual para mitigar a questão **credencial CREATE DATABASE SCOPED.**

- Estas funções do sistema devolvem valores NUOS quando executados sob os principais AD da Azure:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="sql-managed-instance"></a>Instância Gerida do SQL

- Os principais servidores AD do Azure (logins) e os utilizadores são suportados para [a SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md).
- Definição de principais servidores AD (logins) mapeados para um grupo AD Azure, uma vez que o proprietário da base de dados não é suportado em [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md).
  - Uma extensão disto é que quando um grupo é adicionado como parte da função do `dbcreator` servidor, os utilizadores deste grupo podem ligar-se à SQL Managed Instance e criar novas bases de dados, mas não serão capazes de aceder à base de dados. Isto porque o novo proprietário da base de dados é SA, e não o utilizador AZURE AD. Este problema não se manifesta se o utilizador individual for adicionado à função do `dbcreator` servidor.
- A gestão de agentes SQL e a execução de empregos são suportadas para os principais servidores AD da Azure (logins).
- As operações de backup e restauro da base de dados podem ser executadas pelos principais servidores AD do Azure (logins).
- A auditoria de todas as declarações relacionadas com os principais servidores AD do Azure (logins) e eventos de autenticação é suportada.
- É suportada a ligação dedicada do administrador para os principais servidores AD do Azure (logins) que são membros da função do servidor Sysadmin.
  - Suportado através do SQLCMD Utility e do SQL Server Management Studio.
- Os gatilhos de início de sessão são suportados para eventos de início de sessão provenientes de diretores de servidores AD do Azure (logins).
- O Corretor de Serviços e o correio DB podem ser configurados utilizando um servidor AD AD Azure (login).

## <a name="connect-by-using-azure-ad-identities"></a>Conecte-se utilizando identidades AZure AD

A autenticação do Azure Ative Directory suporta os seguintes métodos de ligação a uma base de dados utilizando identidades Azure AD:

- Senha de diretório ativo Azure
- Diretório Ativo Azure Integrado
- Diretório Ativo Azure Universal com Autenticação Multi-Factor
- Utilização da autenticação simbólica de aplicação

Os seguintes métodos de autenticação são suportados para os principais servidores AZure AD (logins):

- Senha de diretório ativo Azure
- Diretório Ativo Azure Integrado
- Diretório Ativo Azure Universal com Autenticação Multi-Factor

### <a name="additional-considerations"></a>Considerações adicionais

- Para melhorar a gestão, recomendamos que você provisa um grupo Azure AD dedicado como administrador.
- Apenas um administrador AD AD (um utilizador ou grupo) pode ser configurado para um servidor na BASE de Dados SQL ou Azure Synapse a qualquer momento.
  - A adição de principais servidores AD (logins) para SQL Managed Instance permite a possibilidade de criar vários principais servidores AD Azure (logins) que podem ser adicionados à `sysadmin` função.
- Apenas um administrador AD AZure para o servidor pode inicialmente ligar-se ao servidor ou caso gerido usando uma conta do Azure Ative Directory. O administrador ative directory pode configurar os utilizadores subsequentes da base de dados Azure AD.
- Recomendamos que se ajuste o tempo limite de ligação para 30 segundos.
- SQL Server 2016 Management Studio e SQL Server Data Tools for Visual Studio 2015 (versão 14.0.60311.1April 2016 ou posterior) suportam a autenticação do Azure Ative Directory. (A autenticação AZURE AD é suportada pelo **Fornecedor de Dados-Quadro .NET para SqlServer**; pelo menos versão .NET Framework 4.6). Por isso, as versões mais recentes destas ferramentas e aplicações de nível de dados (DAC e BACPAC) podem utilizar a autenticação AD Azure.
- Começando com a versão 15.0.1, [utilitário sqlcmd](/sql/tools/sqlcmd-utility) e suporte [de utilitário do BCP](/sql/tools/bcp-utility) Ative Directy Interactive autenticação com autenticação multi-factor.
- As Ferramentas de Dados do Servidor SQL para o Visual Studio 2015 requerem pelo menos a versão abril de 2016 das Ferramentas de Dados (versão 14.0.60311.1). Atualmente, os utilizadores de Azure AD não são mostrados no SSDT Object Explorer. Como solução alternativa, veja os utilizadores em [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).
- [O Microsoft JDBC Driver 6.0 para o SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) suporta a autenticação AD AZure. Consulte também [a Definição das Propriedades de Ligação](/sql/connect/jdbc/setting-the-connection-properties).
- A PolyBase não pode autenticar utilizando a autenticação Azure AD.
- A autenticação Azure AD é suportada para Azure SQL Database e Azure Synapse utilizando o portal Azure **Import Database** e as lâminas **de base de dados de exportação.** A importação e exportação utilizando a autenticação Azure AD também é suportada por um comando PowerShell.
- A autenticação AZure AD é suportada para base de dados SQL, SQL Managed Instance e Azure Synapse com a utilização do CLI. Para obter mais informações, consulte [a autenticação Configure E gere a autenticação AD do Azure com base de dados SQL ou Azure Synapse](authentication-aad-configure.md) e [SQL Server - servidor az sql](/cli/azure/sql/server).

## <a name="next-steps"></a>Passos seguintes

- Para aprender a criar e povoar uma instância AD Azure e, em seguida, configugá-la com Azure SQL Database, SQL Managed Instance, ou Azure Synapse, ver [Configurar e gerir a autenticação do Azure Ative Directory com base de dados SQL, SQL Managed Instance ou Azure Synapse](authentication-aad-configure.md).
- Para um tutorial de utilização de principais servidores AD (logins) com SQL Managed Instance, consulte [os principais dos servidores AD do Azure (logins) com sql Managed Instance](../managed-instance/aad-security-configure-tutorial.md)
- Para uma visão geral de logins, utilizadores, funções de base de dados e permissões na Base de Dados SQL, consulte [Logins, utilizadores, funções de base de dados e permissões.](logins-create-manage.md)
- Para obter mais informações sobre os principais de bases de dados, veja [Principals (Principais)](/sql/relational-databases/security/authentication-access/principals-database-engine).
- Para obter mais informações sobre as funções de base de dados, veja [Database roles (Funções de base de dados)](/sql/relational-databases/security/authentication-access/database-level-roles).
- Para sintaxe sobre a criação de principais servidores AD (logins) para SQL Managed Instance, consulte  [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true).
- Para obter mais informações sobre as regras de firewall na Base de Dados SQL, veja [Regras de firewall da Base de Dados SQL](firewall-configure.md).

<!--Image references-->
[1]: ./media/authentication-aad-overview/1aad-auth-diagram.png
[2]: ./media/authentication-aad-overview/2subscription-relationship.png
[3]: ./media/authentication-aad-overview/3admin-structure.png
