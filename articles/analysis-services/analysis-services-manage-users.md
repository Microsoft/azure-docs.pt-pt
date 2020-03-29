---
title: Autenticação dos Serviços de Análise Azure e permissões dos utilizadores Microsoft Docs
description: Este artigo descreve como os Serviços de Análise Azure utilizam o Azure Ative Directory (Azure AD) para gestão de identidade e autenticação do utilizador.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 4a054c3c042e18f1679acd75e5ba5ad74f66edff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572752"
---
# <a name="authentication-and-user-permissions"></a>Autenticação e permissões de utilizador

Os Serviços de Análise Azure utilizam o Azure Ative Directory (Azure AD) para gestão de identidade e autenticação do utilizador. Qualquer utilizador que crie, gere ou se ligue a um servidor dos Serviços de Análise Azure deve ter uma identidade de utilizador válida num [inquilino da AD Azure](../active-directory/fundamentals/active-directory-administer.md) na mesma subscrição.

Os Serviços de Análise Azure apoiam a [colaboração Azure AD B2B.](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) Com o B2B, os utilizadores de fora de uma organização podem ser convidados como utilizadores convidados num diretório Azure AD. Os hóspedes podem ser de outro diretório de inquilinos DaD Azure ou qualquer endereço de e-mail válido. Uma vez convidado e o utilizador aceita o convite enviado por e-mail do Azure, a identidade do utilizador é adicionada ao diretório do inquilino. Essas identidades podem ser adicionadas a grupos de segurança ou como membros de um administrador de servidor ou papel de base de dados.

