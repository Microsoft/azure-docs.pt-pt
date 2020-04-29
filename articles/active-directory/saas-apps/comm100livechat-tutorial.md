---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Comm100 Live Chat [ Comm100 Live Chat ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Comm100 Live Chat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e41161a2e1bfd5544410bba0cb470bacbf152fd2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75561260"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-comm100-live-chat"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com Comm100 Live Chat

Neste tutorial, você vai aprender a integrar o Comm100 Live Chat com o Azure Ative Directory (Azure AD). Quando integrar o Comm100 Live Chat com o Azure AD, pode:

* Controle em Azure AD que tem acesso ao Comm100 Live Chat.
* Permita que os seus utilizadores sejam automaticamente inscritos no Comm100 Live Chat com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Comm100 Live Chat single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Comm100 Live Chat suporta **SP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Adicionar Comm100 Live Chat da galeria

Para configurar a integração do Comm100 Live Chat em Azure AD, você precisa adicionar Comm100 Live Chat da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **O Chat Ao Vivo Comm100** na caixa de pesquisa.
1. Selecione **Comm100 Live Chat** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-comm100-live-chat"></a>Configure e teste Azure AD single sign-on para Comm100 Live Chat

Configure e teste Azure AD SSO com Comm100 Live Chat utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Comm100 Live Chat.

Para configurar e testar o Azure AD SSO com o Comm100 Live Chat, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure O SSO de Chat ao Vivo Comm100](#configure-comm100-live-chat-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. Crie o utilizador de **[teste Comm100 Live Chat](#create-comm100-live-chat-test-user)** - para ter uma contrapartida de B.Simon no Comm100 Live Chat que está ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Comm100 Live Chat,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > O valor do URL de inscrição não é real. Atualizará o valor de URL de Sign-on com o URL de sign-on real, o que é explicado mais tarde no tutorial.

1. A aplicação Comm100 Live Chat espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação Comm100 Live Chat espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com o seu requisito.

    | Nome |  Atributo fonte|
    | ---------------| --------------- |
    |   e-mail    | utilizador.mail |

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **'Chat' Comm100,** copie os URL(s) apropriados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, você permitirá que B.Simon use o único sign-on Azure, concedendo acesso ao Comm100 Live Chat.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Comm100 Live Chat**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-comm100-live-chat-sso"></a>Configure Comm100 Live Chat SSO

1. Numa janela de navegador web diferente, inscreva-se no Comm100 Live Chat como administrador de segurança.

1. No lado superior direito da página, clique na **Minha Conta**.

   ![Comm100 Live Chat myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

1. Do lado esquerdo do menu, clique em **Segurança** e, em seguida, clique em **Agent Single Sign-On**.

   ![Comm100 Segurança do Chat ao Vivo](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

1. Na página de Assinatura Única do **Agente,** execute os seguintes passos:

   ![Comm100 Segurança do Chat ao Vivo](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

   a. Copie o primeiro link realçado e **cole-o** na caixa de texto url signon na secção **basic SAML Configuração** no portal Azure.

   b. Na caixa de texto **URL SAML SSO,** colá o valor do URL de **Login,** que copiou do portal Azure.

   c. Na caixa de texto url de **logout remoto,** colá o valor do URL de **Logout,** que copiou do portal Azure.

   d. Clique **Em Escolher um Ficheiro** para fazer o upload do certificado codificado base-64 que descarregou do portal Azure, para o **Certificado**.

   e. Clique em **Guardar Alterações**.

### <a name="create-comm100-live-chat-test-user"></a>Criar o utilizador de teste de Chat Ao Vivo Comm100

Para permitir que os utilizadores da Azure AD entrem no Comm100 Live Chat, devem ser aprovisionados no Comm100 Live Chat. No Comm100 Live Chat, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no Comm100 Live Chat como administrador de segurança.

2. No lado superior direito da página, clique na **Minha Conta**.

    ![Comm100 Live Chat myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. Do lado esquerdo do menu, clique em **Agentes** e clique em **New Agent**.

    ![Comm100 Live Chat agente](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. Na página **New Agent,** execute os seguintes passos:

    ![Comm100 Live Chat novo agente](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. Na caixa de texto **por e-mail,** introduza o e-mail de utilizadores como **B.simon\@contoso.com**.

    b. Na caixa de texto **First Name,** introduza o primeiro nome do utilizador como **B**.

    c. Na caixa de texto **De Apelido,** introduza o último nome do utilizador como **simon**.

    d. Na caixa de texto 'Nome de **exibição',** introduza o nome de visualização do utilizador como **B.simon**

    e. Na caixa de texto **Password,** escreva a sua palavra-passe.

    f. Clique em **Guardar**.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Comm100 Live Chat no Painel de Acesso, deve ser automaticamente inscrito no Comm100 Live Chat para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Comm100 Live Chat com o Azure AD](https://aad.portal.azure.com/)

