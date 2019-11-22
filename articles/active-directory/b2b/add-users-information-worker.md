---
title: Adicionar usuários de colaboração B2B como um operador de informações-Azure AD
description: A colaboração B2B permite que operadores de informações e proprietários de aplicativos adicionem usuários convidados ao Azure AD para acesso | Microsoft Docs
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37dc4a618f01022f4963a14aba921edcce9f4b60
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74268860"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>Como os usuários em sua organização podem convidar usuários convidados para um aplicativo

Depois que um usuário convidado tiver sido adicionado ao diretório no Azure AD, um proprietário de aplicativo poderá enviar ao usuário convidado um link direto para o aplicativo que deseja compartilhar. Os administradores do Azure AD também podem configurar o gerenciamento de autoatendimento para a galeria ou aplicativos baseados em SAML em seu locatário do Azure AD. Dessa forma, os proprietários do aplicativo podem gerenciar seus próprios usuários convidados, mesmo que os usuários convidados ainda não tenham sido adicionados ao diretório. Quando um aplicativo é configurado para autoatendimento, o proprietário do aplicativo usa seu painel de acesso para convidar um usuário convidado para um aplicativo ou adicionar um usuário convidado a um grupo que tem acesso ao aplicativo. O gerenciamento de aplicativo de autoatendimento para galeria e aplicativos baseados em SAML requer uma configuração inicial por um administrador. Veja a seguir um resumo das etapas de instalação (para obter instruções mais detalhadas, consulte [pré-requisitos](#prerequisites) mais adiante nesta página):

 - Habilitar o gerenciamento de grupo de autoatendimento para seu locatário
 - Criar um grupo para atribuir ao aplicativo e tornar o usuário um proprietário
 - Configurar o aplicativo para autoatendimento e atribuir o grupo ao aplicativo

> [!NOTE]
> Este artigo descreve como configurar o gerenciamento de autoatendimento para a galeria e aplicativos baseados em SAML que você adicionou ao seu locatário do Azure AD. Você também pode [configurar grupos de autoatendimento do office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) para que os usuários possam gerenciar o acesso aos seus próprios grupos do Office 365. Para obter mais maneiras como os usuários podem compartilhar arquivos e aplicativos do Office com usuários convidados, consulte [acesso de convidado em grupos do office 365](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) e [compartilhar arquivos ou pastas do SharePoint](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c).

## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>Convidar um usuário convidado para um aplicativo no painel de acesso

Depois que um aplicativo é configurado para autoatendimento, os proprietários do aplicativo podem usar seu próprio painel de acesso para convidar um usuário convidado para o aplicativo que deseja compartilhar. O usuário convidado não precisa necessariamente ser adicionado ao Azure AD com antecedência. 

1. Abra o painel de acesso acessando `https://myapps.microsoft.com`.
2. Aponte para o aplicativo, selecione as reticências ( **...** ) e, em seguida, selecione **gerenciar aplicativo**.
 
   ![Captura de tela mostrando o submenu gerenciar aplicativo para o aplicativo Salesforce](media/add-users-iw/access-panel-manage-app.png)
 
3. Na parte superior da lista de usuários, selecione **+** .
   
   ![Captura de tela mostrando o símbolo de adição para adicionar membros ao aplicativo](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. Na caixa de pesquisa **adicionar membros** , digite o endereço de email do usuário convidado. Também tem a opção de incluir uma mensagem de boas-vindas.
   
   ![Captura de tela mostrando a janela Adicionar membros para adicionar um convidado](media/add-users-iw/access-panel-invitation.png)
   
5. Selecione **Adicionar** para enviar um convite para o usuário convidado. Após enviar o convite, a conta do utilizador será automaticamente adicionada ao diretório como convidada.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>Convidar alguém para ingressar em um grupo que tenha acesso ao aplicativo
Depois que um aplicativo é configurado para autoatendimento, os proprietários do aplicativo podem convidar usuários convidados para os grupos que eles gerenciam que têm acesso aos aplicativos que desejam compartilhar. Os usuários convidados não precisam já existir no diretório. O proprietário do aplicativo segue estas etapas para convidar um usuário convidado para o grupo para que ele possa acessar o aplicativo.

1. Verifique se você é um proprietário do grupo de autoatendimento que tem acesso ao aplicativo que você deseja compartilhar.
2. Abra o painel de acesso acessando `https://myapps.microsoft.com`.
3. Selecione o aplicativo **grupos** .
   
   ![Captura de tela mostrando o aplicativo grupos no painel de acesso](media/add-users-iw/access-panel-groups.png)
   
4. Em **grupos**que possuo, selecione o grupo que tem acesso ao aplicativo que você deseja compartilhar.
   
   ![Captura de tela mostrando onde selecionar um grupo nos grupos dos quais sou proprietário](media/add-users-iw/access-panel-groups-i-own.png)
   
5. Na parte superior da lista de membros do grupo, selecione **+** .
   
   ![Captura de tela mostrando o símbolo de adição para adicionar membros ao grupo](media/add-users-iw/access-panel-groups-add-member.png)
   
6. Na caixa de pesquisa **adicionar membros** , digite o endereço de email do usuário convidado. Também tem a opção de incluir uma mensagem de boas-vindas.
   
   ![Captura de tela mostrando a janela Adicionar membros para adicionar um convidado](media/add-users-iw/access-panel-invitation.png)
   
7. Selecione **Adicionar** para enviar automaticamente o convite ao usuário convidado. Após enviar o convite, a conta do utilizador será automaticamente adicionada ao diretório como convidada.


## <a name="prerequisites"></a>Pré-requisitos

O gerenciamento de aplicativo de autoatendimento requer uma configuração inicial por um administrador global e um administrador do Azure AD. Como parte dessa configuração, você configurará o aplicativo para autoatendimento e atribuirá um grupo ao aplicativo que o proprietário do aplicativo pode gerenciar. Você também pode configurar o grupo para permitir que qualquer pessoa solicite a associação, mas que exija a aprovação de um proprietário de grupo. (Saiba mais sobre o [Gerenciamento de grupo de autoatendimento](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)). 

> [!NOTE]
> Você não pode adicionar usuários convidados a um grupo dinâmico ou a um grupo que é sincronizado com Active Directory locais.

### <a name="enable-self-service-group-management-for-your-tenant"></a>Habilitar o gerenciamento de grupo de autoatendimento para seu locatário
1. Entre no [portal do Azure](https://portal.azure.com) como um administrador global.
2. No painel de navegação, selecione **Azure Active Directory**.
3. Selecione **grupos**.
4. Em **configurações**, selecione **geral**.
5. Em **Gerenciamento de grupo de autoatendimento**, ao lado de **proprietários podem gerenciar solicitações de associação de grupo no painel de acesso**, selecione **Sim**.
6. Selecione **Guardar**.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>Criar um grupo para atribuir ao aplicativo e tornar o usuário um proprietário
1. Entre no [portal do Azure](https://portal.azure.com) como um administrador do Azure ad ou administrador global.
2. No painel de navegação, selecione **Azure Active Directory**.
3. Selecione **grupos**.
4. Selecione **novo grupo**.
5. Em **tipo de grupo**, selecione **segurança**.
6. Introduza um **Nome do grupo** e uma **Descrição do grupo**.
7. Em **tipo de associação**, selecione **atribuído**.
8. Selecione **criar**e feche a página **grupo** .
9. Na página **grupos – todos os grupos** , abra o grupo. 
10. Em **gerenciar**, selecione **proprietários** > **Adicionar proprietários**. Pesquise o usuário que deve gerenciar o acesso ao aplicativo. Selecione o usuário e clique em **selecionar**.

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>Configurar o aplicativo para autoatendimento e atribuir o grupo ao aplicativo
1. Entre no [portal do Azure](https://portal.azure.com) como um administrador do Azure ad ou administrador global.
2. No painel de navegação, selecione **Azure Active Directory**.
3. Em **gerenciar**, selecione **aplicativos empresariais** > **todos os aplicativos**.
4. Na lista de aplicativos, localize e abra o aplicativo.
5. Em **gerenciar**, selecione **logon único**e configure o aplicativo para logon único. (Para obter detalhes, consulte [como gerenciar o logon único para aplicativos empresariais](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal).)
6. Em **gerenciar**, selecione **autoatendimento**e configure o acesso do aplicativo de autoatendimento. (Para obter detalhes, consulte [como usar o acesso do aplicativo de autoatendimento](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).) 

    > [!NOTE]
    > Para a configuração **à qual os usuários atribuídos devem ser adicionados?** selecione o grupo que você criou na seção anterior.
7. Em **gerenciar**, selecione **usuários e grupos**e verifique se o grupo de autoatendimento criado aparece na lista.
8. Para adicionar o aplicativo ao painel de acesso do proprietário do grupo, selecione **Adicionar usuário** > **usuários e grupos**. Procure o proprietário do grupo e selecione o usuário, clique em **selecionar**e, em seguida, clique em **atribuir** para adicionar o usuário ao aplicativo.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos sobre a colaboração B2B do Azure AD:

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Como os administradores de Azure Active Directory adicionam usuários de colaboração B2B?](add-users-administrator.md)
- [Resgate de convite para colaboração B2B](redemption-experience.md)
- [Licenciamento de colaboração B2B do Azure AD](licensing-guidance.md)
