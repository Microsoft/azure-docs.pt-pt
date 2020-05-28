---
title: Azure Active Directory
description: Saiba como utilizar o Diretório Ativo Azure para autenticação com base de dados Azure SQL, Instância Gerida Azure SQL e Azure Synapse Analytics
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 04/23/2020
ms.openlocfilehash: a1d6ea37c162a5b2bbe9066e25f80f972ce897ee
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041403"
---
# <a name="use-azure-active-directory-authentication"></a>Utilizar a autenticação de diretório ativo Azure
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

A autenticação do Diretório Ativo Azure é um mecanismo de ligação à Base de [Dados Azure SQL,](sql-database-paas-overview.md)à Instância Gerida pelo [Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md)e à [Azure Synapse Analytics (antiga Azure SQL Data Warehouse)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) utilizando identidades no Azure Ative Directory (Azure AD).

> [!NOTE]
> Este artigo aplica-se à Base de Dados Azure SQL, À Instância Gerida SQL e ao Azure Synapse. Para a simplicidade, o Azure SQL é usado quando se refere aos três.

Com a autenticação do Azure Active Directory, pode gerir centralmente as identidades dos utilizadores da base de dados e de outros serviços da Microsoft num local central. A gestão de IDs centralizada disponibiliza um único local para gerir utilizadores da base de dados e simplifica a gestão de permissões. Os benefícios incluem o seguinte:

- Proporciona uma alternativa à autenticação do SQL Server
- Ajuda a parar a proliferação de identidades de utilizadores através de servidores
- Permite a rotação de palavras-passe num único local
- Os clientes podem gerir permissões de bases de dados com grupos externos (Azure Active Directory)
- Tal pode eliminar o armazenamento de palavras-passe ao permitir a autenticação integrada do Windows e outras formas de autenticação suportadas pelo Azure Active Directory
- A autenticação do Azure Active Directory utiliza utilizadores da base de dados contida para autenticar identidades ao nível da base de dados
- Azure AD suporta autenticação baseada em token para aplicações que liguem ao Azure SQL
- Suportes de autenticação Azure AD:
  - Identidades apenas em nuvem azure
  - Identidades híbridas Azure AD que suportam:
    - Autenticação em nuvem com duas opções aliadas a um único sinal insígnia (SSO): autenticação **pass-through** e autenticação **de hash password**
    - Autenticação federada
  - Para obter mais informações sobre os métodos de autenticação da AD Azure e quais escolher, consulte o seguinte artigo:
    - [Escolha o método de autenticação certo para a sua solução de identidade híbrida Azure Ative Directory](../../active-directory/hybrid/choose-ad-authn.md)
- O Azure Active Directory suporta ligações do SQL Server Management Studio que utilizam a Autenticação Universal do Active Directory, o que inclui a Multi-Factor Authentication (MFA). A MFA inclui uma autenticação segura com um conjunto de opções de verificação simples: telefonema, mensagem de texto, cartões inteligentes com PIN ou notificação de aplicação móvel. Para mais informações, consulte suporte [sSMS para Azure AD MFA com Base de Dados Azure SQL, Instância Gerida SQL e Azure Synapse](authentication-mfa-ssms-overview.md)
- O Azure Active Directory suporta ligações semelhante do SQL Server Data Tools (SSDT), que utilizam a Autenticação Interativa do Active Directory. Para mais informações, consulte o [suporte do Diretório Ativo Azure nas Ferramentas de Dados do Servidor SQL (SSDT)](/sql/ssdt/azure-active-directory)

> [!NOTE]  
> A ligação ao SQL Server em execução num VM Azure não é suportada utilizando uma conta de Diretório Ativo Azure. Em alternativa, utilize um domínio de conta do Active Directory.  

Os passos de configuração incluem os seguintes procedimentos para configurar e utilizar a autenticação do Diretório Ativo Azure.

1. Crie e povoea o Azure AD.
2. Opcional: Associar ou alterar o diretório ativo que está atualmente associado à sua Subscrição Azure.
3. Crie um administrador de Diretório Ativo Azure.
4. Configure os computadores dos seus clientes.
5. Crie utilizadores de bases de dados contidos na sua base de dados mapeada para identidades da AD Azure.
6. Ligue-se à sua base de dados utilizando identidades AD Azure.

