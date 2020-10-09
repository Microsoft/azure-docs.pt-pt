---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Comm100 Live Chat Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Comm100 Live Chat.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: 0ce6e4dc44a19f4aaee35cc4477761010d76a96b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91775924"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-comm100-live-chat"></a>Tutorial: Azure Ative Directory single sign-on (SSO) integração com Comm100 Live Chat

Neste tutorial, você vai aprender a integrar o Comm100 Live Chat com o Azure Ative Directory (Azure AD). Quando integrar o Comm100 Live Chat com a Azure AD, pode:

* Controle em Azure AD que tem acesso ao Comm100 Live Chat.
* Permita que os seus utilizadores sejam automaticamente inscritos no Comm100 Live Chat com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Comm100 Live Chat assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Comm100 Live Chat suporta **SP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Adicionar Comm100 Live Chat da galeria

Para configurar a integração do Comm100 Live Chat em AZure AD, precisa de adicionar o Comm100 Live Chat da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **da galeria,** escreva **Comm100 Live Chat** na caixa de pesquisa.
1. Selecione **Comm100 Live Chat** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-comm100-live-chat"></a>Configurar e testar Azure AD single sign-on para Comm100 Live Chat

Configure e teste Azure AD SSO com Comm100 Live Chat usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Comm100 Live Chat.

Para configurar e testar o Azure AD SSO com o Comm100 Live Chat, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Comm100 Live Chat SSO](#configure-comm100-live-chat-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Comm100 Live Chat test user](#create-comm100-live-chat-test-user)** - para ter uma contraparte de B.Simon em Comm100 Live Chat que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Comm100 Live Chat,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > O valor url de inscrição não é real. Irá atualizar o valor URL de acesso com o URL de inscrição real, que é explicado mais tarde no tutorial.

1. A aplicação Comm100 Live Chat espera as afirmações DO SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação Comm100 Live Chat espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com o seu requisito.

    | Nome |  Atributo de origem|
    | ---------------| --------------- |
    |   e-mail    | user.mail |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configuração Comm100 Live Chat,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Comm100 Live Chat.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Comm100 Live Chat**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-comm100-live-chat-sso"></a>Configure Comm100 Live Chat SSO

1. Numa janela diferente do navegador web, inscreva-se no Comm100 Live Chat como Administrador de Segurança.

1. No lado superior direito da página, clique em **"Minha Conta".**

   ![Conta de Chat ao Vivo comm100](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

1. A partir do lado esquerdo do menu, clique em **Segurança** e, em seguida, clique **em Sign-On Único agente**.

   ![Screenshot que mostra o menu da conta do lado esquerdo com "Segurança" e "Agente Sign-On Único" em destaque.](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

1. Na página **'Sign-On' único do agente,** execute os seguintes passos:

   ![Segurança comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

   a. Copie o primeiro link realçado e **cole-o** na caixa de texto URL de acesso ao início da súmia na secção **de configuração SAML básica** no portal Azure.

   b. Na caixa de texto **URL SSO SAML,** cole o valor do URL de **Login,** que copiou a partir do portal Azure.

   c. Na caixa de texto **url de logout remoto,** cole o valor do **URL logout,** que copiou a partir do portal Azure.

   d. Clique **em Escolher um Ficheiro** para carregar o certificado codificado base-64 que descarregou a partir do portal Azure, para o **Certificado.**

   e. Clique em **Guardar Alterações**.

### <a name="create-comm100-live-chat-test-user"></a>Criar utilizador de teste de Chat Ao Vivo comm100

Para permitir que os utilizadores da Azure AD inscrevam-se no Comm100 Live Chat, devem ser ateados no Comm100 Live Chat. No Comm100 Live Chat, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no Comm100 Live Chat como Administrador de Segurança.

2. No lado superior direito da página, clique em **"Minha Conta".**

    ![Conta de Chat ao Vivo comm100](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. Do lado esquerdo do menu, clique em **Agentes** e, em seguida, clique em **Novo Agente**.

    ![Agente de Chat Ao Vivo comm100](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. Na página **New Agent,** execute os seguintes passos:

    ![Novo agente do Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. Na caixa de texto **por e-mail,** insira o e-mail de um utilizador como **B.simon \@ contoso.com**.

    b. Na caixa de texto **First Name,** insira o primeiro nome do utilizador como **B**.

    c. Na caixa de texto **Last Name,** insira o último nome do utilizador como **simon**.

    d. Na caixa de texto **'Display Name',** insira o nome de exibição do utilizador como **B.simon**

    e. Na caixa de texto da **palavra-passe,** digite a sua palavra-passe.

    f. Clique em **Guardar**.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo comm100 Live Chat no Painel de Acesso, deverá ser automaticamente inscrito no Comm100 Live Chat para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Comm100 Live Chat com a Azure AD](https://aad.portal.azure.com/)

