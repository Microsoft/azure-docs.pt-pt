---
title: Adicione utilizadores de colaboração B2B no portal Azure - Azure AD
description: Mostra como um administrador pode adicionar utilizadores convidados ao seu diretório de uma organização parceira que usa a colaboração Do Azure Ative Directory (Azure AD) B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf2ca3bde0cfee97adee8bb875df1522e95b1f53
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263507"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Adicione utilizadores de colaboração azure Ative Directory B2B no portal Azure

Como utilizador a quem seja atribuída qualquer uma das funções de diretório de administrador limitado, pode utilizar o portal Azure para convidar utilizadores de colaboração B2B. Pode convidar utilizadores convidados para o diretório, para um grupo ou para uma aplicação. Depois de convidar um utilizador através de qualquer um destes métodos, a conta do utilizador convidado é adicionada ao Azure Ative Directory (Azure AD), com um tipo de utilizador de *Hóspede*. O utilizador convidado deve então resgatar o seu convite para aceder aos recursos. Um convite de um utilizador não expira.

Depois de adicionar um utilizador convidado ao diretório, pode enviar ao utilizador convidado um link direto para uma aplicação partilhada, ou o utilizador convidado pode clicar no URL de resgate no e-mail de convite. Para obter mais informações sobre o processo de resgate, consulte a redenção do convite de [colaboração B2B.](redemption-experience.md)

