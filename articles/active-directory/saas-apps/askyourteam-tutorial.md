---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com AskYourTeam Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o AskYourTeam.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: jeedes
ms.openlocfilehash: 6ab6a355b986daeca41ffd18a51689cd9d880dcd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91713811"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>Tutorial: Azure Ative Directory integração única (SSO) com AskYourTeam

Neste tutorial, você vai aprender a integrar AskYourTeam com Azure Ative Directory (Azure AD). Quando integrar o AskYourTeam com a Azure AD, pode:

* Controle em Azure AD que tem acesso a AskYourTeam.
* Ative os seus utilizadores a serem automaticamente inscritos no AskYourTeam com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* AskYourTeam assinatura única ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* AskYourTeam suporta **SP e IDP** iniciado SSO.
* Uma vez configurado AskYourTeam, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-askyourteam-from-the-gallery"></a>Adicionar AskYourTeam da galeria

Para configurar a integração do AskYourTeam no AD Azure, é necessário adicionar o AskYourTeam da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite AskYourTeam** na caixa de pesquisa.
1. Selecione **AskYourTeam** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-askyourteam"></a>Configurar e testar Azure AD único sign-on para AskYourTeam

Configure e teste Azure AD SSO com AskYourTeam usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AD Azure e o utilizador relacionado na AskYourTeam.

Para configurar e testar o Azure AD SSO com a AskYourTeam, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure AskYourTeam SSO](#configure-askyourteam-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create AskYourTeam test user](#create-askyourteam-test-user)** - para ter uma contraparte de B.Simon em AskYourTeam que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **AskYourTeam,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<COMPANY>.app.askyourteam.com/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com os valores de URL de resposta e Sign-On que são explicados mais tarde no tutorial.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar AskYourTeam,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao AskYourTeam.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **AskYourTeam**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-askyourteam-sso"></a>Configurar AskyourTeam SSO

1. Numa janela diferente do navegador web, inscreva-se no website da AskYourTeam como administrador.

1. Clique na **Organização My.**

    ![A screenshot mostra a ligação My Organization.](./media/askyourteam-tutorial/user1.png)

1. Clique em **Integrações**.

    ![A screenshot mostra a ligação Integrações.](./media/askyourteam-tutorial/configure1.png)

1. Clique em **Configurações de Edição**.

    ![A screenshot mostra a mensagem single Sign-On com um botão De editar Definições.](./media/askyourteam-tutorial/configure2.png)

1. Na página **Editar A Integração de Sign-On Única,** execute os seguintes passos: 

    ![O Screenshot mostra editar a Integração de Sign-On Única, onde pode introduzir os valores deste passo.](./media/askyourteam-tutorial/configure3.png)

    a. Na caixa de texto **URL de serviço de Sign-On único SAML,** cole o valor URL de **login** que copiou do portal Azure.

    b. Na caixa de texto **ID da Entidade SAML,** cole o valor **identificador Azure AD** que copiou do portal Azure.

    c. Na caixa de texto **URL de assinatura,** cole o valor **URL logout** que copiou a partir do portal Azure.

    d. Abra o Certificado descarregado **(Base64)** do portal Azure para o Bloco de Notas e cole o conteúdo no **Certificado de Assinatura SAML - Base64.**

    > [!NOTE]
    > Em alternativa, também pode carregar o ficheiro **XML dos metadados da Federação** clicando na opção **Escolha Ficheiro.**

    e. Copiar **URL (Url de serviço de apoio ao consumidor de afirmação)** valor, colar este valor na caixa de texto **URL de resposta** na secção **configuração DE SAML básico** no portal Azure.

    f. Copiar Assinar no valor **URL,** colar este valor na caixa de texto **do Sinal na** caixa de texto URL na secção **configuração básica do SAML** no portal Azure.

    exemplo, Clique em **Guardar**.

### <a name="create-askyourteam-test-user"></a>Criar utilizador de teste AskYourTeam

1. Numa janela diferente do navegador web, inscreva-se no website da AskYourTeam como administrador.

1. Clique na **Organização My.**

    ![A screenshot mostra a ligação My Organization onde inicia esta tarefa.](./media/askyourteam-tutorial/user1.png)

1. Clique **nos Utilizadores** e selecione **Novo Utilizador.**

    ![A screenshot mostra a ligação dos Utilizadores com o Novo Utilizador.](./media/askyourteam-tutorial/user2.png)

1. Na nova secção **de utilizadores,** execute os seguintes passos:

    ![A screenshot mostra uma secção de Novo Utilizador onde introduz as informações do utilizador.](./media/askyourteam-tutorial/user3.png)

    1. Na caixa de texto do **primeiro nome,** insira o primeiro nome do utilizador.

    1. Na caixa de texto **do último nome,** insira o último nome do utilizador.

    1. Na caixa de sms **do Email,** introduza o endereço de e-mail do utilizador como B.Simon@contoso.com .

    1. Selecione a **Função** para o utilizador de acordo com o seu requisito de organização.

    1. Clique em **Guardar**.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo AskYourTeam no Painel de Acesso, deverá ser automaticamente inscrito na AskYourTeam para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente AskYourTeam com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
