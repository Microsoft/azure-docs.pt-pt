---
title: Adicione utilizadores de colaboração B2B como um trabalhador da informação - Azure AD
description: A colaboração B2B permite aos trabalhadores da informação e proprietários de aplicações adicionar utilizadores convidados à Azure AD para acesso Microsoft Docs
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 12/19/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9bc3175017e5b26251d1a12d0d1e2c51c4e5f9c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87909745"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>Como os utilizadores da sua organização podem convidar utilizadores convidados para uma aplicação

Depois de um utilizador convidado ter sido adicionado ao diretório em Azure AD, um proprietário de aplicações pode enviar ao utilizador convidado um link direto para a app que pretende partilhar. Os administradores da AD AZure também podem criar uma gestão de self-service para apps baseadas em galerias ou saml no seu inquilino Azure AD. Desta forma, os proprietários de aplicações podem gerir os seus próprios utilizadores de hóspedes, mesmo que os utilizadores convidados ainda não tenham sido adicionados ao diretório. Quando uma aplicação é configurada para self-service, o proprietário da aplicação usa o seu Painel de Acesso para convidar um utilizador convidado para uma aplicação ou adicionar um utilizador convidado a um grupo que tenha acesso à app. A gestão de aplicativos self-service para apps baseadas em galerias e SAML requer alguma configuração inicial por um administrador. Segue-se um resumo dos passos de configuração (para obter instruções mais detalhadas, consulte [pré-requisitos](#prerequisites) mais tarde nesta página):

 - Ativar a gestão do grupo self-service para o seu inquilino
 - Criar um grupo para atribuir à app e fazer do utilizador um proprietário
 - Configure a app para self-service e atribua o grupo à app

> [!NOTE]
> Este artigo descreve como configurar a gestão de self-service para galerias e aplicações baseadas em SAML que adicionou ao seu inquilino AZure AD. Também pode [configurar grupos microsoft 365 self-service](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) para que os seus utilizadores possam gerir o acesso aos seus próprios grupos Microsoft 365. Para obter mais formas de os utilizadores partilharem ficheiros e aplicações do Office com utilizadores [convidados, consulte o acesso do Hóspede em grupos microsoft 365](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) e [ficheiros share SharePoint ou pastas](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c).

## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>Convide um utilizador convidado para uma aplicação do Painel de Acesso

Depois de uma aplicação ser configurada para self-service, os proprietários de aplicações podem usar o seu próprio Painel de Acesso para convidar um utilizador convidado para a app que querem partilhar. O utilizador convidado não precisa necessariamente de ser adicionado ao Azure AD com antecedência. 

1. Abra o painel de acesso indo para `https://myapps.microsoft.com` .
2. Aponte para a aplicação, selecione as elipses **(... )** e, em seguida, selecione **Gerir a aplicação**.
 
   ![Screenshot mostrando o sub-menu da aplicação Manage para a aplicação Salesforce](media/add-users-iw/access-panel-manage-app.png)
 
3. No topo da lista de utilizadores, selecione **+** .
   
   ![Screenshot mostrando o símbolo mais para adicionar membros à app](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. Na caixa de pesquisa **de membros Add,** digite o endereço de e-mail para o utilizador convidado. Também tem a opção de incluir uma mensagem de boas-vindas.
   
   ![Screenshot mostrando a janela de membros Add para adicionar um hóspede](media/add-users-iw/access-panel-invitation.png)
   
5. **Selecione Adicionar** para enviar um convite ao utilizador convidado. Após enviar o convite, a conta do utilizador será automaticamente adicionada ao diretório como convidada.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>Convide alguém para se juntar a um grupo que tenha acesso à app
Depois de uma aplicação ser configurada para self-service, os proprietários de aplicações podem convidar os utilizadores convidados para os grupos que gerem e que têm acesso às apps que querem partilhar. Os utilizadores convidados já não têm de existir no diretório. O proprietário da aplicação segue estes passos para convidar um utilizador convidado para o grupo para que possa aceder à app.

1. Certifique-se de que é proprietário do grupo self-service que tem acesso à app que pretende partilhar.
2. Abra o painel de acesso indo para `https://myapps.microsoft.com` .
3. Selecione a aplicação **Grupos.**
   
   ![Screenshot mostrando a aplicação Grupos no Painel de Acesso](media/add-users-iw/access-panel-groups.png)
   
4. Em **Grupos I,** selecione o grupo que tem acesso à app que pretende partilhar.
   
   ![Screenshot mostrando onde selecionar um grupo sob os grupos que possuo](media/add-users-iw/access-panel-groups-i-own.png)
   
5. No topo da lista de membros do grupo, selecione **+** .
   
   ![Screenshot mostrando o símbolo mais para adicionar membros ao grupo](media/add-users-iw/access-panel-groups-add-member.png)
   
6. Na caixa de pesquisa **de membros Add,** digite o endereço de e-mail para o utilizador convidado. Também tem a opção de incluir uma mensagem de boas-vindas.
   
   ![Screenshot mostrando a janela de membros Add para adicionar um hóspede](media/add-users-iw/access-panel-invitation.png)
   
7. **Selecione Adicionar** para enviar automaticamente o convite para o utilizador convidado. Após enviar o convite, a conta do utilizador será automaticamente adicionada ao diretório como convidada.


## <a name="prerequisites"></a>Pré-requisitos

A gestão de aplicativos self-service requer alguma configuração inicial por um Administrador Global e um administrador AD Azure. Como parte desta configuração, irá configurar a app para self-service e atribuir um grupo à app que o proprietário da aplicação pode gerir. Também pode configurar o grupo para permitir que qualquer pessoa solicite a adesão, mas requer a aprovação do proprietário do grupo. (Saiba mais sobre [a gestão do grupo self-service](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management).) 

> [!NOTE]
> Não é possível adicionar utilizadores convidados a um grupo dinâmico ou a um grupo sincronizado com o Ative Directory no local.

### <a name="enable-self-service-group-management-for-your-tenant"></a>Ativar a gestão do grupo self-service para o seu inquilino
1. Inscreva-se no [portal Azure](https://portal.azure.com) como Administrador Global.
2. No painel de navegação, selecione **Azure Ative Directory**.
3. Selecione **Grupos**.
4. Em **Definições**, selecione **General**.
5. No **âmbito da Gestão do Grupo self service,** ao lado **dos Proprietários pode gerir os pedidos de membros do grupo no Painel de Acesso,** selecione **Sim**.
6. Selecione **Guardar**.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>Criar um grupo para atribuir à app e fazer do utilizador um proprietário
1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador AD da Azure ou Administrador Global.
2. No painel de navegação, selecione **Azure Ative Directory**.
3. Selecione **Grupos**.
4. Selecione **Novo grupo**.
5. Sob **o tipo de grupo**, selecione **Security**.
6. Introduza um **Nome do grupo** e uma **Descrição do grupo**.
7. Sob **o tipo de membro**, selecione **Atribuído**.
8. Selecione **Criar**e feche a página **do Grupo.**
9. Na página **Grupos - Todos os grupos,** abra o grupo. 
10. Em **Manage**, selecione **Proprietários**  >  **Adicionar proprietários**. Procure o utilizador que deve gerir o acesso à aplicação. Selecione o utilizador e, em seguida, clique em **Selecionar**.

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>Configure a app para self-service e atribua o grupo à app
1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador AD da Azure ou Administrador Global.
2. No painel de navegação, selecione **Azure Ative Directory**.
3. Em **Gestão**, selecione **Aplicações da Empresa**Todas as  >  **aplicações**.
4. Na lista de candidaturas, encontre e abra a aplicação.
5. Em **Manage**, selecione **Single sign-on**e configurar o pedido de inscrição única. (Para mais detalhes, consulte [como gerir um único s-on para aplicações empresariais](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications).)
6. Em **Manage**, selecione **Self-service**e crie acesso a aplicativos de self-service. (Para mais detalhes, consulte [como utilizar o acesso a aplicativos self-service](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).) 

    > [!NOTE]
    > Para a definição **Para que grupo deve ser adicionado os utilizadores atribuídos?** Selecione o grupo que criou na secção anterior.
7. Em **Manage**, selecione **Utilizadores e grupos**, e verifique se o grupo de self-service que criou aparece na lista.
8. Para adicionar a aplicação ao Painel de Acesso do proprietário do grupo, **selecione Adicionar**  >  **utilizadores e grupos**. Procure o proprietário do grupo e selecione o utilizador, clique em **Selecionar**e, em seguida, clique em **Atribuir** para adicionar o utilizador à aplicação.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos sobre a colaboração Azure AD B2B:

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Como é que os administradores do Azure Ative Directory adicionam utilizadores de colaboração B2B?](add-users-administrator.md)
- [Resgate de convite de colaboração B2B](redemption-experience.md)
- [Licenciamento de colaboração B2B do Azure AD](licensing-guidance.md)
