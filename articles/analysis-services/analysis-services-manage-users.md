---
title: Azure Analysis Services autenticação e permissões de usuário | Microsoft Docs
description: Este artigo descreve como o Azure Analysis Services usa o Azure Active Directory (AD do Azure) para gerenciamento de identidade e autenticação de usuário.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 4a054c3c042e18f1679acd75e5ba5ad74f66edff
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572752"
---
# <a name="authentication-and-user-permissions"></a>Autenticação e permissões de utilizador

O Azure Analysis Services usa Azure Active Directory (AD do Azure) para gerenciamento de identidade e autenticação de usuário. Qualquer usuário que cria, gerencia ou se conecta a um servidor de Azure Analysis Services deve ter uma identidade de usuário válida em um [locatário do Azure ad](../active-directory/fundamentals/active-directory-administer.md) na mesma assinatura.

O Azure Analysis Services dá suporte à [colaboração B2B do Azure ad](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md). Com o B2B, os usuários de fora de uma organização podem ser convidados como usuários convidados em um diretório do Azure AD. Convidados podem ser de outro diretório de locatário do Azure AD ou qualquer endereço de email válido. Depois de convidado e o usuário aceitar o convite enviado por email do Azure, a identidade do usuário será adicionada ao diretório do locatário. Essas identidades podem ser adicionadas a grupos de segurança ou como membros de um administrador de servidor ou função de banco de dados.

