---
title: Adicione utilizadores de colaboração B2B como um trabalhador da informação - Azure AD
description: A colaboração B2B permite que os trabalhadores da informação e os proprietários de aplicações adicionem utilizadores convidados à Azure AD para acesso Microsoft Docs
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
ms.openlocfilehash: abb5c6939d8c88db35a776aa8f2c075a4bdcc609
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565422"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>Como os utilizadores da sua organização podem convidar utilizadores convidados para uma app

Depois de um utilizador convidado ter sido adicionado ao diretório em Azure AD, um proprietário de aplicação pode enviar ao utilizador convidado um link direto para a app que pretende partilhar. Os administradores da Azure AD também podem criar gestão de self-service para galerias ou aplicações baseadas em SAML no seu inquilino Azure AD. Desta forma, os proprietários de aplicações podem gerir os seus próprios utilizadores convidados, mesmo que os utilizadores convidados ainda não tenham sido adicionados ao diretório. Quando uma aplicação é configurada para self-service, o proprietário da aplicação usa o seu Painel de Acesso para convidar um utilizador convidado para uma aplicação ou adicionar um utilizador convidado a um grupo que tem acesso à aplicação. A gestão de aplicações de self-service para apps baseadas em galeria e SAML requer alguma configuração inicial por um administrador. Segue-se um resumo das etapas de configuração (para instruções mais [detalhadas, ver pré-requisitos](#prerequisites) mais tarde nesta página):

 - Ativar gestão de grupo de self-service para o seu inquilino
 - Criar um grupo para atribuir à app e fazer do utilizador um proprietário
 - Configure a aplicação para self-service e atribua o grupo à app

> [!NOTE]
> Este artigo descreve como configurar a gestão de self-service para apps baseadas em galeria e SAML que você adicionou ao seu inquilino Azure AD. Também pode [configurar grupos self-service Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) para que os seus utilizadores possam gerir o acesso aos seus próprios grupos Office 365. Para mais formas de os utilizadores poderem partilhar ficheiros e aplicações do Office com os utilizadores convidados, consulte o [acesso do Hóspede no Office 365 grupos](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) e os [ficheiros ou pastas SharePoint](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c).

## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>Convide um utilizador convidado para uma aplicação do Painel de Acesso

Depois de configurar uma aplicação para self-service, os proprietários de aplicações podem usar o seu próprio Painel de Acesso para convidar um utilizador convidado para a app que queiram partilhar. O utilizador convidado não precisa necessariamente de ser adicionado ao Azure AD com antecedência. 

1. Abra o painel de acesso indo para `https://myapps.microsoft.com`.
2. Aponte para a aplicação, selecione as elipses ( **...** ) e, em seguida, selecione **'Gerir a aplicação**.
 
   ![Screenshot mostrando o submenu da aplicação Manage para a app Salesforce](media/add-users-iw/access-panel-manage-app.png)
 
3. No topo da lista de utilizadores, selecione **+** .
   
   ![Screenshot mostrando o símbolo plus para adicionar membros à app](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. Na caixa de pesquisa de **membros Adicionar,** digite o endereço de e-mail para o utilizador convidado. Também tem a opção de incluir uma mensagem de boas-vindas.
   
   ![Screenshot mostrando a janela adicionar membros para adicionar um convidado](media/add-users-iw/access-panel-invitation.png)
   
5. Selecione **Adicionar** para enviar um convite ao utilizador convidado. Após enviar o convite, a conta do utilizador será automaticamente adicionada ao diretório como convidada.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>Convide alguém a juntar-se a um grupo que tenha acesso à app
Depois de configurar uma aplicação para self-service, os proprietários de aplicações podem convidar os utilizadores convidados para os grupos que gerem que têm acesso às aplicações que pretendem partilhar. Os utilizadores convidados já não têm de existir no diretório. O proprietário da aplicação segue estas medidas para convidar um utilizador convidado para o grupo para que possa aceder à aplicação.

1. Certifique-se de que é proprietário do grupo de self-service que tem acesso à app que pretende partilhar.
2. Abra o painel de acesso indo para `https://myapps.microsoft.com`.
3. Selecione a aplicação **Grupos.**
   
   ![Screenshot mostrando a app Grupos no Painel de Acesso](media/add-users-iw/access-panel-groups.png)
   
4. Ao abrigo dos **Grupos que possuo,** selecione o grupo que tem acesso à app que pretende partilhar.
   
   ![Screenshot mostrando onde selecionar um grupo sob os grupos que possuo](media/add-users-iw/access-panel-groups-i-own.png)
   
5. No topo da lista de membros do grupo, selecione **+** .
   
   ![Screenshot mostrando o símbolo plus para adicionar membros ao grupo](media/add-users-iw/access-panel-groups-add-member.png)
   
6. Na caixa de pesquisa de **membros Adicionar,** digite o endereço de e-mail para o utilizador convidado. Também tem a opção de incluir uma mensagem de boas-vindas.
   
   ![Screenshot mostrando a janela adicionar membros para adicionar um convidado](media/add-users-iw/access-panel-invitation.png)
   
7. Selecione **Adicionar** para enviar automaticamente o convite ao utilizador convidado. Após enviar o convite, a conta do utilizador será automaticamente adicionada ao diretório como convidada.


## <a name="prerequisites"></a>Pré-requisitos

A gestão de aplicações self-service requer alguma configuração inicial por um Administrador Global e um administrador da Azure AD. Como parte desta configuração, você vai configurar a app para self-service e atribuir um grupo à app que o proprietário da aplicação pode gerir. Também pode configurar o grupo para permitir que qualquer pessoa solicite a adesão, mas requer a aprovação do proprietário do grupo. (Saiba mais sobre [a gestão do grupo self-service](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management).) 

> [!NOTE]
> Não é possível adicionar utilizadores convidados a um grupo dinâmico ou a um grupo que está sincronizado com o Ative Directory no local.

### <a name="enable-self-service-group-management-for-your-tenant"></a>Ativar gestão de grupo de self-service para o seu inquilino
1. Inscreva-se no [portal Azure](https://portal.azure.com) como Administrador Global.
2. No painel de navegação, selecione **Azure Ative Directory**.
3. Selecione **Grupos**.
4. Em **Definições,** selecione **General**.
5. No âmbito da **Gestão do Grupo self service,** ao lado dos Proprietários podem gerir os pedidos de **membros do grupo no Painel**de Acesso, selecione **Sim**.
6. Selecione **Guardar**.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>Criar um grupo para atribuir à app e fazer do utilizador um proprietário
1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador da AD Azure ou Administrador Global.
2. No painel de navegação, selecione **Azure Ative Directory**.
3. Selecione **Grupos**.
4. Selecione **Novo grupo**.
5. Sob o **tipo de grupo,** selecione **Segurança**.
6. Digite um **nome de grupo** e **descrição do grupo.**
7. No **tipo de Membro,** selecione **Designado**.
8. Selecione **Criar**e fechar a página **do Grupo.**
9. Na página **grupo - Todos os grupos,** abra o grupo. 
10. Under **Manage**, selecione **Owners** > **Adicionar proprietários**. Procure o utilizador que deve gerir o acesso à aplicação. Selecione o utilizador e, em seguida, clique em **Selecionar**.

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>Configure a aplicação para self-service e atribua o grupo à app
1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador da AD Azure ou Administrador Global.
2. No painel de navegação, selecione **Azure Ative Directory**.
3. Em **'Gerir',** selecione **aplicações da Enterprise** > todas as **aplicações.**
4. Na lista de candidaturas, encontre e abra a app.
5. Em **'Gerir',** selecione **um único sinal e**configure a aplicação para um único início de sessão. (Para mais detalhes, consulte [como gerir um único sign-on para aplicações empresariais](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications).)
6. Under **Manage**, selecione **Self-service**e instale acesso a aplicações self-service. (Para mais detalhes, consulte [como utilizar o acesso à aplicação self-service](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).) 

    > [!NOTE]
    > Para a definição **Para que grupo os utilizadores designados devem ser adicionados?**
7. Em **Manage**, selecione **Utilizadores e grupos,** e verifique se o grupo de self-service que criou aparece na lista.
8. Para adicionar a aplicação ao Painel de Acesso do proprietário do grupo, selecione **Adicionar utilizadores** > utilizadores **e grupos**. Procure o proprietário do grupo e selecione o utilizador, clique em **Selecionar**e, em seguida, clique em **Atribuir** para adicionar o utilizador à aplicação.

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos sobre a colaboração Azure AD B2B:

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
- [Como é que os administradores do Azure Ative Directory adicionam utilizadores de colaboração B2B?](add-users-administrator.md)
- [Resgate de convite de colaboração B2B](redemption-experience.md)
- [Licenciamento de colaboração B2B do Azure AD](licensing-guidance.md)
