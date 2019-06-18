---
title: Autenticação e permissões de utilizador no Azure Analysis Services | Documentos da Microsoft
description: Saiba mais sobre as permissões de utilizador e autenticação no Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7673b115a3ad2e6ca7aec34b1cfabfb38d2a16f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60859214"
---
# <a name="authentication-and-user-permissions"></a>Autenticação e permissões de utilizador

O Azure Analysis Services utiliza o Azure Active Directory (Azure AD) para autenticação de utilizador e gestão de identidade. Qualquer usuário criar, gerir ou ligar a um Azure Analysis Services server tem de ter uma identidade de utilizador válido [inquilino do Azure AD](../active-directory/fundamentals/active-directory-administer.md) na mesma subscrição.

O Azure Analysis Services suporta [colaboração B2B do Azure AD](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md). Com o B2B, os utilizadores fora da organização podem ser convidados como os utilizadores convidados num diretório do Azure AD. Os convidados podem ser de outro diretório de inquilino do Azure AD ou de qualquer endereço de e-mail válido. Uma vez convidado e o utilizador aceitar o convite enviado por e-mail do Azure, a identidade do utilizador é adicionada ao diretório do inquilino. Essas identidades podem ser adicionadas a grupos de segurança ou como membros de uma função de administrador ou a base de dados do servidor.