> [!NOTE]
> Para aprender a criar e povoar o Azure AD, e depois configurar o Azure AD com base de dados Azure SQL, SQL Managed Instance e Azure Synapse, consulte [configure Azure AD com Base de Dados Azure SQL.](authentication-aad-configure.md)

## <a name="trust-architecture"></a>Arquitetura de confiança

- Para suportar a palavra-passe do utilizador nativo da Azure AD, apenas é considerada a porção Cloud e o Azure AD/Azure SQL.
- Para suportar credenciais de inscrição únicas do Windows (ou utilizador/palavra-passe para credenciais do Windows), utilize credenciais de Diretório Ativo Azure a partir de um domínio federado ou gerido que esteja configurado para uma única inscrição perfeita para a autenticação de hash pass-through e password. Para mais informações, consulte [Azure Ative Directory Seamless Single Sign-On](../../active-directory/hybrid/how-to-connect-sso.md).
- Para suportar a autenticação federada (ou utilizador/palavra-passe para credenciais do Windows), é necessária a comunicação com o bloco ADFS.

Para obter mais informações sobre as identidades híbridas Azure AD, a configuração e a sincronização, consulte os seguintes artigos:

- Autenticação de hash password - [Implemente a sincronização de hash de senha com o sincronde Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Autenticação pass-through - [Autenticação de Passagem de Diretório Ativo Azure](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Autenticação federada - Implantação de Serviços da Federação de [Diretório Ativo em Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) e [Azure AD Connect e federação](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

Para obter uma amostra de autenticação federada com infraestrutura ADFS (ou utilizador/senha para credenciais windows), consulte o diagrama abaixo. As setas indicam vias de comunicação.

![aad auth diagrama][1]

O diagrama seguinte indica a federação, a confiança e as relações de hospedagem que permitem a um cliente ligar-se a uma base de dados, enviando um símbolo. O símbolo é autenticado por um Anúncio Azure, e é confiável pela base de dados. O cliente 1 pode representar um Diretório Ativo Azure com utilizadores nativos ou um Azure AD com utilizadores federados. O Cliente 2 representa uma possível solução, incluindo utilizadores importados; neste exemplo vindo de um Diretório Ativo Azure federado com a ADFS a ser sincronizada com o Diretório Ativo Azure. É importante entender que o acesso a uma base de dados utilizando a autenticação Azure AD requer que a subscrição de hospedagem esteja associada ao Anúncio Azure. A mesma subscrição deve ser utilizada para criar os recursos Azure SQL, SQL Managed Instance ou Azure Synapse.

![relação de subscrição][2]

## <a name="administrator-structure"></a>Estrutura de administrador

Ao utilizar a autenticação Azure AD, existem duas contas de Administrador; o administrador original da SQL e o administrador da AD Azure. Os mesmos conceitos aplicam-se ao Azure Synapse. Apenas o administrador com base numa conta Azure AD pode criar o primeiro utilizador de base de dados Azure AD numa base de dados de utilizador. O login do administrador da AD Azure pode ser um utilizador de Anúncios Azure ou um grupo Azure AD. Quando o administrador é uma conta de grupo, pode ser usado por qualquer membro do grupo, permitindo vários administradores de AD Azure para a instância Do Servidor SQL. A utilização da conta de grupo como administrador aumenta a capacidade de gestão, permitindo-lhe adicionar e remover centralmente membros do grupo em Azure AD sem alterar os utilizadores ou permissões no Azure SQL. Apenas um administrador da AD Azure (utilizador ou grupo) pode ser configurado a qualquer momento.

![estrutura de administração][3]

## <a name="permissions"></a>Permissões

Para criar novos utilizadores, tem de ter a `ALTER ANY USER` permissão na base de dados. A permissão pode ser concedida a qualquer utilizador de base de `ALTER ANY USER` dados. A permissão também é detida pelas contas do administrador do servidor e pelos utilizadores da base de dados com a ou a permissão dessa base de `ALTER ANY USER` `CONTROL ON DATABASE` `ALTER ON DATABASE` dados, e por membros da função de base de `db_owner` dados.

Para criar um utilizador de base de dados contido na Base de Dados Azure SQL, SQL Managed Instance ou Azure Synapse, deve ligar-se à base de dados ou à instância utilizando uma identidade Azure AD. Para criar o primeiro utilizador de base de dados contido, deve ligar-se à base de dados utilizando um administrador da AD Azure (que é o proprietário da base de dados). Isto é demonstrado na [Configuração e gere a autenticação do Diretório Ativo Azure com base de dados SQL ou Azure Synapse](authentication-aad-configure.md). A autenticação Azure AD só é possível se o administrador DaD Azure foi criado para a Base de Dados Azure SQL, SQL Managed Instance ou Azure Synapse. Se o administrador do Diretório Ativo Azure foi removido do servidor, os atuais utilizadores do Diretório Ativo Do Azure criados anteriormente dentro do SQL Server já não podem ligar-se à base de dados utilizando as suas credenciais de Diretório Ativo Azure.

## <a name="azure-ad-features-and-limitations"></a>Funcionalidades e limitações da AD Azure

- Os seguintes membros da Azure AD podem ser provisionados para o Azure SQL:

  - Membros nativos: Um membro criado em Azure AD no domínio gerido ou num domínio de cliente. Para mais informações, consulte [Adicionar o seu próprio nome de domínio ao Azure AD](../../active-directory/fundamentals/add-custom-domain.md).
  - Membros de um domínio de Diretório Ativo federado com Diretório Ativo Azure num domínio gerido configurado para um único sign-on sem emenda com a autenticação de hash pass-through ou password. Para mais informações, consulte o Microsoft Azure agora suporta a [federação com o Diretório Ativo do Windows Server](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory//) e o [Diretório Ativo Azure Seamless Single Sign-On](../../active-directory/hybrid/how-to-connect-sso.md).
  - Membros importados de outros AD's azure que são membros nativos ou federados de domínio.
  - Grupos de Diretórios Ativos criados como grupos de segurança.

- Os utilizadores de AD Azure que fazem parte de um grupo que tem a função de servidor não podem utilizar a sintaxe CREDENTIAL DE BASE DE DADOS CREATE DATABASE CONTRA a Base de `db_owner` Dados Azure SQL e a Azure Synapse. **[CREATE DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** Verá o seguinte erro:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Conceda o `db_owner` papel diretamente ao utilizador da AD Azure para mitigar a questão **CREDENTIAL** DE BASE DE DADOS CREATE DATABASE.

- Estas funções de sistema devolvem valores NULOs quando executados sob os principais da AD Azure:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="sql-managed-instance"></a>Instância Gerida do SQL

- Os diretores de servidores Da Azure AD (logins) e os utilizadores são suportados para a [Instância Gerida sQL](../managed-instance/sql-managed-instance-paas-overview.md).
- Definição de diretores de servidores Azure AD (logins) mapeados para um grupo Azure AD como proprietário de base de dados não é suportado em [Instâncias Geridas SQL](../managed-instance/sql-managed-instance-paas-overview.md).
  - Uma extensão disto é que quando um grupo é adicionado como parte da função do `dbcreator` servidor, os utilizadores deste grupo podem ligar-se à Instância Gerida SQL e criar novas bases de dados, mas não serão capazes de aceder à base de dados. Isto porque o novo proprietário da base de dados é SA, e não o utilizador da AD Azure. Este problema não se manifesta se o utilizador individual for adicionado à função do `dbcreator` servidor.
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

Os seguintes métodos de autenticação são suportados para os diretores de servidores DaA Azure (logins):

- Senha de direção ativa azure
- Diretório Ativo Azure Integrado
- Diretório Ativo Azure Universal com MFA

### <a name="additional-considerations"></a>Considerações adicionais

- Para melhorar a capacidade de gestão, recomendamos que provisão de um grupo Azure AD dedicado como administrador.
- Apenas um administrador de AD Azure (utilizador ou grupo) pode ser configurado para um servidor na Base de Dados SQL ou azure Synapse a qualquer momento.
  - A adição de diretores de servidores Azure AD (logins) para Instâncias Geridas SQL permite a possibilidade de criar vários diretores de servidores Azure AD (logins) que podem ser adicionados à `sysadmin` função.
- Apenas um administrador de AD Azure para o servidor pode inicialmente ligar-se ao servidor ou à instância gerida utilizando uma conta De Diretório Ativo Azure. O administrador de Diretório Ativo pode configurar os utilizadores subsequentes da base de dados Azure AD.
- Recomendamos que o tempo de ligação se ajuste a 30 segundos.
- SQL Server 2016 Management Studio e SQL Server Data Tools for Visual Studio 2015 (versão 14.0.60311.1abril 2016 ou mais tarde) suportam a autenticação do Diretório Ativo azure. (A autenticação Azure AD é suportada pelo Fornecedor de **Dados-Quadro .NET para SqlServer;** pelo menos versão .NET Framework 4.6). Por conseguinte, as versões mais recentes destas ferramentas e aplicações a nível de dados (DAC e . BACPAC) pode utilizar a autenticação Azure AD.
- Começando com a versão 15.0.1, [utilitário sqlcmd](/sql/tools/sqlcmd-utility) e [suporte utilitário BCP](/sql/tools/bcp-utility) Ative Directory Interactive autenticação com MFA.
- SQL Server Data Tools for Visual Studio 2015 requer pelo menos a versão abril de 2016 das Ferramentas de Dados (versão 14.0.60311.1). Atualmente, os utilizadores de Anúncios Azure não são mostrados no SSDT Object Explorer. Como uma suposição, veja os utilizadores em [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
- [O Microsoft JDBC Driver 6.0 para o SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) suporta a autenticação Azure AD. Consulte também [a definição das propriedades de ligação](/sql/connect/jdbc/setting-the-connection-properties).
- A PolyBase não pode autenticar utilizando a autenticação Azure AD.
- A autenticação Azure AD é suportada para a Base de Dados Azure SQL e Azure Synapse utilizando o portal Azure **Import Database** e **exportdatabase** blades. A importação e exportação utilizando a autenticação Azure AD também é suportada a partir de um comando PowerShell.
- A autenticação Azure AD é suportada para a Base de Dados SQL, SQL Managed Instance e Azure Synapse com a utilização do CLI. Para mais informações, consulte [Configure e gerea autenticação de Diretório Ativo Azure com Base de Dados SQL ou Azure Synapse](authentication-aad-configure.md) e [SQL Server - servidor az sql](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Próximos passos

- Para aprender a criar e povoar o Azure AD, e depois configurar o Azure AD com base de dados Azure SQL, SQL Managed Instance ou Azure Synapse, consulte [configure e gerea autenticação de Diretório Ativo Azure com Base de Dados SQL, Instância Gerida SQL ou Azure Synapse.](authentication-aad-configure.md)
- Para um tutorial de utilização de diretores de servidores Azure AD (logins) com Instâncias Geridas SQL, consulte os diretores de [servidores DaA Azure (logins) com Instâncias Geridas SQL](../managed-instance/aad-security-configure-tutorial.md)
- Para uma visão geral dos logins, utilizadores, funções de base de dados e permissões na Base de Dados SQL, consulte [Logins, utilizadores, funções de base de dados e permissões](logins-create-manage.md).
- Para obter mais informações sobre os principais de bases de dados, veja [Principals (Principais)](https://msdn.microsoft.com/library/ms181127.aspx).
- Para obter mais informações sobre as funções de base de dados, veja [Database roles (Funções de base de dados)](https://msdn.microsoft.com/library/ms189121.aspx).
- Para sintaxe sobre a criação de diretores de servidores Azure AD (logins) para Instâncias Geridas SQL, consulte [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).
- Para obter mais informações sobre as regras de firewall na Base de Dados SQL, veja [Regras de firewall da Base de Dados SQL](firewall-configure.md).

<!--Image references-->
[1]: ./media/authentication-aad-overview/1aad-auth-diagram.png
[2]: ./media/authentication-aad-overview/2subscription-relationship.png
[3]: ./media/authentication-aad-overview/3admin-structure.png
