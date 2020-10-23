---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Kanbanize Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Kanbanize.
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
ms.openlocfilehash: 05ce0d3d3d5c66514edc07446aaf0a879657c10c
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92459242"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kanbanize"></a>Tutorial: Azure Ative Directory integração única (SSO) com Kanbanize

Neste tutorial, você vai aprender a integrar Kanbanize com Azure Ative Directory (Azure AD). Quando integrar o Kanbanize com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Kanbanize.
* Permita que os seus utilizadores sejam automaticamente inscritos no Kanbanize com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Kanbanize assinatura única ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Kanbanize apoia **SP e IDP** iniciado SSO
* Kanbanize suporta **provisão de** utilizadores just in time

## <a name="adding-kanbanize-from-the-gallery"></a>Adicionando Kanbanize da galeria

Para configurar a integração do Kanbanize no Azure AD, precisa adicionar Kanbanize da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **da galeria,** **escreva Kanbanize** na caixa de pesquisa.
1. **Selecione Kanbanize** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-kanbanize"></a>Configurar e testar Azure AD único sinal para Kanbanize

Configure e teste Azure AD SSO com Kanbanize usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Kanbanize.

Para configurar e testar o Azure AD SSO com kanbanize, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Kanbanize SSO](#configure-kanbanize-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Kanbanize test user](#create-kanbanize-test-user)** - para ter uma contraparte de B.Simon em Kanbanize que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Kanbanize,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

     a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.kanbanize.com/`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.kanbanize.com/saml/acs`

    c. Clique **em Definir URLs adicionais**.

    d. Na caixa de texto **do Estado de Retransmissão,** digite um URL: `/ctrl_login/saml_login`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<subdomain>.kanbanize.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte a [equipa de apoio ao Cliente Kanbanize](mailto:support@ms.kanbanize.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação Kanbanize espera as afirmações DO SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem seguinte mostra a lista de atributos predefinidos, onde como identificador de nome é mapeado com **user.userprincipalname**. A aplicação Kanbanize espera que o identificador de nomes seja mapeado com **o user.mail**, pelo que é necessário editar o mapeamento do atributo clicando no ícone Editar e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Kanbanize,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Kanbanize.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, **selecione Kanbanize**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-kanbanize-sso"></a>Configure Kanbanize SSO

1. Para automatizar a configuração dentro do Kanbanize, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar Kanbanize** irá direcioná-lo para a aplicação Kanbanize. A partir daí, forneça as credenciais de administração para assinar em Kanbanize. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração](common/setup-sso.png)

3. Se quiser configurar o Kanbanize manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Kanbanize como administrador e execute os seguintes passos:

4. Vá para o topo direito da página, clique no logotipo **Definições.**

    ![Configurações kanbanize](./media/kanbanize-tutorial/tutorial-kanbanize-set.png)

5. Na página do painel da Administração a partir do lado esquerdo do menu clique em **Integrações** e, em seguida, ative **o Sign-On único**.

    ![A screenshot mostra o painel de Administração com integração selecionada.](./media/kanbanize-tutorial/tutorial-kanbanize-admin.png)

6. Na secção Integrações, clique em **CONFIGURAR** para abrir a página **de Integração Sign-On Única.**

    ![Config kanbanize](./media/kanbanize-tutorial/tutorial-kanbanize-config.png)

7. Na página **de Integração de Sign-On única** em **Configurações,** execute os seguintes passos:

    ![A screenshot mostra a página de Integração Sign-On Única onde introduz os valores neste passo.](./media/kanbanize-tutorial/tutorial-kanbanize-save.png)

    a. Na caixa de texto **Idp Entity ID,** cole o valor do **Identificador AD Azure,** que copiou a partir do portal Azure.

    b. Na caixa de texto do **Idp Login Endpoint,** cole o valor do URL de **Login,** que copiou a partir do portal Azure.

    c. Na caixa de texto **Idp Logout Endpoint,** cole o valor do **URL logout,** que copiou a partir do portal Azure.

    d. No nome do Atributo para caixa de texto **de email,** insira este valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. No nome do Atributo para caixa de texto **name,** insira este valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. No nome do Atributo para caixa de texto **apelido,** insira este valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    > [!Note]
    > Pode obter estes valores combinando valores de nomespace e nome do respetivo atributo da secção atributos do Utilizador no portal Azure.

    exemplo, No Bloco de Notas, abra o certificado codificado base-64 que descarregou a partir do portal Azure, copie o seu conteúdo (sem os marcadores de arranque e fim) e, em seguida, cole-o na caixa de **Certificado Idp X.509.**

    h. Verifique **o início de sSO e o Kanbanize.**

    i. Clique **em Guardar Definições**.

### <a name="create-kanbanize-test-user"></a>Criar utilizador de teste Kanbanize

Nesta secção, um utilizador chamado B.Simon é criado em Kanbanize. O Kanbanize suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Kanbanize, um novo é criado após a autenticação. Se precisar de criar um utilizador manualmente, contacte a [equipa de suporte do Cliente Kanbanize.](mailto:support@ms.kanbanize.com)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Kanbanize no Painel de Acesso, deverá ser automaticamente inscrito no Kanbanize para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente Kanbanize com Azure AD](https://aad.portal.azure.com/)