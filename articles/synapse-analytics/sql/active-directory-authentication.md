---
title: Azure Active Directory
description: Saiba como utilizar o Azure Ative Directy para autenticação com base de dados SQL, Instância Gerida e SQL de Sinapse
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: a3bd565b26d011e6186cc6957769db57f9cd1c9c
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92093417"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-synapse-sql"></a>Utilize autenticação de diretório ativo Azure para autenticação com Sinapse SQL

A autenticação do Azure Ative Directory é um mecanismo que se conecta ao [Azure Synapse Analytics](../overview-faq.md) utilizando identidades no Azure Ative Directory (Azure AD).

Com a autenticação AZure AD, pode gerir centralmente as identidades dos utilizadores que tenham acesso ao Azure Synapse para simplificar a gestão da permissão. Os benefícios incluem o seguinte:

- Fornece uma alternativa ao nome de utilizador regular e autenticação de senha.
- Ajuda a parar a proliferação de identidades de utilizadores através dos servidores.
- Permite a rotação da palavra-passe num único local.
- Os clientes podem gerir permissões utilizando grupos externos (Azure AD).
- Pode eliminar o armazenamento de palavras-passe, permitindo a autenticação integrada do Windows e outras formas de autenticação suportadas pelo Azure Ative Directory.
- A Azure AD suporta a autenticação baseada em símbolos para aplicações que se ligam ao Azure Synapse.
- A autenticação AZure AD suporta a autenticação ADFS (federação de domínio) ou a autenticação nativa do utilizador/palavra-passe para um Diretório Ativo Azure local sem sincronização de domínio.
- O Azure Active Directory suporta ligações do SQL Server Management Studio que utilizam a Autenticação Universal do Active Directory, o que inclui a Multi-Factor Authentication (MFA).  O MFA inclui a autenticação forte com um leque de opções de verificação fáceis, incluindo chamada telefónica, mensagem de texto, cartões inteligentes com pin ou notificação de aplicações móveis. Para mais informações, consulte [o suporte SSMS para Azure AD MFA com Synapse SQL](mfa-authentication.md).
- O Azure Active Directory suporta ligações semelhante do SQL Server Data Tools (SSDT), que utilizam a Autenticação Interativa do Active Directory. Para obter mais informações, veja [Azure Active Directory support in SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(Suporte do Azure Active Directory no SQL Server Data Tools [SSDT]).

As etapas de configuração incluem os seguintes procedimentos para configurar e utilizar a autenticação do Azure Ative Directory.

1. Criar e povoar Azure AD.
2. Criar uma identidade de Diretório Ativo Azure
3. Atribuir papel à identidade do Azure Ative Directory no espaço de trabalho da Sinapse (pré-visualização)
4. Ligue-se ao Synapse Studio utilizando identidades Azure AD.

## <a name="aad-pass-through-in-azure-synapse-analytics"></a>Passagem da AAD em Azure Synapse Analytics

O Azure Synapse Analytics permite-lhe aceder aos dados no lago de dados utilizando a sua identidade de Diretório Ativo Azure.

Definir direitos de acesso nos ficheiros e dados que são respeitados em diferentes motores de dados permite-lhe simplificar as suas soluções de data lake, tendo um único local onde as permissões são definidas em vez de ter que defini-las em vários lugares.

## <a name="trust-architecture"></a>Confie na arquitetura

O seguinte diagrama de alto nível resume a arquitetura da solução de utilização da autenticação AD AD com o Sinapse SQL. Para suportar a palavra-passe do utilizador nativo AZure AD, apenas é considerada a porção cloud e Azure AD/Synapse Synapse SQL. Para suportar a autenticação federada (ou palavra-passe do utilizador/palavra-passe para credenciais do Windows), é necessária a comunicação com o bloco ADFS. As setas indicam vias de comunicação.

![diagrama aad auth](./media/aad-authentication/1-active-directory-authentication-diagram.png)

O diagrama seguinte indica a federação, confiança e relacionamentos de hospedagem que permitem a um cliente ligar-se a uma base de dados enviando um token. O token é autenticado por um AD Azure, e é fidedigno pela base de dados. 

O Cliente 1 pode representar um Azure Ative Directy com utilizadores nativos ou um AD Azure com utilizadores federados. O Cliente 2 representa uma solução possível, incluindo utilizadores importados; neste exemplo vindo de um Diretório Ativo Azure federado com a ADFS sendo sincronizada com o Azure Ative Directory. 

É importante entender que o acesso a uma base de dados usando a autenticação Azure AD requer que a subscrição do anfitrião esteja associada ao Azure AD. A mesma subscrição deve ser utilizada para criar o SQL Server que acolhe a Base de Dados SQL Azure ou a piscina SQL.

![relação de subscrição](./media/aad-authentication/2-subscription-relationship.png)

## <a name="administrator-structure"></a>Estrutura de administrador

Ao utilizar a autenticação AZure AD, existem duas contas de Administrador para o SQL synapse; o administrador original do SQL Server e o administrador AD Azure. Apenas o administrador baseado numa conta AZure AD pode criar o primeiro utilizador de base de dados Azure AD contido numa base de dados de utilizador. 

O login do administrador AZure AD pode ser um utilizador AD Azure ou um grupo AZure AD. Quando o administrador é uma conta de grupo, pode ser usado por qualquer membro do grupo, permitindo vários administradores AD Azure para a instância Synapse SQL. 

A utilização da conta de grupo como administrador aumenta a gestão, permitindo-lhe adicionar e remover centralmente membros do grupo em Azure AD sem alterar os utilizadores ou permissões no espaço de trabalho Synapse Analytics. Apenas um administrador AD AZure (um utilizador ou grupo) pode ser configurado a qualquer momento.

![estrutura de administração](./media/aad-authentication/3-admin-structure.png)

## <a name="permissions"></a>Permissões

Para criar novos utilizadores, tem de ter a `ALTER ANY USER` permissão na base de dados. A `ALTER ANY USER` permissão pode ser concedida a qualquer utilizador da base de dados. A `ALTER ANY USER` permissão também é detida pelas contas do administrador do servidor, e pelos utilizadores de bases de dados com a `CONTROL ON DATABASE` base de dados ou `ALTER ON DATABASE` permissão para essa base de dados, e por membros da função de `db_owner` base de dados.

Para criar um utilizador de base de dados contido no Synapse SQL, tem de se ligar à base de dados ou à sua instância utilizando uma identidade AD Azure. Para criar o primeiro utilizador de base de dados contido, tem de se ligar à base de dados utilizando um administrador AD Azure (que é o proprietário da base de dados). 

Qualquer autenticação AZure AD só é possível se o administrador AD AD Azure tiver sido criado para o Synapse SQL. Se o administrador do Azure Ative Directory foi removido do servidor, os utilizadores existentes do Azure Ative Directory criados anteriormente dentro do Synapse SQL já não podem ligar-se à base de dados utilizando as suas credenciais de Diretório Azure Ative.
 
## <a name="azure-ad-features-and-limitations"></a>Funcionalidades e limitações da AD AZure

- Os seguintes membros da Azure AD podem ser a provisionados na Synapse SQL:

  - Membros nativos: Um membro criado em Azure AD no domínio gerido ou no domínio do cliente. Para obter mais informações, consulte [Adicionar o seu próprio nome de domínio ao Azure AD](../../active-directory/fundamentals/add-custom-domain.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
  - Membros de domínio federado: Um membro criado em Azure AD com um domínio federado. Para obter mais informações, consulte [o Microsoft Azure agora suporta a federação com o Windows Server Ative Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/).
  - Membros importados de outros ADs Azure que são membros de domínio nativo ou federado.
  - Grupos de diretório ativo criados como grupos de segurança.

- Os utilizadores de AD Azure que fazem parte de um grupo que tem função de `db_owner` servidor não podem usar a sintaxe **[CREDENTIAL CREATE DATABASE SCOPED](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)** contra o Synapse SQL. Verá o seguinte erro:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Conceder a `db_owner` função diretamente ao utilizador Azure AD individual para mitigar a questão **credencial CREATE DATABASE SCOPED.**

- Estas funções do sistema devolvem valores NUOS quando executados sob os principais AD da Azure:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

## <a name="connect-using-azure-ad-identities"></a>Conecte-se usando identidades AD Azure

A autenticação do Azure Ative Directory suporta os seguintes métodos de ligação a uma base de dados utilizando identidades Azure AD:

- Senha de diretório ativo Azure
- Diretório Ativo Azure Integrado
- Diretório Ativo Azure Universal com MFA
- Utilização da autenticação simbólica de aplicação

Os seguintes métodos de autenticação são suportados para os principais servidores AD do Azure (logins)**(pré-visualização pública):**

- Senha de diretório ativo Azure
- Diretório Ativo Azure Integrado
- Diretório Ativo Azure Universal com MFA

### <a name="additional-considerations"></a>Considerações adicionais

- Para melhorar a gestão, recomendamos que você provisa um grupo Azure AD dedicado como administrador.
- Apenas um administrador AD AD (um utilizador ou grupo) pode ser configurado para a piscina Sinaapse SQL a qualquer momento.
  - A adição de principais servidores AD (logins) para SQL on demand (pré-visualização) permite a possibilidade de criar vários principais servidores AD Azure (logins) que podem ser adicionados ao `sysadmin` papel.
- Apenas um administrador AD AD da Synapse SQL pode inicialmente ligar-se ao SQL synapse utilizando uma conta do Azure Ative Directory. O administrador ative directory pode configurar os utilizadores subsequentes da base de dados Azure AD.
- Recomendamos que se ajuste o tempo limite de ligação para 30 segundos.
- SQL Server 2016 Management Studio e SQL Server Data Tools for Visual Studio 2015 (versão 14.0.60311.1April 2016 ou posterior) suportam a autenticação do Azure Ative Directory. (A autenticação AZURE AD é suportada pelo **Fornecedor de Dados-Quadro .NET para SqlServer**; pelo menos versão .NET Framework 4.6). Assim, as versões mais recentes destas ferramentas e aplicações de nível de dados (DAC e . BACPAC) pode utilizar a autenticação AD AZure.
- Começando com a versão 15.0.1, [utilitário sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e suporte [de utilitário do BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) Ative Directy Interactive autenticação com MFA.
- As Ferramentas de Dados do Servidor SQL para o Visual Studio 2015 requerem pelo menos a versão abril de 2016 das Ferramentas de Dados (versão 14.0.60311.1). Atualmente, os utilizadores de AZure AD não são mostrados no SSDT Object Explorer. Como solução alternativa, veja os utilizadores em [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).
- [O Microsoft JDBC Driver 6.0 para o SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) suporta a autenticação AD AZure. Consulte também [a Definição das Propriedades de Ligação](/sql/connect/jdbc/setting-the-connection-properties?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral do acesso e controlo em Synapse SQL, consulte [o controlo de acesso Sinaapse SQL](../sql/access-control.md).
- Para obter mais informações sobre os principais de bases de dados, veja [Principals (Principais)](/sql/relational-databases/security/authentication-access/principals-database-engine?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).
- Para obter mais informações sobre as funções de base de dados, veja [Database roles (Funções de base de dados)](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

 