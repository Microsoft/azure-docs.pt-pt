---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com a Teamphoria Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Teamphoria.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.openlocfilehash: b53b717759bf65241b946194aab91bab0608320c
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92483408"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-teamphoria"></a>Tutorial: Azure Ative Directy integração única (SSO) com a Teamphoria

Neste tutorial, você vai aprender a integrar Teamphoria com Azure Ative Directory (Azure AD). Quando integrar a Teamphoria com Azure AD, pode:

* Controlo em Azure AD que tem acesso à Teamphoria.
* Permita que os seus utilizadores sejam automaticamente inscritos na Teamphoria com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por Teamphoria (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Teamphoria apoia **SP** iniciado SSO

## <a name="adding-teamphoria-from-the-gallery"></a>Adicionando Teamphoria da galeria

Para configurar a integração da Teamphoria no Azure AD, é necessário adicionar Teamphoria da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **digite Teamphoria** na caixa de pesquisa.
1. Selecione **Teamphoria** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-teamphoria"></a>Configurar e testar Azure AD único sinal para a Teamphoria

Configure e teste Azure AD SSO com Teamphoria usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Teamphoria.

Para configurar e testar o Azure AD SSO com a Teamphoria, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Teamphoria SSO](#configure-teamphoria-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Teamphoria test user](#create-teamphoria-test-user)** - para ter uma contraparte de B.Simon em Teamphoria que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Teamphoria,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<sub-domain>.teamphoria.com/login`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte [a equipa de apoio ao cliente da Teamphoria](https://www.teamphoria.com/) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configuração teamphoria,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Teamphoria.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Teamphoria**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-teamphoria-sso"></a>Configure Teamphoria SSO

1. Para automatizar a configuração dentro da Teamphoria, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar Teamphoria** irá direcioná-lo para a aplicação Teamphoria. A partir daí, forneça as credenciais de administração para assinar em Teamphoria. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração](common/setup-sso.png)

3. Se quiser configurar o Teamphoria manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Teamphoria como administrador e execute os seguintes passos:

4. Aceda à opção **ADMIN SETTINGS** na barra de ferramentas esquerda e sob o separador configurar clique no **SIGN-ON ÚNICO** para abrir a janela de configuração SSO.

    ![A screenshot mostra definições de ADMIN onde pode selecionar UM ÚNICO SIGN-ON.](./media/teamphoria-tutorial/admin_sso_configure.png)

5. Clique na opção **ADD NEW IDENTITY PROVIDER** no canto superior direito para abrir o formulário para adicionar as definições de SSO.

    ![A screenshot mostra onde pode selecionar ADICIONAR NOVO FORNECEDOR DE IDENTIDADE.](./media/teamphoria-tutorial/add_new_identity_provider.png)

6. Insira os detalhes nos campos como descrito abaixo...

    ![A screenshot mostra a página onde pode introduzir os valores descritos.](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **NOME DO VISÃO**: Introduza o nome do visor do plugin na página de administração.

    b. **NOME DO BOTÃO**: O nome do separador que irá exibir na página de início de sessão para iniciar sessão via SSO.

    c. **CERTIFICADO**: Abra o Certificado descarregado anteriormente a partir do portal Azure em bloco de notas, copie o conteúdo do mesmo e cole-o aqui na caixa.

    d. **PONTO DE ENTRADA**: Cole o **URL de login** copiado anteriormente do portal Azure.

    e. Mude a opção para **ON** e clique em **SAVE**.

### <a name="create-teamphoria-test-user"></a>Criar utilizador de teste de Teamphoria

Para permitir que os utilizadores da Azure AD inscrevam-se na Teamphoria, devem ser a provisionados em Teamphoria. No caso da Teamphoria, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da sua empresa Teamphoria como administrador.

1. Clique nas definições **de ADMIN** na barra de ferramentas esquerda e no separador **GESTÃO** Clique nos **UTILIZADORES** para abrir a página de administração para os utilizadores.

    ![Adicionar Empregado](./media/teamphoria-tutorial/admin_manage_users.png)

1. Clique na opção **MANUAL CONVIDAR.**

    ![A screenshot mostra a opção MANUAL INVITE.](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. Nesta página, execute a seguinte ação.

    ![A screenshot mostra a página MANUAL USER INVITE onde pode introduzir nome e endereço de e-mail.](./media/teamphoria-tutorial/manual_user_invite.png)

    a. Na caixa de sms **do endereço de e-mail,** insira o endereço de **e-mail** do utilizador como B.Simon.

    b. Na caixa de texto **NAME NAME,** insira o primeiro nome do utilizador como **B**.

    c. Na caixa de texto **NAME,** insira o último nome do utilizador como **Simon**.

    d. Clique **em CONVIDAR 1 UTILIZADOR**. O utilizador precisa de aceitar o convite para ser criado no sistema.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo teamphoria no Painel de Acesso, deverá ser automaticamente inscrito na Teamphoria para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a Teamphoria com Azure AD](https://aad.portal.azure.com/)

