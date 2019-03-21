---
title: Adicionar utilizadores de colaboração B2B como um operador de informações - Azure Active Directory | Documentos da Microsoft
description: Colaboração B2B permite que os operadores de informações e os proprietários da aplicação adicionar utilizadores convidados para o Azure AD para acesso | Documentos da Microsoft
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 561d7f5b28775e5f431d35d956cff6f800cc11b2
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294523"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>Como os utilizadores na sua organização podem convidar utilizadores convidados para uma aplicação

Depois de um convidado utilizador foi adicionado para o diretório no Azure AD, um proprietário da aplicação pode enviar o utilizador convidado uma ligação direta para a aplicação que pretendem partilhar. Os administradores do Azure AD também podem configurar gestão self-service para aplicações baseadas em SAML no seu inquilino do Azure AD ou galeria. Desta forma, os proprietários do aplicativo podem gerir seus próprios utilizadores convidados, mesmo que os utilizadores convidados ainda não foi ainda adicionados para o diretório. Quando uma aplicação está configurada para Self-Service, o proprietário da aplicação utiliza o seu painel de acesso para convidar um utilizador convidado a uma aplicação ou adicionar um utilizador convidado a um grupo que tenha acesso à aplicação. Gestão de aplicações self-service para Galeria e aplicações baseadas em SAML requer alguma configuração inicial, por um administrador. Segue-se um resumo dos passos de configuração (para obter instruções mais detalhadas, consulte [pré-requisitos](#prerequisites) posteriormente nesta página):

 - Ativar a gestão de grupos self-service para o seu inquilino
 - Criar um grupo para atribuir à aplicação e tornar o utilizador proprietário
 - Configurar a aplicação para gestão personalizada e atribua o grupo para a aplicação

> [!NOTE]
> Este artigo descreve como configurar a gestão de self-service para a Galeria e aplicativos baseados em SAML que adicionou ao seu inquilino do Azure AD. Também pode [configure os grupos do Office 365 de Self-serviços](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) para que os utilizadores podem gerir o acesso aos seus próprios grupos do Office 365. Para mais utilizadores de formas podem partilhar aplicações e ficheiros do Office com utilizadores convidados, consulte [acesso de convidado em grupos do Office 365](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) e [SharePoint partilhar ficheiros ou pastas](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c).

## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>Convidar um utilizador convidado para uma aplicação a partir do painel de acesso

Depois de uma aplicação está configurada para Self-Service, os proprietários da aplicação podem utilizar o seu próprio painel de acesso para convidar um utilizador convidado para a aplicação que pretendem partilhar. O utilizador convidado não necessariamente precisa ser adicionado ao Azure AD com antecedência. 

1. Abra o painel de acesso da `https://myapps.microsoft.com`.
2. Aponte para a aplicação, selecione as reticências (**...** ) e, em seguida, selecione **Gerir aplicação**.
 
   ![Captura de ecrã que mostra o submenu para aplicação de gerir para a aplicação do Salesforce](media/add-users-iw/access-panel-manage-app.png)
 
3. Na parte superior da lista de utilizadores, selecione **+**.
   
   ![Captura de ecrã que mostra o símbolo de adição para adicionar membros à aplicação](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. Na **adicionar membros** caixa de pesquisa, escreva o endereço de e-mail para o utilizador convidado. Também tem a opção de incluir uma mensagem de boas-vindas.
   
   ![Captura de ecrã que mostra o adicionar a janela de membros para adicionar um convidado](media/add-users-iw/access-panel-invitation.png)
   
5. Selecione **adicionar** para enviar um convite ao utilizador convidado. Após enviar o convite, a conta do utilizador será automaticamente adicionada ao diretório como convidada.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>Convidar alguém para aderir a um grupo que tenha acesso à aplicação
Depois de uma aplicação está configurada para Self-Service, os proprietários de aplicativos podem convidar utilizadores convidados para os grupos que gerem e que tenham acesso às aplicações que pretendem partilhar. Os utilizadores convidados não tem de já existir no diretório. O proprietário da aplicação segue estes passos para convidar um utilizador convidado ao grupo, para que eles podem aceder à aplicação.

1. Certifique-se de que é um proprietário do grupo self-service que tenha acesso à aplicação que pretende partilhar.
2. Abra o painel de acesso da `https://myapps.microsoft.com`.
3. Selecione o **grupos** aplicação.
   
   ![Captura de ecrã que mostra a aplicação de grupos no painel de acesso](media/add-users-iw/access-panel-groups.png)
   
4. Sob **grupos que possuo**, selecione o grupo que tem acesso à aplicação que pretende partilhar.
   
   ![Captura de ecrã que mostra onde pode selecionar um grupo de grupos que sou proprietário](media/add-users-iw/access-panel-groups-i-own.png)
   
5. No topo da lista de membros do grupo, selecione **+**.
   
   ![Captura de ecrã que mostra o símbolo de adição para adicionar membros ao grupo](media/add-users-iw/access-panel-groups-add-member.png)
   
6. Na **adicionar membros** caixa de pesquisa, escreva o endereço de e-mail para o utilizador convidado. Também tem a opção de incluir uma mensagem de boas-vindas.
   
   ![Captura de ecrã que mostra o adicionar a janela de membros para adicionar um convidado](media/add-users-iw/access-panel-invitation.png)
   
7. Selecione **adicionar** para enviar automaticamente o convite ao utilizador convidado. Após enviar o convite, a conta do utilizador será automaticamente adicionada ao diretório como convidada.


## <a name="prerequisites"></a>Pré-requisitos

Gestão de aplicações self-service requer alguma configuração inicial, um Administrador Global e administrador do Azure AD. Como parte dessa configuração, irá configurar a aplicação para o Self-Service e atribuir um grupo para a aplicação que pode gerir o proprietário da aplicação. Também pode configurar o grupo para permitir que qualquer pessoa pedir associação, mas exigir a aprovação do proprietário do grupo. (Saiba mais sobre [gestão de grupos self-service](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management).) 

> [!NOTE]
> Não é possível adicionar utilizadores convidados para um grupo dinâmico ou a um grupo que está sincronizado com o Active Directory no local.

### <a name="enable-self-service-group-management-for-your-tenant"></a>Ativar a gestão de grupos self-service para o seu inquilino
1. Inicie sessão para o [portal do Azure](https://portal.azure.com) como um Administrador Global.
2. No painel de navegação, selecione **do Azure Active Directory**.
3. Selecione **grupos**.
4. Sob **configurações**, selecione **geral**.
5. Sob **gestão de grupo do serviço de reposição personalizada**, junto a **os proprietários podem gerir pedidos de associação de grupo no painel de acesso**, selecione **Sim**.
6. Selecione **Guardar**.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>Criar um grupo para atribuir à aplicação e tornar o utilizador proprietário
1. Inicie sessão para o [portal do Azure](https://portal.azure.com) como Administrador Global ou administrador do Azure AD.
2. No painel de navegação, selecione **do Azure Active Directory**.
3. Selecione **grupos**.
4. Selecione **novo grupo**.
5. Sob **tipo de grupo**, selecione **segurança**.
6. Introduza um **Nome do grupo** e uma **Descrição do grupo**.
7. Sob **tipo de associação**, selecione **atribuído**.
8. Selecione **Create**e feche o **grupo** página.
9. Sobre o **grupos – todos os grupos** página, abra o grupo. 
10. Sob **Manage**, selecione **proprietários** > **adicionar proprietários**. Procure o utilizador que deve gerir o acesso à aplicação. Selecione o utilizador e, em seguida, clique em **selecione**.

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>Configurar a aplicação para gestão personalizada e atribua o grupo para a aplicação
1. Inicie sessão para o [portal do Azure](https://portal.azure.com) como Administrador Global ou administrador do Azure AD.
2. No painel de navegação, selecione **do Azure Active Directory**.
3. Sob **Manage**, selecione **aplicações empresariais** > **todos os aplicativos**.
4. Na lista de aplicações, localize e abra a aplicação.
5. Sob **Manage**, selecione **início de sessão único**e configurar a aplicação para início de sessão único. (Para obter detalhes, consulte [como gerir o início de sessão único para aplicações empresariais de](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal).)
6. Sob **Manage**, selecione **self-service**e configurar o acesso de aplicação self-service. (Para obter detalhes, consulte [como utilizar o acesso da aplicação self-service](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).) 

    > [!NOTE]
    > Para a definição **a que grupo devem os utilizadores atribuídos ser adicionados?** selecione o grupo que criou na secção anterior.
7. Sob **Manage**, selecione **utilizadores e grupos**e certifique-se de que o grupo de gestão personalizada que criou é apresentada na lista.
8. Para adicionar a aplicação para o painel de acesso do proprietário do grupo, selecione **adicionar utilizador** > **utilizadores e grupos**. Procure o proprietário do grupo e selecione o utilizador, clique em **selecionar**e, em seguida, clique em **atribuir** para adicionar o utilizador para a aplicação.

## <a name="next-steps"></a>Passos Seguintes

Veja os artigos seguintes na colaboração B2B do Azure AD:

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Como é que os administradores do Azure Active Directory adicionar utilizadores de colaboração B2B?](add-users-administrator.md)
- [Resgate de convite de colaboração B2B](redemption-experience.md)
- [Licenciamento de colaboração B2B do Azure AD](licensing-guidance.md)
