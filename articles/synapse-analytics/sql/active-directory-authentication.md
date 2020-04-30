---
title: Azure Active Directory
description: Saiba como utilizar o Diretório Ativo Azure para autenticação com base de dados SQL, Instância Gerida e SQL Synapse
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: db80c11c3b6eab3b7e682878e479729f4787a40b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82086101"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-synapse-sql"></a>Utilize autenticação de diretório ativo Azure para autenticação com Synapse SQL

A autenticação do Diretório Ativo Azure é um mecanismo de ligação ao [Azure Synapse Analytics](../overview-faq.md) utilizando identidades no Azure Ative Directory (Azure AD).

Com a autenticação da AD Azure, pode gerir centralmente as identidades dos utilizadores que têm acesso ao Azure Synapse para simplificar a gestão da permissão. Os benefícios incluem o seguinte:

- Fornece uma alternativa ao nome de utilizador regular e à autenticação de senha.
- Ajuda a parar a proliferação de identidades de utilizadores através de servidores de base de dados.
- Permite a rotação da palavra-passe num único local.
- Os clientes podem gerir permissões utilizando grupos externos (Azure AD).
- Pode eliminar o armazenamento de palavras-passe permitindo a autenticação integrada do Windows e outras formas de autenticação suportadas pelo Diretório Ativo do Azure.
- A Azure AD suporta a autenticação baseada em token para aplicações que liguem ao Azure Synapse.
- A autenticação Azure AD suporta a Autenticação ADFS (federação de domínio) ou autenticação de utilizador/palavra-passe nativo para um Diretório Ativo Azure local sem sincronização de domínio.
- O Azure Active Directory suporta ligações do SQL Server Management Studio que utilizam a Autenticação Universal do Active Directory, o que inclui a Multi-Factor Authentication (MFA).  A MFA inclui uma autenticação segura com um conjunto de opções de verificação simples: telefonema, mensagem de texto, cartões inteligentes com PIN ou notificação de aplicação móvel. Para mais informações, consulte o [suporte sSMS para O MFA Azure AD com Synapse SQL](mfa-authentication.md).
- O Azure Active Directory suporta ligações semelhante do SQL Server Data Tools (SSDT), que utilizam a Autenticação Interativa do Active Directory. Para obter mais informações, veja [Azure Active Directory support in SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(Suporte do Azure Active Directory no SQL Server Data Tools [SSDT]).

Os passos de configuração incluem os seguintes procedimentos para configurar e utilizar a autenticação do Diretório Ativo Azure.

1. Crie e povoea o Azure AD.
2. Criar uma identidade de Diretório Ativo Azure
3. Atribuir papel à criação de identidade de Diretório Ativo Azure no espaço de trabalho synapse (pré-visualização)
4. Ligue-se ao Estúdio Synapse utilizando identidades Azure AD.

## <a name="aad-pass-through-in-azure-synapse-analytics"></a>Passagem da AAD no Azure Synapse Analytics

A Azure Synapse Analytics permite-lhe aceder aos dados no lago de dados utilizando a sua identidade azure Ative Directory.

Definir direitos de acesso nos ficheiros e dados que são respeitados em diferentes motores de dados permite-lhe simplificar as soluções do lago de dados, tendo um único local onde as permissões são definidas em vez de ter que defini-las em vários lugares.

## <a name="trust-architecture"></a>Arquitetura de confiança

O diagrama de alto nível que se segue resume a arquitetura de solução de utilização da autenticação Azure AD com Synapse SQL. Para suportar a palavra-passe do utilizador nativo azure AD, apenas é considerada a porção Cloud e o Azure AD/Synapse Synapse Synapse SYnapse SQL. Para suportar a autenticação federada (ou utilizador/palavra-passe para credenciais do Windows), é necessária a comunicação com o bloco ADFS. As setas indicam vias de comunicação.

![aad auth diagrama](./media/aad-authentication/1-active-directory-authentication-diagram.png)

O diagrama seguinte indica a federação, a confiança e as relações de hospedagem que permitem a um cliente ligar-se a uma base de dados, enviando um símbolo. O símbolo é autenticado por um Anúncio Azure, e é confiável pela base de dados. 

O cliente 1 pode representar um Diretório Ativo Azure com utilizadores nativos ou um Azure AD com utilizadores federados. O Cliente 2 representa uma possível solução, incluindo utilizadores importados; neste exemplo vindo de um Diretório Ativo Azure federado com a ADFS a ser sincronizada com o Diretório Ativo Azure. 

É importante entender que o acesso a uma base de dados utilizando a autenticação Azure AD requer que a subscrição de hospedagem esteja associada ao Anúncio Azure. A mesma subscrição deve ser utilizada para criar o Servidor SQL que acolhe a Base de Dados Azure SQL ou o pool SQL.

![relação de subscrição](./media/aad-authentication/2-subscription-relationship.png)

## <a name="administrator-structure"></a>Estrutura de administrador

Ao utilizar a autenticação Azure AD, existem duas contas de Administrador para o Synapse SQL; o administrador original do SQL Server e o administrador da AD Azure. Apenas o administrador com base numa conta Azure AD pode criar o primeiro utilizador de base de dados Azure AD numa base de dados de utilizador. 

O login do administrador da AD Azure pode ser um utilizador de Anúncios Azure ou um grupo Azure AD. Quando o administrador é uma conta de grupo, pode ser usado por qualquer membro do grupo, permitindo vários administradores da AD Azure para a instância SQL Synapse. 

A utilização da conta de grupo como administrador aumenta a capacidade de gestão, permitindo-lhe adicionar e remover centralmente membros do grupo em Azure AD sem alterar os utilizadores ou permissões no espaço de trabalho Synapse Analytics. Apenas um administrador da AD Azure (utilizador ou grupo) pode ser configurado a qualquer momento.

![estrutura de administração](./media/aad-authentication/3-admin-structure.png)

## <a name="permissions"></a>Permissões

Para criar novos utilizadores, `ALTER ANY USER` tem de ter a permissão na base de dados. A `ALTER ANY USER` permissão pode ser concedida a qualquer utilizador de base de dados. A `ALTER ANY USER` permissão também é detida pelas contas do `CONTROL ON DATABASE` administrador `ALTER ON DATABASE` do servidor e pelos utilizadores `db_owner` da base de dados com a ou a permissão dessa base de dados, e por membros da função de base de dados.

Para criar um utilizador de base de dados contido no Synapse SQL, deve ligar-se à base de dados ou à instância utilizando uma identidade Azure AD. Para criar o primeiro utilizador de base de dados contido, deve ligar-se à base de dados utilizando um administrador da AD Azure (que é o proprietário da base de dados). 

Qualquer autenticação Azure AD só é possível se o administrador da AD Azure tiver sido criado para synapse SQL. Se o administrador do Azure Ative Directory foi removido do servidor, os atuais utilizadores do Azure Ative Directory criados anteriormente dentro da Synapse SQL já não podem ligar-se à base de dados utilizando as suas credenciais de Diretório Ativo Azure.
 
## <a name="azure-ad-features-and-limitations"></a>Funcionalidades e limitações da AD Azure

- Os seguintes membros da AD Azure podem ser provisionados em Synapse SQL:

  - Membros nativos: Um membro criado em Azure AD no domínio gerido ou num domínio de cliente. Para mais informações, consulte [Adicionar o seu próprio nome de domínio ao Azure AD](../../active-directory/fundamentals/add-custom-domain.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
  - Membros federados de domínio: Um membro criado em Azure AD com um domínio federado. Para mais informações, consulte o Microsoft Azure agora suporta a [federação com o Diretório Ativo do Windows Server](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/).
  - Membros importados de outros AD's azure que são membros nativos ou federados de domínio.
  - Grupos de Diretórios Ativos criados como grupos de segurança.

- Os utilizadores de AD Azure que `db_owner` fazem parte de um grupo que tem a função de servidor não podem usar a sintaxe **[CREDENTIAL SCOPED DA BASE](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)** DE DADOS CREATE CONTRA synapse SQL. Verá o seguinte erro:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Conceda `db_owner` o papel diretamente ao utilizador da AD Azure para mitigar a questão **CREDENTIAL** DE BASE DE DADOS CREATE DATABASE.

- Estas funções de sistema devolvem valores NULOs quando executados sob os principais da AD Azure:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

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
- Apenas um administrador da AD Azure (um utilizador ou grupo) pode ser configurado para piscina Synapse SQL a qualquer momento.
  - A adição de diretores de servidores Azure AD (logins) para SQL on-demand (pré-visualização) permite a possibilidade `sysadmin` de criar vários diretores de servidores Azure AD (logins) que podem ser adicionados à função.
- Apenas um administrador de AD Azure para synapse SQL pode inicialmente ligar-se ao Synapse SQL usando uma conta azure Ative Directory. O administrador de Diretório Ativo pode configurar os utilizadores subsequentes da base de dados Azure AD.
- Recomendamos que o tempo de ligação se ajuste a 30 segundos.
- SQL Server 2016 Management Studio e SQL Server Data Tools for Visual Studio 2015 (versão 14.0.60311.1abril 2016 ou mais tarde) suportam a autenticação do Diretório Ativo azure. (A autenticação Azure AD é suportada pelo Fornecedor de **Dados-Quadro .NET para SqlServer;** pelo menos versão .NET Framework 4.6). Por conseguinte, as versões mais recentes destas ferramentas e aplicações a nível de dados (DAC e . BACPAC) pode utilizar a autenticação Azure AD.
- Começando com a versão 15.0.1, [utilitário sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [suporte utilitário BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Ative Directory Interactive autenticação com MFA.
- SQL Server Data Tools for Visual Studio 2015 requer pelo menos a versão abril de 2016 das Ferramentas de Dados (versão 14.0.60311.1). Atualmente, os utilizadores de Anúncios Azure não são mostrados no SSDT Object Explorer. Como uma suposição, veja os utilizadores em [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).
- [O Microsoft JDBC Driver 6.0 para o SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) suporta a autenticação Azure AD. Consulte também [a definição das propriedades de ligação](/sql/connect/jdbc/setting-the-connection-properties?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral do acesso e controlo em Synapse SQL, consulte o controlo de [acesso Synapse SQL](../sql/access-control.md).
- Para obter mais informações sobre os principais de bases de dados, veja [Principals (Principais)](/sql/relational-databases/security/authentication-access/principals-database-engine?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).
- Para obter mais informações sobre as funções de base de dados, veja [Database roles (Funções de base de dados)](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

 