![Arquitetura de autenticação de serviços de análise azure](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Autenticação

Todas as aplicações e ferramentas do cliente utilizam uma ou mais [bibliotecas](analysis-services-data-providers.md) de clientes dos Serviços de Análise (AMO, MSOLAP, ADOMD) para se conectarem a um servidor. 

As três bibliotecas de clientes suportam tanto o fluxo interativo azure AD como os métodos de autenticação não interativos. Os dois métodos não interativos, palavra-passe de diretório ativo e métodos de autenticação integrada de diretório ativo podem ser utilizados em aplicações que utilizam a AMOMD e a MSOLAP. Estes dois métodos nunca resultam em caixas de diálogo pop-up.

Aplicações de clientes como Excel e Power BI Desktop, e ferramentas como a extensão de projetos sSMS e Serviços de Análise para O Estúdio Visual instalam as versões mais recentes das bibliotecas quando atualizadas para o mais recente lançamento. A extensão dos projetos de power BI Desktop, SSMS e Serviços de Análise são atualizadas mensalmente. O Excel é [atualizado com o Office 365](https://support.office.com/article/When-do-I-get-the-newest-features-in-Office-2016-for-Office-365-da36192c-58b9-4bc9-8d51-bb6eed468516). As atualizações do Office 365 são menos frequentes, e algumas organizações usam o canal diferido, o que significa que as atualizações são adiadas até três meses.

Dependendo da aplicação ou ferramenta do cliente que utiliza, o tipo de autenticação e a forma como se inscreveu pode ser diferente. Cada aplicação pode suportar diferentes funcionalidades para a ligação a serviços de cloud como o Azure Analysis Services.

Power BI Desktop, Visual Studio e SSMS suportam autenticação universal de diretório ativo, um método interativo que também suporta a autenticação de multi-factores Azure (MFA). O Azure MFA ajuda a salvaguardar o acesso a dados e aplicações, ao mesmo tempo que fornece um processo simples de entrada. Oferece autenticação forte com várias opções de verificação (chamada telefónica, mensagem de texto, cartões inteligentes com pin ou notificação de aplicação móvel). O MFA interativo com o Azure AD pode resultar numa caixa de diálogo pop-up para validação. **Recomenda-se a autenticação universal.**

Se iniciar sessão no Azure utilizando uma conta Windows, e a Autenticação Universal não estiver selecionada ou disponível (Excel), é necessário um Serviço de Federação de [Diretórios Ativos (AD FS).](../active-directory/hybrid/how-to-connect-fed-azure-adfs.md) Com a Federação, a AD Azure e os 365 utilizadores do Office são autenticados usando credenciais no local e podem aceder aos recursos do Azure.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

Os servidores dos Serviços de Análise Azure suportam ligações a partir de [SSMS V17.1](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e superiores utilizando a autenticação do Windows, a autenticação de palavra-passe de diretório ativo e a autenticação universal do Diretório Ativo. Em geral, recomenda-se que utilize a autenticação universal do Diretório Ativo porque:

*  Suporta métodos de autenticação interativos e não interativos.

*  Suporta os utilizadores convidados do Azure B2B convidados para o inquilino Azure AS. Ao ligar-se a um servidor, os utilizadores dos hóspedes devem selecionar a Autenticação Universal do Diretório Ativo ao ligarem-se ao servidor.

*  Suporta a autenticação multi-factor (MFA). O Azure MFA ajuda a salvaguardar o acesso a dados e aplicações com um leque de opções de verificação: chamada telefónica, mensagem de texto, cartões inteligentes com pin ou notificação de aplicações móveis. O MFA interativo com o Azure AD pode resultar numa caixa de diálogo pop-up para validação.

### <a name="visual-studio"></a>Visual Studio

O Visual Studio conecta-se aos Serviços de Análise Azure utilizando a Autenticação Universal do Diretório Ativo com suporte ao MFA. Os utilizadores são solicitados a iniciar sessão no Azure na primeira implementação. Os utilizadores devem iniciar sessão no Azure com uma conta com permissões de administrador de servidor no servidor para o qual estão a ser implementadas. Ao assinar no Azure pela primeira vez, é atribuído um símbolo. O símbolo está em cache na memória para futuras religações.

### <a name="power-bi-desktop"></a>Power BI Desktop

Power BI Desktop conecta-se aos Serviços de Análise Azure utilizando a autenticação universal do Diretório Ativo com suporte mFA. Os utilizadores são solicitados a iniciar sessão no Azure na primeira ligação. Os utilizadores devem iniciar sessão no Azure com uma conta que esteja incluída num administrador de servidor ou numa função de base de dados.

### <a name="excel"></a>Excel

Os utilizadores do Excel podem ligar-se a um servidor utilizando uma conta Windows, um ID da organização (endereço de e-mail) ou um endereço de e-mail externo. As identidades de e-mail externas devem existir no Anúncio De Azure como utilizador convidado.

## <a name="user-permissions"></a>Permissões de utilizador

**Os administradores do servidor** são específicos de uma instância de servidor dos Serviços de Análise Azure. Conectam-se com ferramentas como o portal Azure, SSMS e Visual Studio para executar tarefas como adicionar bases de dados e gerir as funções dos utilizadores. Por predefinição, o utilizador que cria o servidor é adicionado automaticamente como administrador de servidor de Serviços de Análise. Outros administradores podem ser adicionados utilizando o portal Azure ou sSMS. Os administradores do servidor devem ter uma conta no inquilino DaD Azure na mesma subscrição. Para saber mais, consulte [Gerir administradores de servidores](analysis-services-server-admins.md). 

**Os utilizadores de bases** de dados ligam-se a bases de dados de modelos utilizando aplicações de clientes como o Excel ou o Power BI. Os utilizadores devem ser adicionados às funções de base de dados. As funções de base de dados definem o administrador, o processo ou a leitura de permissões para uma base de dados. É importante entender que os utilizadores de bases de dados numa função com permissões de administrador é diferente dos administradores do servidor. No entanto, por padrão, os administradores do servidor também são administradores de bases de dados. Para saber mais, consulte [Gerir as funções de base](analysis-services-database-users.md)de dados e os utilizadores.

**Proprietários de recursos Azure.** Os proprietários de recursos gerem recursos para uma subscrição do Azure. Os proprietários de recursos podem adicionar identidades de utilizador azure AD a Funções de Proprietário ou Colaborador dentro de uma subscrição utilizando **o controlo** de acesso no portal Azure, ou com modelos de Gestor de Recursos Azure. 

![Controlo de acesso no portal Azure](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

As funções a este nível aplicam-se aos utilizadores ou contas que precisam de executar tarefas que podem ser concluídas no portal ou utilizando modelos do Gestor de Recursos Do Azure. Para saber mais, consulte [o Controlo de Acesso baseado em Papéis.](../role-based-access-control/overview.md) 

## <a name="database-roles"></a>Funções de base de dados

 As funções definidas para um modelo tabular são funções de base de dados. Ou seja, as funções contêm membros constituídos por utilizadores de Anúncios Azure e grupos de segurança que têm permissões específicas que definem a ação que esses membros podem assumir numa base de dados de modelos. Uma função de base de dados é criada como um objeto separado na base de dados e aplica-se apenas à base de dados na qual essa função é criada.   
  
 Por padrão, quando se cria um novo modelo tabular, o projeto modelo não tem qualquer função. As funções podem ser definidas utilizando a caixa de diálogo Role Manager no Estúdio Visual. Quando as funções são definidas durante o design do projeto de modelo, são aplicadas apenas à base de dados do espaço de trabalho do modelo. Quando o modelo é implantado, as mesmas funções são aplicadas ao modelo implantado. Depois de um modelo ter sido implementado, os administradores de servidores e bases de dados podem gerir funções e membros utilizando o SSMS. Para saber mais, consulte [Gerir as funções de base](analysis-services-database-users.md)de dados e os utilizadores.
  
## <a name="next-steps"></a>Passos seguintes

[Gerir o acesso aos recursos com grupos de Diretórios Ativos Azure](../active-directory/fundamentals/active-directory-manage-groups.md)   
[Gerir funções de base de dados e utilizadores](analysis-services-database-users.md)  
[Gerir administradores de servidor](analysis-services-server-admins.md)  
[Controlo de Acesso baseado em papéis](../role-based-access-control/overview.md)  