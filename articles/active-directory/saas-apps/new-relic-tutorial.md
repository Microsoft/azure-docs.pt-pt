---
title: 'Tutorial: Integração do Azure Ative Directory com Nova Relíquia por Conta Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a New Relic by Account.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: jeedes
ms.openlocfilehash: c8d24d0b71ce537b81ee729109b398042c3b60c5
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940962"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic-by-account"></a>Tutorial: Azure Ative Directory integração única (SSO) com Nova Relíquia por Conta

Neste tutorial, você vai aprender a integrar Nova Relíquia por Conta com Azure Ative Direy (Azure AD). Quando integra a Nova Relíquia por Conta com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Nova Relíquia por Conta.
* Permitir que os seus utilizadores sejam automaticamente inscritos na Nova Relíquia por Conta com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Nova subscrição ativada por nova relíquia por conta (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Nova Relíquia por Conta suporta **SSO** iniciado sp

* Uma vez configurado a Nova Relíquia por Conta, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-new-relic-by-account-from-the-gallery"></a>Adicionar nova relíquia por conta da galeria

Para configurar a integração de Nova Relíquia por Conta no AZure AD, é necessário adicionar Nova Relíquia por Conta da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** digite **Nova Relíquia por Conta** na caixa de pesquisa.
1. Selecione **Nova Relíquia por Conta** a partir do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-new-relic-by-account"></a>Configurar e testar Azure AD único sinal de inscrição para Nova Relíquia por Conta

Configure e teste Azure AD SSO com Nova Relíquia por Conta usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Nova Relíquia por Conta.

Para configurar e testar o Azure AD SSO com Nova Relíquia por Conta, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Nova Relíquia por Conta SSO](#configure-new-relic-by-account-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Criar novo utilizador](#create-new-relic-by-account-test-user)** de teste de relíquia por conta - para ter uma contraparte de B.Simon em Nova Relíquia por Conta que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **New Relic by Account,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)
1. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão:

    `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` - Substitua-se `acc_id` pela sua própria Conta ID de Nova Relíquia por Conta.

    b. Na caixa de texto **identifier (Entity ID),** digite um URL: `rpm.newrelic.com`

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Nova Relíquia por Conta,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Nova Relíquia por Conta.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Nova Relíquia por Conta**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-new-relic-by-account-sso"></a>Configure nova relíquia por conta SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa **New Relic by Account** como administrador.

2. No menu em cima, clique em **Definições de Conta**.
   
    ![A screenshot mostra a página Welcome com as definições de Conta selecionadas.](./media/new-relic-tutorial/ic797036.png "Definições de conta")

3. Clique no separador **Segurança e autenticação** e, em seguida, clique **no sinal único no** separador.
   
    ![Único sign-on](./media/new-relic-tutorial/ic797037.png "Início de Sessão Único")

4. Na página de diálogo SAML, execute os seguintes passos:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
    a. Clique **em Escolher Ficheiro** para fazer o upload do certificado de Diretório Azure Ative descarregado.

    b. Na caixa de texto **url de login remoto,** cole o valor do URL de **login,** que copiou do portal Azure.
   
    c. Na caixa de texto **URL de aterragem logout,** cole o valor do **URL logout,** que copiou do portal Azure.

    d. Clique **em Guardar as minhas alterações.**

### <a name="create-new-relic-by-account-test-user"></a>Criar novo utilizador de teste de relíquia por conta

1. Inscreva-se no site da empresa **New Relic by Account** como administrador.

2. No menu em cima, clique em **Definições de Conta**.
   
    ![A screenshot mostra as definições de conta selecionadas na página Welcome.](./media/new-relic-tutorial/ic797040.png "Definições de conta")

3. No painel **conta** no lado esquerdo, clique em **Resumo**e, em seguida, clique em **Adicionar utilizador**.
   
    ![A screenshot mostra o painel resumo onde pode selecionar Adicionar utilizador.](./media/new-relic-tutorial/ic797041.png "Definições de conta")

4. No diálogo dos **utilizadores Ativos,** execute os seguintes passos:
   
    ![Utilizadores Ativos](./media/new-relic-tutorial/ic797042.png "Utilizadores Ativos")
   
    a. Na caixa de texto **do Email,** digite o endereço de e-mail de um utilizador válido do Azure Ative Directory que pretende.

    b. Como **Fun** select **User**.

    c. Clique **em Adicionar este utilizador**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador de Novas Relíquias por Conta fornecidas pela New Relic by Account para fornecer contas de utilizador Azure AD.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar na Nova Relíquia por Conta no Painel de Acesso, deverá ser automaticamente inscrito na Nova Relíquia por Conta para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente nova relíquia por conta com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)