---
title: Azure Active Directory
description: Saiba como utilizar o Diretório Ativo Azure para autenticação com base de dados SQL, Instância Gerida e Synapse Azure
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 818337f478a506f80ba87979aa1915d54e6457cf
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78193786"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>Utilize autenticação de diretório ativo Azure para autenticação com SQL

A autenticação do Diretório Ativo Azure é um mecanismo de ligação à Base de Dados Azure [SQL,](sql-database-technical-overview.md) [Instância Gerida](sql-database-managed-instance.md)e [Azure Synapse Analytics](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) utilizando identidades no Azure Ative Directory (Azure AD). 

> [!NOTE]
> Este tópico aplica-se ao servidor Azure SQL, e tanto à Base de Dados SQL como ao Synapse Azure que são criados no servidor Azure SQL. Para a simplicidade, a Base de Dados SQL é utilizada quando se refere tanto à Base de Dados SQL como ao Synapse Azure.

Com a autenticação do Azure Active Directory, pode gerir centralmente as identidades dos utilizadores da base de dados e de outros serviços da Microsoft num local central. A gestão central de ID fornece um único lugar para gerir utilizadores de bases de dados e simplifica a gestão de permissões. Os benefícios incluem o seguinte:

- Fornece uma alternativa à autenticação do Servidor SQL.
- Ajuda a parar a proliferação de identidades de utilizadores através de servidores de base de dados.
- Permite a rotação da palavra-passe num único local.
- Os clientes podem gerir permissões de base de dados utilizando grupos externos (Azure AD).
- Pode eliminar o armazenamento de palavras-passe permitindo a autenticação integrada do Windows e outras formas de autenticação suportadas pelo Diretório Ativo do Azure.
- A autenticação azure AD utiliza utilizadores de bases de dados contidos para autenticar identidades ao nível da base de dados.
- A Azure AD suporta a autenticação baseada em token para aplicações que liguem à Base de Dados SQL.
- A autenticação Azure AD suporta a Autenticação ADFS (federação de domínio) ou autenticação de utilizador/palavra-passe nativo para um Diretório Ativo Azure local sem sincronização de domínio.
- O Azure Active Directory suporta ligações do SQL Server Management Studio que utilizam a Autenticação Universal do Active Directory, o que inclui a Multi-Factor Authentication (MFA).  A MFA inclui uma autenticação segura com um conjunto de opções de verificação simples: telefonema, mensagem de texto, cartões inteligentes com PIN ou notificação de aplicação móvel. Para mais informações, consulte o [suporte sSMS para O MFA Azure AD com base de dados SQL e Azure Synapse](sql-database-ssms-mfa-authentication.md).
- O Azure Active Directory suporta ligações semelhante do SQL Server Data Tools (SSDT), que utilizam a Autenticação Interativa do Active Directory. Para obter mais informações, veja [Azure Active Directory support in SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory)(Suporte do Azure Active Directory no SQL Server Data Tools [SSDT]).

> [!NOTE]  
> A ligação ao SQL Server em execução num VM Azure não é suportada utilizando uma conta de Diretório Ativo Azure. Utilize uma conta de diretório ativo de domínio.  

Os passos de configuração incluem os seguintes procedimentos para configurar e utilizar a autenticação do Diretório Ativo Azure.