> [!IMPORTANT]
> Deve seguir os passos em Como: Adicionar as informações de [privacidade da sua organização no Diretório Ativo Azure](https://aka.ms/adprivacystatement) para adicionar o URL da declaração de privacidade da sua organização. Como parte do primeiro processo de resgate de convites, um utilizador convidado deve consentir com os seus termos de privacidade para continuar. 

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que as definições de colaboração externa da sua organização estão configuradas de modo a que possa convidar os hóspedes. Por padrão, todos os utilizadores e administradores podem convidar os hóspedes. Mas as políticas de colaboração externa da sua organização podem ser configuradas para impedir que certos tipos de utilizadores ou administradores convidem os hóspedes. Para saber como visualizar e definir estas políticas, consulte a [colaboração externa Enable B2B e gere quem pode convidar os hóspedes.](delegate-invitations.md)

## <a name="add-guest-users-to-the-directory"></a>Adicione utilizadores convidados ao diretório

Para adicionar utilizadores de colaboração B2B ao diretório, siga estes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como um utilizador a quem seja atribuído um papel de diretório de administrador limitado ou o papel de Convidado convidado.
2. Procure e selecione **Azure Ative Directory** a partir de qualquer página.
3. Em **Gerir**, selecione **Utilizadores**.
4. Selecione **Novo utilizador convidado**.

   ![Mostra onde o novo utilizador convidado está na UI](./media/add-users-administrator/new-guest-user-in-all-users.png) 
 
   > [!NOTE]
   > A nova opção **de utilizador convidado** também está disponível na página de **relações organizacionais.** No **Diretório Ativo Azure,** sob **a Gestão,** selecione **relações organizacionais.**

5. Na página **de utilizador Nova,** selecione **Convidar o utilizador** e, em seguida, adicionar as informações do utilizador convidado. 

    > [!NOTE]
    > Os endereços de e-mail do grupo não são suportados; insira o endereço de e-mail para um indivíduo. Além disso, alguns fornecedores de e-mail permitem que os utilizadores adicionem um símbolo plus (+) e texto adicional aos seus endereços de e-mail para ajudar com coisas como a filtragem da caixa de entrada. No entanto, a Azure AD não suporta atualmente mais símbolos em endereços de e-mail. Para evitar problemas de entrega, omita o símbolo plus e quaisquer caracteres que o seguem até ao símbolo @.

   - **O nome.** O primeiro e último nome do utilizador convidado.
   - **Endereço de e-mail (obrigatório)** . O endereço de e-mail do utilizador convidado.
   - **Mensagem pessoal (opcional)** Inclua uma mensagem de boas-vindas pessoal ao utilizador convidado.
   - **Grupos**: Pode adicionar o utilizador convidado a um ou mais grupos existentes, ou pode fazê-lo mais tarde.
   - **Função de diretório**: Se necessitar de permissões administrativas da AD Azure para o utilizador, pode adicioná-las a uma função Azure AD. 

7. Selecione **Convidar** para enviar automaticamente o convite ao utilizador convidado. 
 
Após enviar o convite, a conta do utilizador será automaticamente adicionada ao diretório como convidada.


![Mostra o utilizador B2B com o tipo de utilizador convidado](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Adicione utilizadores convidados a um grupo
Se precisar de adicionar manualmente utilizadores de colaboração B2B a um grupo, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador do Azure AD.
2. Procure e selecione **Azure Ative Directory** a partir de qualquer página.
3. Under **Manage**, selecione **Groups**.
4. Selecione um grupo (ou clique em **Novo grupo** para criar um novo). É uma boa ideia incluir na descrição do grupo que o grupo contém utilizadores convidados B2B.
5. Selecione **Membros**. 
6. Efetue uma das seguintes ações:
   - Se o utilizador convidado já existir no diretório, procure o utilizador B2B. Selecione o utilizador e, em seguida, clique em **Selecionar** para adicionar o utilizador ao grupo.
   - Se o utilizador convidado ainda não existir no diretório, convide-os para o grupo digitando o seu endereço de e-mail na caixa de pesquisa, digitando uma mensagem pessoal opcional e clicando em **Select**. O convite sai automaticamente para o utilizador convidado.
     
     ![Adicione botão de convite para adicionar membros convidados](./media/add-users-administrator/GroupInvite.png)
   
Também pode utilizar grupos dinâmicos com colaboração Azure AD B2B. Para mais informações, consulte [grupos Dinâmicos e colaboração Azure Ative Directory B2B.](use-dynamic-groups.md)

## <a name="add-guest-users-to-an-application"></a>Adicione utilizadores convidados a uma aplicação

Para adicionar utilizadores de colaboração B2B a uma aplicação, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador do Azure AD.
2. Procure e selecione **Azure Ative Directory** a partir de qualquer página.
3. Em **'Gerir',** selecione **aplicações da Enterprise** > todas as **aplicações.**
4. Selecione a aplicação à qual pretende adicionar utilizadores convidados.
5. No painel de instrumentos da aplicação, selecione **Total Users** para abrir o painel de Utilizadores **e grupos.**

    ![Botão Total utilizadores para adicionar utilizadores e grupos abertos](./media/add-users-administrator/AppUsersAndGroups.png)

6. Selecione **Adicionar utilizador**.
7. Em **função da atribuição de adicionar,** selecione **User and groups**.
8. Efetue uma das seguintes ações:
   - Se o utilizador convidado já existir no diretório, procure o utilizador B2B. Selecione o utilizador, clique em **Selecionar**, e, em seguida, clique em **Atribuir** para adicionar o utilizador à aplicação.
   - Se o utilizador convidado ainda não existir no diretório, sob o **membro Select ou convidar um utilizador externo,** escreva o endereço de e-mail do utilizador. Na caixa de mensagens, digite uma mensagem pessoal opcional. Por baixo da caixa de mensagens, clique **em Convidar**.
           
       ![Adicione botão de convite para adicionar membros convidados](./media/add-users-administrator/AppInviteUsers.png)
   
      Clique em **Selecionar**e, em seguida, clique em **Atribuir** para adicionar o utilizador à aplicação. Um convite sai automaticamente para o utilizador convidado.

9. O utilizador convidado aparece na lista de **Utilizadores e grupos** da aplicação com a função atribuída de **Acesso Predefinido**. Se quiser mudar o papel, faça o seguinte:
   - Selecione o utilizador convidado e, em seguida, **selecione Editar**. 
   - Em **função da Atribuição de Edição,** clique em **Selecionar função**e selecione a função que pretende atribuir ao utilizador selecionado.
   - Clique em **Selecionar**.
   - Clique em **Atribuir**.
 
## <a name="resend-invitations-to-guest-users"></a>Reenviar convites para utilizadores convidados

Se um utilizador convidado ainda não tiver reembolsado o seu convite, pode reenviar o e-mail de convite.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador do Azure AD.
2. Procure e selecione **Azure Ative Directory** a partir de qualquer página.
3. Em **Gerir**, selecione **Utilizadores**.
5. Selecione a conta de utilizador.
6. Em **'Gerir',** selecione **Perfil**.
7. Se o utilizador ainda não aceitou o convite, está disponível uma opção de **convite Resend.** Selecione este botão para reenviar.

   ![Reenviar opção de convite no perfil do utilizador](./media/add-users-administrator/b2b-user-resend-invitation.png)

> [!NOTE]
> Se reenviar um convite que inicialmente direcionou o utilizador para uma aplicação específica, entenda que o link no novo convite leva o utilizador ao Painel de Acesso de alto nível.

## <a name="next-steps"></a>Passos seguintes

- Para saber como os administradores da AD não-Azure podem adicionar utilizadores convidados B2B, veja como os trabalhadores da [informação adicionam utilizadores de colaboração B2B?](add-users-information-worker.md)
- Para obter informações sobre o e-mail de convite, consulte os elementos do convite de [colaboração B2B.](invitation-email-elements.md)

