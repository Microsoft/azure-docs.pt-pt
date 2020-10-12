---
title: Autenticação dos Serviços de Análise Azure e permissões do utilizador. Microsoft Docs
description: Este artigo descreve como a Azure Analysis Services utiliza o Azure Ative Directory (Azure AD) para gestão de identidade e autenticação do utilizador.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c44ac820349973240328fbb92dea14668b019a12
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400796"
---
# <a name="authentication-and-user-permissions"></a>Autenticação e permissões de utilizador

A Azure Analysis Services utiliza o Azure Ative Directory (Azure AD) para gestão de identidade e autenticação do utilizador. Qualquer utilizador que crie, gere ou se conecte a um servidor Azure Analysis Services deve ter uma identidade de utilizador válida num [inquilino AD Azure](../active-directory/fundamentals/active-directory-administer.md) na mesma subscrição.

A Azure Analysis Services suporta [a colaboração Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md). Com o B2B, os utilizadores de fora de uma organização podem ser convidados como utilizadores convidados num diretório AD Azure. Os hóspedes podem ser de outro diretório de inquilinos da Azure AD ou qualquer endereço de e-mail válido. Uma vez convidado e o utilizador aceita o convite enviado por e-mail da Azure, a identidade do utilizador é adicionada ao diretório do inquilino. Essas identidades podem ser adicionadas a grupos de segurança ou como membros de um administrador de servidor ou papel de base de dados.

