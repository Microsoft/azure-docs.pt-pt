---
title: 'Quickstart: Adicione utilizadores convidados no portal Azure - Azure AD'
description: Utilize este guia de início rápido para saber como os administradores do Azure Active Directory podem adicionar utilizadores convidados B2B no portal do Azure e ler as instruções do fluxo de trabalho de convite B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12f134a11b6d0a561a8b53156c927906afe2874a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87906143"
---
# <a name="quickstart-add-guest-users-to-your-directory-in-the-azure-portal"></a>Guia de Início Rápido: Adicionar utilizadores convidados ao seu diretório no portal do Azure

Pode convidar qualquer pessoa a colaborar com a sua organização ao adicioná-la ao seu diretório como um utilizador convidado. Em seguida, pode enviar um e-mail de convite que contenha uma ligação de resgate ou enviar uma ligação direta para uma aplicação que pretenda partilhar. Os utilizadores convidados podem iniciar sessão com as respetivas identidades das redes sociais, da escola ou do trabalho. Juntamente com este arranque rápido, pode aprender mais sobre a adição de utilizadores convidados [no portal Azure](add-users-administrator.md), via [PowerShell](b2b-quickstart-invite-powershell.md), ou [a granel](tutorial-bulk-invite.md).

Neste quickstart, você adicionará um novo utilizador convidado ao seu diretório AZure AD através do portal Azure, enviará um convite e verá como é o processo de resgate de convite do utilizador convidado.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o cenário deste tutorial, precisa de:

 - Uma função que lhe permita criar utilizadores no seu diretório de inquilinos, como a função de Administrador Global ou qualquer uma das funções de diretório de administrador limitadas.
 - Uma conta de e-mail válida que possa adicionar ao seu diretório de inquilinos e que possa utilizar para receber o e-mail de convite para teste.

## <a name="add-a-new-guest-user-in-azure-ad"></a>Adicionar um novo utilizador convidado no Azure Active Directory

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador do Azure Active Directory.
2. No painel esquerdo, selecione **Azure Active Directory**.
3.  Em **Gerir**, selecione **Utilizadores**.

    ![Screenshot mostrando onde selecionar a opção Utilizadores](media/quickstart-add-users-portal/quickstart-users-portal-user.png)

4.  Selecione **Novo utilizador convidado**.

    ![Screenshot mostrando onde selecionar a nova opção de utilizador do hóspede](media/quickstart-add-users-portal/quickstart-users-portal-user-3.png)

5. Na nova página do **utilizador,** selecione **Convidar utilizador** e, em seguida, adicionar as informações do utilizador convidado. 

   - **O nome.** O primeiro e último nome do utilizador convidado.
   - **Endereço de e-mail (obrigatório)**. O endereço de e-mail do utilizador convidado.
   - **Mensagem pessoal (opcional)** Inclua uma mensagem de boas-vindas pessoal ao utilizador convidado.
   - **Grupos**: Pode adicionar o utilizador convidado a um ou mais grupos existentes, ou pode fazê-lo mais tarde.
   - **Função diretório**: Se necessitar de permissões administrativas Azure AD para o utilizador, pode adicioná-las a uma função AZure AD. 

6. Selecione **Convidar** para enviar automaticamente o convite ao utilizador convidado. É apresentada uma notificação com a mensagem **Utilizador convidado com sucesso** no canto superior direito. 
7.  Após enviar o convite, a conta do utilizador será automaticamente adicionada ao diretório como convidada.

## <a name="assign-an-app-to-the-guest-user"></a>Atribuir uma aplicação ao utilizador convidado
Adicione a aplicação Salesforce ao seu inquilino de teste e atribua o utilizador convidado de teste à aplicação.
1.  Inicie sessão no portal do Azure como administrador do Azure Active Directory.
2.  No painel esquerdo, selecione **Aplicações empresariais**.
3.  Selecione **Nova aplicação**.
4. Em **Adicionar a partir da galeria**, pesquise a aplicação **Salesforce** e, em seguida, selecione-a.

    ![Screenshot mostrando o Add da caixa de pesquisa da galeria](media/quickstart-add-users-portal/quickstart-users-portal-select-salesforce.png)
5. Selecione **Adicionar**.
6. Em **Gerir**, selecione **Início de sessão único** e em **Modo de Início de Sessão Único**, selecione **Início de Sessão Baseado em Palavra-passe** e clique em **Guardar**.
7. Em **Gerir**, selecione **Utilizadores e grupos** > **Adicionar utilizador** > **Utilizadores e grupos**.
8. Utilize a caixa de pesquisa para pesquisar o utilizador de teste (caso seja necessário) e selecione o utilizador de teste na lista. Em seguida, clique em **Selecionar**.
9. Selecione **Atribuir**. 

## <a name="accept-the-invitation"></a>Aceitar o convite
Agora inicie sessão como utilizador convidado para ver o convite.
1.  Inicie sessão na conta de e-mail do seu utilizador convidado de teste.
2.  Na sua caixa de entrada, procure o e-mail "Foi convidado".

    ![Screenshot mostrando o e-mail de convite B2B](media/quickstart-add-users-portal/quickstart-users-portal-email-small.png)

3.  No corpo do e-mail, selecione **Começar Agora**. É apresentada uma página **Analisar permissões** no browser. 

    ![Screenshot mostrando a página de permissões de revisão](media/quickstart-add-users-portal/quickstart-users-portal-accept.png)

4. Selecione **Aceitar**. É apresentado o Painel de Acesso, que enumera as aplicações a que o utilizador convidado pode aceder.

## <a name="clean-up-resources"></a>Limpar os recursos
Quando já não forem necessários, elimine o utilizador convidado de teste e a aplicação de teste.
1.  Inicie sessão no portal do Azure como administrador do Azure Active Directory.
2.  No painel esquerdo, selecione **Azure Active Directory**.
3.  Em **Gerir**, selecione **Aplicações empresariais**.
4.  Abra a aplicação **Salesforce** e, em seguida, selecione **Eliminar**.
5.  No painel esquerdo, selecione **Azure Active Directory**.
6.  Em **Gerir**, selecione **Utilizadores**.
7.  Selecione o utilizador de teste e, em seguida, selecione **Eliminar utilizador**.

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, criou um utilizador convidado no portal do Azure e enviou um convite para partilhar aplicações. Em seguida, viu o processo de resgate na perspetiva do utilizador convidado e verificou que a aplicação era apresentada no Painel de Acesso do utilizador convidado. Para saber mais sobre como adicionar utilizadores para colaboração, veja [Adicionar utilizadores de colaboração B2B do Azure Active Directory no portal do Azure](add-users-administrator.md).
