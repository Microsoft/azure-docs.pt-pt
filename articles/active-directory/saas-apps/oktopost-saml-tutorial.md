---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Oktopost SAML | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Oktopost SAML.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: jeedes
ms.openlocfilehash: e71f991037c0d725a2a03ecd8c1ff4ebbd4d7810
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735938"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oktopost-saml"></a>Tutorial: Azure Ative Directory integração única (SSO) com Oktopost SAML

Neste tutorial, você vai aprender a integrar Oktopost SAML com Azure Ative Directory (Azure AD). Quando integrar o Oktopost SAML com a Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Oktopost SAML.
* Permita que os seus utilizadores sejam automaticamente inscritos no Oktopost SAML com as suas contas AD AZure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Oktopost SAML única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Oktopost SAML suporta **SP e IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.


## <a name="adding-oktopost-saml-from-the-gallery"></a>Adicionar Oktopost SAML da galeria

Para configurar a integração do Oktopost SAML em AD AZure, precisa adicionar Oktopost SAML da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** digite **Oktopost SAML** na caixa de pesquisa.
1. Selecione **Oktopost SAML** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-oktopost-saml"></a>Configure e teste Azure AD SSO para Oktopost SAML

Configure e teste Azure AD SSO com Oktopost SAML usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Oktopost SAML.

Para configurar e testar o Azure AD SSO com oktopost SAML, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure oktopost SAML SSO](#configure-oktopost-saml-sso)** - para configurar as definições de sinalização única no lado da aplicação.
    1. **[Create Oktopost SAML test user](#create-oktopost-saml-test-user)** - para ter uma contraparte de B.Simon em Oktopost SAML que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Oktopost SAML,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure.

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite o URL:  `https://app.oktopost.com/auth/login`


1. Clique em **Guardar**.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **set up Oktopost SAML,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Oktopost SAML.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Oktopost SAML**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-oktopost-saml-sso"></a>Configurar Oktopost SAML SSO

1. Faça login no Oktopost SAML como administrador.

1. Clique nas definições de **> do ícone do utilizador.**

    ![Definições de SAML de Oktopost](./media/oktopost-saml-tutorial/settings.png)

1. Nas **Definições**, aceda à **página de > de assinatura única** e execute os seguintes passos.

    ![Configuração SAML do oktopost](./media/oktopost-saml-tutorial/configure-sso.png)

    a. Selecione **Ativar o único sinal de** **sim**.

    b. Na caixa de texto **SAML Endpoint,** cole o valor URL de **login** que copiou a partir do portal Azure.

    c. Na caixa de texto **emitente,** cole o valor do **identificador Azure AD** que copiou do portal Azure.

    d. Abra o Certificado descarregado **(Base64)** do portal Azure para o Bloco de Notas e cole o conteúdo na caixa de texto **do Certificado X.509.**
    
    e. Clique em **Guardar**.

### <a name="create-oktopost-saml-test-user"></a>Criar utilizador de teste SAML oktopost

1. Faça login no Oktopost SAML como administrador.

1. Clique nas definições de **> do ícone do utilizador.**

    ![Utilizador de teste SAML oktopost1](./media/oktopost-saml-tutorial/settings.png)

1.  Aceda à gestão do **utilizador > utilizadores > adicionar utilizador**.

    ![Utilizador de teste SAML oktopost2](./media/oktopost-saml-tutorial/add-user-1.png)

1. Preencha os campos necessários no pop-up e clique em **Enviar**.

    ![Utilizador de teste SAML oktopost3](./media/oktopost-saml-tutorial/add-user-2.png)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Oktopost SAML Sign on URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de assinatura do Oktopost SAML e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Oktopost SAML para o qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo OKtopost SAML nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Oktopost SAML para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Oktopost SAML, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).