![Arquitetura de autenticação Azure Analysis Services](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Autenticação

Todos os aplicativos e ferramentas cliente usam uma ou mais das [bibliotecas de cliente](analysis-services-data-providers.md) Analysis Services (amo, MSOLAP, ADOMD) para se conectar a um servidor. 

Todas as três bibliotecas de cliente dão suporte ao fluxo interativo do Azure AD e aos métodos de autenticação não interativa. Os dois métodos não-interativos, Active Directory senha e Active Directory métodos de autenticação integrada podem ser usados em aplicativos que utilizam AMOMD e MSOLAP. Esses dois métodos nunca resultam em caixas de diálogo pop-up.

Aplicativos cliente como Excel e Power BI Desktop e ferramentas como o SSMS e Analysis Services extensão de projetos para o Visual Studio instalam as versões mais recentes das bibliotecas quando atualizados para a versão mais recente. A extensão de projetos Power BI Desktop, SSMS e Analysis Services são atualizadas mensalmente. O Excel é [atualizado com o Office 365](https://support.office.com/article/When-do-I-get-the-newest-features-in-Office-2016-for-Office-365-da36192c-58b9-4bc9-8d51-bb6eed468516). As atualizações do Office 365 são menos frequentes e algumas organizações usam o canal adiado, o que significa que as atualizações são adiadas até três meses.

Dependendo do aplicativo cliente ou da ferramenta que você usa, o tipo de autenticação e como você entra pode ser diferente. Cada aplicativo pode dar suporte a diferentes recursos para se conectar a serviços de nuvem como Azure Analysis Services.

Power BI Desktop, o Visual Studio e o SSMS dão suporte Active Directory autenticação universal, um método interativo que também dá suporte à MFA (autenticação multifator do Azure). O Azure MFA ajuda a proteger o acesso a dados e aplicativos ao mesmo tempo em que fornece um processo de entrada simples. Ele fornece autenticação forte com várias opções de verificação (chamada telefônica, mensagem de texto, cartões inteligentes com PIN ou notificação de aplicativo móvel). O MFA interativo com o Azure AD pode resultar em uma caixa de diálogo pop-up para validação. A **autenticação universal é recomendada**.

Se entrar no Azure usando uma conta do Windows e a autenticação universal não estiver selecionada ou disponível (Excel), [serviços de Federação do Active Directory (AD FS) (AD FS)](../active-directory/hybrid/how-to-connect-fed-azure-adfs.md) será necessária. Com a Federação, os usuários do Azure AD e do Office 365 são autenticados usando credenciais locais e podem acessar recursos do Azure.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

Os servidores de Azure Analysis Services dão suporte a conexões do [SSMS v 17.1](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e superior usando a autenticação do Windows, a autenticação de senha do Active Directory e a Active Directory autenticação universal. Em geral, é recomendável usar Active Directory autenticação universal porque:

*  Dá suporte a métodos de autenticação interativos e não interativos.

*  Dá suporte a usuários convidados do Azure B2B convidados no Azure como locatário. Ao se conectar a um servidor, os usuários convidados devem selecionar Active Directory autenticação universal ao se conectarem ao servidor.

*  Dá suporte à MFA (autenticação multifator). O Azure MFA ajuda a proteger o acesso a dados e aplicativos com uma variedade de opções de verificação: chamada telefônica, mensagem de texto, cartões inteligentes com PIN ou notificação de aplicativo móvel. O MFA interativo com o Azure AD pode resultar em uma caixa de diálogo pop-up para validação.

### <a name="visual-studio"></a>Visual Studio

O Visual Studio se conecta ao Azure Analysis Services usando a autenticação Active Directory universal com suporte a MFA. Os usuários são solicitados a entrar no Azure na primeira implantação. Os usuários devem entrar no Azure com uma conta com permissões de administrador do servidor no servidor no qual estão implantando. Ao entrar no Azure pela primeira vez, um token é atribuído. O token é armazenado em cache na memória para reconexões futuras.

### <a name="power-bi-desktop"></a>Power BI Desktop

Power BI Desktop se conecta a Azure Analysis Services usando Active Directory autenticação universal com suporte a MFA. Os usuários são solicitados a entrar no Azure na primeira conexão. Os usuários devem entrar no Azure com uma conta que esteja incluída em um administrador de servidor ou função de banco de dados.

### <a name="excel"></a>Excel

Os usuários do Excel podem se conectar a um servidor usando uma conta do Windows, uma ID da organização (endereço de email) ou um endereço de email externo. Identidades de email externas devem existir no Azure AD como um usuário convidado.

## <a name="user-permissions"></a>Permissões de utilizador

**Os administradores de servidor** são específicos de uma instância de servidor Azure Analysis Services. Eles se conectam com ferramentas como portal do Azure, SSMS e Visual Studio para executar tarefas como adicionar bancos de dados e gerenciar funções de usuário. Por padrão, o usuário que cria o servidor é adicionado automaticamente como um administrador de servidor Analysis Services. Outros administradores podem ser adicionados usando portal do Azure ou SSMS. Os administradores de servidor devem ter uma conta no locatário do Azure AD na mesma assinatura. Para saber mais, consulte [gerenciar administradores de servidor](analysis-services-server-admins.md). 

**Os usuários de banco de dados** se conectam a bancos de dados de modelo usando aplicativos cliente como Excel ou Power bi. Os usuários devem ser adicionados às funções de banco de dados. As funções de banco de dados definem permissões de administrador, processo ou leitura para um banco de dados. É importante entender que os usuários de banco de dados em uma função com permissões de administrador são diferentes dos administradores de servidor. No entanto, por padrão, os administradores de servidor também são administradores de banco de dados. Para saber mais, consulte [gerenciar usuários e funções de banco de dados](analysis-services-database-users.md).

**Proprietários de recursos do Azure**. Os proprietários de recursos gerenciam recursos para uma assinatura do Azure. Os proprietários de recursos podem adicionar identidades de usuário do Azure AD a funções de proprietário ou colaborador em uma assinatura usando o **controle de acesso** no portal do Azure ou com modelos de Azure Resource Manager. 

![Controle de acesso no portal do Azure](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

As funções nesse nível se aplicam a usuários ou contas que precisam executar tarefas que podem ser concluídas no portal ou usando modelos de Azure Resource Manager. Para saber mais, consulte [controle de acesso baseado em função](../role-based-access-control/overview.md). 

## <a name="database-roles"></a>Funções de banco de dados

 As funções definidas para um modelo de tabela são funções de banco de dados. Ou seja, as funções contêm membros que consistem em usuários e grupos de segurança do Azure AD que têm permissões específicas que definem a ação que esses membros podem executar em um banco de dados modelo. Uma função de base de dados é criada como um objeto separado na base de dados e aplica-se apenas à base de dados na qual essa função é criada.   
  
 Por padrão, quando você cria um novo projeto de modelo de tabela, o projeto de modelo não tem nenhuma função. As funções podem ser definidas usando a caixa de diálogo Gerenciador de funções no Visual Studio. Quando as funções são definidas durante o design do projeto de modelo, elas são aplicadas somente ao banco de dados de espaço de trabalho modelo. Quando o modelo é implantado, as mesmas funções são aplicadas ao modelo implantado. Depois que um modelo foi implantado, os administradores de servidor e de banco de dados podem gerenciar funções e membros usando o SSMS. Para saber mais, consulte [gerenciar usuários e funções de banco de dados](analysis-services-database-users.md).
  
## <a name="next-steps"></a>Passos seguintes

[Gerenciar o acesso a recursos com grupos de Azure Active Directory](../active-directory/fundamentals/active-directory-manage-groups.md)   
[Gerenciar usuários e funções de banco de dados](analysis-services-database-users.md)  
[Gerir administradores de servidor](analysis-services-server-admins.md)  
[Controlo de Acesso Baseado em Funções](../role-based-access-control/overview.md)  