![Arquitetura de autenticação de Serviços de Análise Azure](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Autenticação

Todas as aplicações e ferramentas do cliente utilizam uma ou mais bibliotecas de clientes dos [Serviços](https://docs.microsoft.com/analysis-services/client-libraries?view=azure-analysis-services-current) de Análise (AMO, MSOLAP, ADOMD) para se conectarem a um servidor. 

As três bibliotecas de clientes suportam tanto o fluxo interativo AZure AD como os métodos de autenticação não interativos. Os dois métodos não interativos, Password de Diretório Ativo e Métodos de Autenticação Integrado de Diretório Ativo podem ser utilizados em aplicações que usassem AMOMD e MSOLAP. Estes dois métodos nunca resultam em caixas de diálogo pop-up.

Aplicações de clientes como Excel e Power BI Desktop, e ferramentas como SSMS e Analysis Services projetam extensão de projetos para Visual Studio instalar as versões mais recentes das bibliotecas quando atualizadas para o lançamento mais recente. A extensão dos projetos de power BI Desktop, SSMS e Analysis Services são atualizadas mensalmente. O Excel é [atualizado com o Microsoft 365](https://support.microsoft.com/office/when-do-i-get-the-newest-features-for-microsoft-365-da36192c-58b9-4bc9-8d51-bb6eed468516). As atualizações da Microsoft 365 são menos frequentes e algumas organizações usam o canal diferido, o que significa que as atualizações são adiadas até três meses.

Dependendo da aplicação ou ferramenta do cliente que utiliza, o tipo de autenticação e a forma como se inscreve pode ser diferente. Cada aplicação pode suportar diferentes funcionalidades para a ligação a serviços na nuvem como os Azure Analysis Services.

Power BI Desktop, Visual Studio e SSMS suportam autenticação universal do Diretório Ativo, um método interativo que também suporta a autenticação multi-factor Azure (MFA). O Azure MFA ajuda a salvaguardar o acesso a dados e aplicações, ao mesmo tempo que fornece um processo simples de inscrição. Oferece autenticação forte com várias opções de verificação (chamada telefónica, mensagem de texto, cartões inteligentes com pin ou notificação de aplicações móveis). O MFA interativo com Azure AD pode resultar numa caixa de diálogo pop-up para validação. **Recomenda-se a autenticação universal.**

Se iniciar sessão no Azure utilizando uma conta Windows e a Autenticação Universal não for selecionada ou disponível (Excel), são [necessários Serviços da Federação de Diretórios Ativos (AD FS).](../active-directory/hybrid/how-to-connect-fed-azure-adfs.md) Com a Federação, os utilizadores do Azure AD e da Microsoft 365 são autenticados utilizando credenciais no local e podem aceder aos recursos do Azure.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

Os servidores dos Serviços de Análise Azure suportam ligações a partir de [SSMS V17.1](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e superior através da utilização de Autenticação do Windows, Autenticação de Passwords de Diretório Ativo e Autenticação Universal do Diretório Ativo. Em geral, recomenda-se que utilize a Autenticação Universal do Diretório Ativo porque:

*  Suporta métodos de autenticação interativa e não interativo.

*  Suporta utilizadores convidados Azure B2B convidados para o inquilino Azure AS. Ao ligar-se a um servidor, os utilizadores convidados devem selecionar a Autenticação Universal do Diretório Ativo ao ligarem-se ao servidor.

*  Suporta a autenticação multi-factor (MFA). O Azure MFA ajuda a salvaguardar o acesso a dados e aplicações com um leque de opções de verificação: chamada telefónica, mensagem de texto, cartões inteligentes com pin ou notificação de aplicações móveis. O MFA interativo com Azure AD pode resultar numa caixa de diálogo pop-up para validação.

### <a name="visual-studio"></a>Visual Studio

O Visual Studio conecta-se aos Serviços de Análise Azure utilizando a Autenticação Universal do Diretório Ativo com suporte AFA. Os utilizadores são solicitados a iniciar súm na Azure na primeira implementação. Os utilizadores devem iniciar scontabilidade no Azure com uma conta com permissões de administrador de servidores no servidor para onde estão a implementar. Ao iniciar sessão no Azure pela primeira vez, é atribuído um sinal. O token está em cache na memória para futuras reconectações.

### <a name="power-bi-desktop"></a>Power BI Desktop

Power BI Desktop conecta-se aos Serviços de Análise Azure utilizando a Autenticação Universal do Diretório Ativo com suporte MFA. Os utilizadores são solicitados a iniciar súm na Azure na primeira ligação. Os utilizadores devem iniciar súpido no Azure com uma conta incluída num administrador de servidor ou numa função de base de dados.

### <a name="excel"></a>Excel

Os utilizadores do Excel podem ligar-se a um servidor utilizando uma conta Windows, um ID de organização (endereço de e-mail) ou um endereço de e-mail externo. As identidades de e-mail externas devem existir no AZure AD como utilizador convidado.

## <a name="user-permissions"></a>Permissões de utilizador

**Os administradores do servidor** são específicos de uma instância do servidor Azure Analysis Services. Conectam-se com ferramentas como o portal Azure, SSMS e Visual Studio para executar tarefas como adicionar bases de dados e gerir funções de utilizador. Por predefinição, o utilizador que cria o servidor é automaticamente adicionado como administrador de servidores de Serviços de Análise. Outros administradores podem ser adicionados utilizando o portal Azure ou sSMS. Os administradores do servidor devem ter uma conta no inquilino AZure AD na mesma subscrição. Para saber mais, consulte [Gerir os administradores do servidor](analysis-services-server-admins.md). 

**Os utilizadores de bases de dados** conectam-se a bases de dados de modelos utilizando aplicações de clientes como o Excel ou o Power BI. Os utilizadores devem ser adicionados às funções de base de dados. As funções de base de dados definem permissões de administrador, processo ou leitura para uma base de dados. É importante entender que os utilizadores de bases de dados numa função com permissões de administrador é diferente dos administradores de servidores. No entanto, por padrão, os administradores de servidores também são administradores de bases de dados. Para saber mais, consulte [Gerir as funções de base de dados e os utilizadores.](analysis-services-database-users.md)

**Proprietários de recursos Azure.** Os proprietários de recursos gerem recursos para uma subscrição do Azure. Os proprietários de recursos podem adicionar identidades de utilizador Azure AD a Funções De Proprietário ou Contribuinte dentro de uma subscrição, utilizando **o controlo de acesso** no portal Azure, ou com modelos de Gestor de Recursos Azure. 

![Controlo de acessos no portal Azure](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

As funções a este nível aplicam-se aos utilizadores ou contas que precisam de executar tarefas que possam ser concluídas no portal ou utilizando modelos de Gestor de Recursos Azure. Para saber mais, consulte [o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md). 

## <a name="database-roles"></a>Funções de base de dados

 As funções definidas para um modelo tabular são funções de base de dados. Ou seja, as funções contêm membros constituídos por utilizadores AZure AD e grupos de segurança que têm permissões específicas que definem a ação que esses membros podem tomar numa base de dados modelo. Uma função de base de dados é criada como um objeto separado na base de dados e aplica-se apenas à base de dados na qual essa função é criada.   
  
 Por padrão, quando cria um novo projeto de modelo tabular, o projeto modelo não tem quaisquer funções. As funções podem ser definidas utilizando a caixa de diálogo Role Manager no Estúdio Visual. Quando as funções são definidas durante o design do projeto do modelo, são aplicadas apenas à base de dados do espaço de trabalho do modelo. Quando o modelo é implantado, as mesmas funções são aplicadas ao modelo implantado. Depois de implementado um modelo, os administradores de servidores e de bases de dados podem gerir funções e membros utilizando SSMS. Para saber mais, consulte [Gerir as funções de base de dados e os utilizadores.](analysis-services-database-users.md)
  
## <a name="next-steps"></a>Passos seguintes

[Gerir o acesso a recursos com grupos Azure Ative Directory](../active-directory/fundamentals/active-directory-manage-groups.md)   
[Gerir funções de base de dados e utilizadores](analysis-services-database-users.md)  
[Gerir administradores de servidor](analysis-services-server-admins.md)  
[Controlo de acesso baseado em funções do Azure (RBAC do Azure)](../role-based-access-control/overview.md)  