1. Crie e povoea o Azure AD.
2. Opcional: Associar ou alterar o diretório ativo que está atualmente associado à sua Subscrição Azure.
3. Crie um administrador de diretório Ativo Azure para o servidor de base de dados Azure SQL, a Instância Gerida ou o [Pool SQL](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Configure os computadores dos seus clientes.
5. Crie utilizadores de bases de dados contidos na sua base de dados mapeada para identidades da AD Azure.
6. Ligue-se à sua base de dados utilizando identidades AD Azure.

> [!NOTE]
> Para aprender a criar e povoar o Azure AD, e depois configurar o Azure AD com base de dados Azure SQL, Managed Instance e SQL pool, consulte [Configure Azure AD com Base de Dados Azure SQL](sql-database-aad-authentication-configure.md).

## <a name="trust-architecture"></a>Arquitetura de confiança

O diagrama de alto nível que se segue resume a arquitetura de solução de utilização da autenticação Azure AD com base de dados Azure SQL. Os mesmos conceitos aplicam-se ao Azure Synapse. Para suportar a palavra-passe do utilizador nativo da AD Azure, apenas é considerada a porção Cloud e a Base de Dados SQL Azure AD/Azure. Para suportar a autenticação federada (ou utilizador/palavra-passe para credenciais do Windows), é necessária a comunicação com o bloco ADFS. As setas indicam vias de comunicação.

![aad auth diagrama][1]

O diagrama seguinte indica a federação, a confiança e as relações de hospedagem que permitem a um cliente ligar-se a uma base de dados, enviando um símbolo. O símbolo é autenticado por um Anúncio Azure, e é confiável pela base de dados. O cliente 1 pode representar um Diretório Ativo Azure com utilizadores nativos ou um Azure AD com utilizadores federados. O Cliente 2 representa uma possível solução, incluindo utilizadores importados; neste exemplo vindo de um Diretório Ativo Azure federado com a ADFS a ser sincronizada com o Diretório Ativo Azure. É importante entender que o acesso a uma base de dados utilizando a autenticação Azure AD requer que a subscrição de hospedagem esteja associada ao Anúncio Azure. A mesma subscrição deve ser utilizada para criar o Servidor SQL que acolhe a Base de Dados Azure SQL ou o Azure Synapse.

![relação de subscrição][2]

## <a name="administrator-structure"></a>Estrutura de administrador

Ao utilizar a autenticação AD Azure, existem duas contas de Administrador para o servidor de base de dados SQL e Instância Gerida; o administrador original do SQL Server e o administrador da AD Azure. Os mesmos conceitos aplicam-se ao Azure Synapse. Apenas o administrador com base numa conta Azure AD pode criar o primeiro utilizador de base de dados Azure AD numa base de dados de utilizador. O login do administrador da AD Azure pode ser um utilizador de Anúncios Azure ou um grupo Azure AD. Quando o administrador é uma conta de grupo, pode ser usado por qualquer membro do grupo, permitindo vários administradores de AD Azure para a instância Do Servidor SQL. A utilização da conta de grupo como administrador aumenta a capacidade de gestão, permitindo-lhe adicionar e remover centralmente membros do grupo em AD Azure sem alterar os utilizadores ou permissões na Base de Dados SQL. Apenas um administrador da AD Azure (utilizador ou grupo) pode ser configurado a qualquer momento.

![estrutura de administração][3]

## <a name="permissions"></a>Permissões

Para criar novos utilizadores, tem de ter a `ALTER ANY USER` permissão na base de dados. A `ALTER ANY USER` permissão pode ser concedida a qualquer utilizador de base de dados. A `ALTER ANY USER` permissão também é detida pelas contas do administrador do servidor, e pelos utilizadores de bases de dados com a `CONTROL ON DATABASE` ou `ALTER ON DATABASE` permissão para essa base de dados, e por membros da função de base de dados `db_owner`.

Para criar um utilizador de base de dados contido na Base de Dados Azure SQL, Na instância gerida ou no Azure Synapse, deve ligar-se à base de dados ou à instância utilizando uma identidade Azure AD. Para criar o primeiro utilizador de base de dados contido, deve ligar-se à base de dados utilizando um administrador da AD Azure (que é o proprietário da base de dados). Isto é demonstrado na [Configuração e gere a autenticação do Diretório Ativo Azure com base de dados SQL ou Azure Synapse](sql-database-aad-authentication-configure.md). Qualquer autenticação Azure AD só é possível se o administrador da AD Azure tiver sido criado para a Base de Dados Azure SQL ou para o servidor Azure Synapse. Se o administrador do Diretório Ativo Azure foi removido do servidor, os atuais utilizadores do Diretório Ativo Do Azure criados anteriormente dentro do SQL Server já não podem ligar-se à base de dados utilizando as suas credenciais de Diretório Ativo Azure.

## <a name="azure-ad-features-and-limitations"></a>Funcionalidades e limitações da AD Azure

- Os seguintes membros da Azure AD podem ser aprovisionados no servidor Azure SQL ou azure Synapse:

  - Membros nativos: Um membro criado em Azure AD no domínio gerido ou num domínio de cliente. Para mais informações, consulte [Adicionar o seu próprio nome de domínio ao Azure AD](../active-directory/active-directory-domains-add-azure-portal.md).
  - Membros federados de domínio: Um membro criado em Azure AD com um domínio federado. Para mais informações, consulte o Microsoft Azure agora suporta a [federação com o Diretório Ativo do Windows Server](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/).
  - Membros importados de outros AD's azure que são membros nativos ou federados de domínio.
  - Grupos de Diretórios Ativos criados como grupos de segurança.

- Os utilizadores de AD Azure que fazem parte de um grupo que tem `db_owner` função de servidor não podem utilizar a sintaxe **[CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** DE BASE DE DADOS CREATE DATABASE CONTRA a Base de Dados Azure SQL e a Azure Synapse. Verá o seguinte erro:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Conceda o papel `db_owner` diretamente ao utilizador da AD Azure para mitigar a questão **CREDENTIAL** DE BASE DE DADOS CREATE DATABASE.

- Estas funções de sistema devolvem valores NULOs quando executados sob os principais da AD Azure:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="managed-instances"></a>Instâncias Geridas

- Os diretores de servidores da AD Azure (logins) e os utilizadores são suportados como uma funcionalidade de pré-visualização para [Instâncias Geridas](sql-database-managed-instance.md).
- Definir os diretores de servidores da AD Azure (logins) mapeados para um grupo Azure AD como proprietário de base de dados não é suportado em [Instâncias Geridas](sql-database-managed-instance.md).
    - Uma extensão disto é que quando um grupo é adicionado como parte da função de servidor `dbcreator`, os utilizadores deste grupo podem ligar-se à Instância Gerida e criar novas bases de dados, mas não poderão aceder à base de dados. Isto porque o novo proprietário da base de dados é SA, e não o utilizador da AD Azure. Este problema não se manifesta se o utilizador individual for adicionado à função `dbcreator` servidor.
- A gestão de agentes da SQL e a execução de postos de trabalho são suportadas para os diretores de servidores da AD Azure (logins).
- As operações de backup e restauro da base de dados podem ser executadas pelos diretores do servidor Azure AD (logins).
- É suportada a auditoria de todas as declarações relacionadas com os diretores de servidores da AD Azure (logins) e eventos de autenticação.
- A ligação dedicada do administrador para os diretores de servidores DaAzure AD (logins) que são membros da função do servidor sysadmin é suportada.
    - Suportado através do Utilitário SQLCMD e do Estúdio de Gestão de Servidores SQL.
- Os gatilhos de logon são suportados para eventos de logon provenientes de diretores de servidores DaAzure AD (logins).
- O Corretor de Serviços e o correio DB podem ser configurados utilizando um servidor Azure AD (login).


## <a name="connecting-using-azure-ad-identities"></a>Ligação utilizando identidades da AD Azure

A autenticação do Diretório Ativo Azure suporta os seguintes métodos de ligação a uma base de dados utilizando identidades da AD Azure:

- Senha de direção ativa azure
- Diretório Ativo Azure Integrado
- Diretório Ativo Azure Universal com MFA
- Utilização da autenticação simbólica da Aplicação

Os seguintes métodos de autenticação são suportados para os diretores de servidores DaA Azure **(logins) (pré-visualização pública):**

- Senha de direção ativa azure
- Diretório Ativo Azure Integrado
- Diretório Ativo Azure Universal com MFA


### <a name="additional-considerations"></a>Considerações adicionais

- Para melhorar a capacidade de gestão, recomendamos que provisão de um grupo Azure AD dedicado como administrador.   
- Apenas um administrador da AD Azure (um utilizador ou grupo) pode ser configurado para um servidor de base de dados Azure SQL ou Azure Synapse a qualquer momento.
  - A adição de diretores de servidores Azure AD (logins) para Instâncias Geridas **(pré-visualização pública)** permite a possibilidade de criar vários diretores de servidores Azure AD (logins) que podem ser adicionados ao `sysadmin` função.
- Apenas um administrador de AD Azure para o SQL Server pode inicialmente ligar-se ao servidor de base de dados Azure SQL, À Instância Gerida ou ao Synapse Azure SQL utilizando uma conta de Diretório Ativo Azure. O administrador de Diretório Ativo pode configurar os utilizadores subsequentes da base de dados Azure AD.   
- Recomendamos que o tempo de ligação se ajuste a 30 segundos.   
- SQL Server 2016 Management Studio e SQL Server Data Tools for Visual Studio 2015 (versão 14.0.60311.1abril 2016 ou mais tarde) suportam a autenticação do Diretório Ativo azure. (A autenticação Azure AD é suportada pelo Fornecedor de **Dados-Quadro .NET para SqlServer;** pelo menos versão .NET Framework 4.6). Por conseguinte, as versões mais recentes destas ferramentas e aplicações a nível de dados (DAC e . BACPAC) pode utilizar a autenticação Azure AD.   
- Começando com a versão 15.0.1, [utilitário sqlcmd](/sql/tools/sqlcmd-utility) e [suporte utilitário BCP](/sql/tools/bcp-utility) Ative Directory Interactive autenticação com MFA.
- SQL Server Data Tools for Visual Studio 2015 requer pelo menos a versão abril de 2016 das Ferramentas de Dados (versão 14.0.60311.1). Atualmente, os utilizadores de Anúncios Azure não são mostrados no SSDT Object Explorer. Como uma suposição, veja os utilizadores em [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
- [O Microsoft JDBC Driver 6.0 para o SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) suporta a autenticação Azure AD. Consulte também [a definição das propriedades de ligação](https://msdn.microsoft.com/library/ms378988.aspx).   
- A PolyBase não pode autenticar utilizando a autenticação Azure AD.   
- A autenticação Azure AD é suportada para base de dados SQL pelo portal Azure **Import Database** and **Export Database** blades. A importação e exportação utilizando a autenticação Azure AD também é suportada a partir do comando PowerShell.   
- A autenticação Azure AD é suportada para base de dados SQL, Instância Gerida e Synapse Azure através do uso CLI. Para mais informações, consulte [Configure e gerea autenticação de Diretório Ativo Azure com Base de Dados SQL ou Azure Synapse](sql-database-aad-authentication-configure.md) e [SQL Server - servidor az sql](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Passos seguintes

- Para aprender a criar e povoar o Azure AD, e depois configurar o Azure AD com a Base de Dados Azure SQL ou azure Synapse, consulte configurar e gerir a [autenticação do Diretório Ativo Azure com base de dados SQL, Instância Gerida ou Synapse Azure.](sql-database-aad-authentication-configure.md)
- Para um tutorial de utilização de diretores de servidores Azure AD (logins) com instâncias geridas, consulte os diretores do [servidor Azure AD (logins) com Instâncias Geridas](sql-database-managed-instance-aad-security-tutorial.md)
- Para obter uma descrição geral do acesso e controlo na Base de Dados SQL, veja [Acesso e controlo da Base de Dados SQL](sql-database-control-access.md).
- Para obter uma descrição geral de inícios de sessão, utilizadores e funções de base de dados da Base de Dados SQL, veja [Inícios de sessão, utilizadores e funções de base de dados](sql-database-manage-logins.md).
- Para obter mais informações sobre os principais de bases de dados, veja [Principals (Principais)](https://msdn.microsoft.com/library/ms181127.aspx).
- Para obter mais informações sobre as funções de base de dados, veja [Database roles (Funções de base de dados)](https://msdn.microsoft.com/library/ms189121.aspx).
- Para sintaxe sobre a criação de diretores de servidores Azure AD (logins) para Instâncias Geridas, consulte [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).
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