![Arquitetura de autenticação do Azure Analysis Services](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Autenticação

Todas as aplicações de cliente e ferramentas de utilizam um ou mais dos serviços de análise [bibliotecas de cliente](analysis-services-data-providers.md) (AMO, MSOLAP, ADOMD) para ligar a um servidor. 

Todas as três bibliotecas de cliente suportam o fluxo de interativo do Azure AD e métodos de autenticação não interativa. Os dois métodos não interativa, palavra-passe de diretório Active Directory e métodos de autenticação de integrada do Active Directory podem ser usados em aplicativos utilizando AMOMD e MSOLAP. Esses dois métodos nunca resultam em caixas de diálogo pop-up.

Aplicações de cliente, como Excel e no Power BI Desktop e instalam as versões mais recentes das bibliotecas quando atualizado para a versão mais recente, ferramentas como o SSMS e o SSDT. Power BI Desktop, o SSMS e o SSDT são atualizada mensalmente. É de Excel [atualizado com o Office 365](https://support.office.com/article/When-do-I-get-the-newest-features-in-Office-2016-for-Office-365-da36192c-58b9-4bc9-8d51-bb6eed468516). Atualizações do Office 365 são menos freqüentes e algumas organizações utilizam o canal diferido, atualizações de significado diferimento até três meses.

Consoante a aplicação cliente ou a ferramenta que utilizar, o tipo de autenticação e como iniciar sessão pode ser diferente. Cada aplicativo pode suportar diferentes funcionalidades para ligar a serviços cloud como o Azure Analysis Services.

Power BI Desktop, o SSDT e o SSMS suporta a autenticação do Active Directory Universal, um método interativo que também oferece suporte a Azure multi-factor Authentication (MFA). Azure MFA ajuda a salvaguardar o acesso a dados e aplicações, fornecendo um processo de início de sessão simples. Proporciona uma autenticação segura com várias opções de verificação (chamada telefónica, mensagem de texto, smart cards com pin ou notificação de aplicação móvel). MFA interativa com o Azure AD pode resultar numa caixa de diálogo pop-up para validação. **Recomenda-se a autenticação universal**.

Se iniciar sessão no Azure utilizando uma conta do Windows e autenticação Universal não está selecionado ou de disponível (Excel), [serviços de Federação do Active Directory (AD FS)](../active-directory/hybrid/how-to-connect-fed-azure-adfs.md) é necessária. Com a Federação, do Azure AD e utilizadores do Office 365 são autenticados com credenciais no local e podem aceder aos recursos do Azure.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

Servidores do Analysis Services do Azure suportam ligações a partir [SSMS V17.1](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e superior utilizando a autenticação do Windows, autenticação de palavra-passe do Active Directory e Active Directory Universal Authentication. Em geral, recomenda-se que usar o Active Directory Universal Authentication porque:

*  Oferece suporte a métodos de autenticação não interativa e interativos.

*  Oferece suporte a usuários de convidado do Azure B2B convidados para o inquilino do Azure AS. Ao ligar a um servidor, os utilizadores convidados tem de selecionar Active Directory Universal Authentication quando ligar ao servidor.

*  Suporta autenticação Multifator (MFA). Azure MFA ajuda a salvaguardar o acesso a dados e aplicações com uma variedade de opções de verificação: chamada telefónica, mensagem de texto, smart cards com pin ou notificação de aplicação móvel. MFA interativa com o Azure AD pode resultar numa caixa de diálogo pop-up para validação.

### <a name="sql-server-data-tools-ssdt"></a>SQL Server Data Tools (SSDT)

SSDT liga-se ao Azure Analysis Services com o Active Directory Universal Authentication com suporte MFA. É pedido aos utilizadores iniciar sessão para o Azure, na primeira implementação. Os utilizadores devem iniciar sessão no Azure com uma conta com permissões de administrador do servidor no servidor que estão a implementar. Quando iniciar sessão Azure pela primeira vez, é atribuído um token. SSDT armazena em cache o token na memória para a reconexão do futuro.

### <a name="power-bi-desktop"></a>Power BI Desktop

Power BI Desktop liga-se para utilizar o Active Directory Universal Authentication com suporte MFA do Azure Analysis Services. É pedido aos utilizadores iniciar sessão para o Azure, na primeira ligação. Os utilizadores devem iniciar sessão no Azure com uma conta que está incluída num administrador do servidor ou a função de base de dados.

### <a name="excel"></a>Excel

Os usuários do Excel, podem ligar a um servidor com uma conta do Windows, um ID de organização (endereço de e-mail) ou um endereço de e-mail externo. Identidades de e-mail externo tem de existir no Azure AD como um utilizador convidado.

## <a name="user-permissions"></a>Permissões de utilizador

**Os administradores de servidor** são específicas para uma instância de servidor Azure Analysis Services. Se ligam-se com ferramentas como o portal do Azure, o SSMS e o SSDT para realizar tarefas como adicionar bases de dados e gerir funções de utilizador. Por predefinição, o utilizador que cria o servidor é automaticamente adicionado como um administrador de servidor do Analysis Services. Podem ser adicionados outros administradores com o portal do Azure ou o SSMS. Os administradores de servidor tem de ter uma conta no inquilino do Azure AD na mesma subscrição. Para obter mais informações, consulte [gerir administradores de servidor](analysis-services-server-admins.md). 

**Os utilizadores de base de dados** ligar a bases de dados do modelo com aplicações de cliente, como o Excel ou Power BI. Os utilizadores devem ser adicionados às funções de base de dados. Funções de base de dados definem o administrador, processo ou as permissões de leitura para uma base de dados. É importante compreender os utilizadores de base de dados numa função com permissões de administrador é diferente do que os administradores de servidor. No entanto, por predefinição, os administradores de servidor também são administradores de banco de dados. Para obter mais informações, consulte [gerir funções de base de dados e os utilizadores](analysis-services-database-users.md).

**Os proprietários de recursos do Azure**. Os proprietários de recursos gerir os recursos de uma subscrição do Azure. Os proprietários de recursos podem adicionar as identidades de utilizador do Azure AD para proprietário ou Contribuidor funções dentro de uma subscrição, utilizando **controlo de acesso** no portal do Azure ou com modelos Azure Resource Manager. 

![Controlo de acesso no portal do Azure](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Funções nesse nível aplicam-se a utilizadores ou contas que têm de executar tarefas que podem ser concluídas no portal ou através de modelos Azure Resource Manager. Para obter mais informações, consulte [controlo de acesso baseado em funções](../role-based-access-control/overview.md). 

## <a name="database-roles"></a>Funções de base de dados

 Funções definidas para um modelo de tabela são funções de base de dados. Ou seja, as funções contêm membros que consiste de utilizadores do Azure AD e os grupos de segurança que tenham permissões específicas que definem a ação esses membros podem ter uma base de dados do modelo. Uma função de base de dados é criada como um objeto separado na base de dados e aplica-se apenas à base de dados na qual essa função é criada.   
  
 Por predefinição, quando cria um novo projeto de modelo em tabela, o projeto de modelo não tem quaisquer funções. As funções podem ser definidas utilizando a caixa de diálogo do Gestor de funções no SSDT. Quando as funções são definidas durante o design de projeto do modelo, estas são aplicadas apenas a base de dados da área de trabalho do modelo. Quando o modelo é implementado, as mesmas funções são aplicadas para o modelo implementado. Depois de ter sido implementado um modelo, os administradores de banco de dados de servidores e podem gerir funções e membros com o SSMS. Para obter mais informações, consulte [gerir funções de base de dados e os utilizadores](analysis-services-database-users.md).
  
## <a name="next-steps"></a>Passos Seguintes

[Gerir o acesso a recursos com grupos do Active Directory do Azure](../active-directory/fundamentals/active-directory-manage-groups.md)   
[Gerir funções de base de dados e utilizadores](analysis-services-database-users.md)  
[Gerir administradores de servidor](analysis-services-server-admins.md)  
[Controlo de Acesso Baseado em Funções](../role-based-access-control/overview.md)  