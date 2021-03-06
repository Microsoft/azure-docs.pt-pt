---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com Litmus | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Litmus.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: f2168715d59f9698cba58b7e91bbc897a8cd94f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727289"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmus"></a>Tutorial: Azure Ative Directory integração única (SSO) com Litmus

Neste tutorial, você vai aprender a integrar Litmus com Azure Ative Directory (Azure AD). Quando integrar Litmus com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Litmus.
* Ative os seus utilizadores a serem automaticamente inscritos na Litmus com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Litmus assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Litmus apoia **SP e IDP** iniciado SSO

## <a name="adding-litmus-from-the-gallery"></a>Adicionando Litmus da galeria

Para configurar a integração do Litmus no Azure AD, é necessário adicionar o Litmus da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **escreva Litmus** na caixa de pesquisa.
1. Selecione **Litmus** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-litmus"></a>Configure e teste Azure AD SSO para Litmus

Configure e teste Azure AD SSO com Litmus usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Litmus.

Para configurar e testar a Azure AD SSO com Litmus, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure litmus SSO](#configure-litmus-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Litmus test user](#create-litmus-test-user)** - para ter uma contraparte de B.Simon em Litmus que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Litmus,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure.

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite o URL:  `https://litmus.com/sessions/new`

1. Clique em **Guardar**.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o Certificado **(Raw)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificateraw.png)

1. Na secção **Configurar Litmus,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Litmus.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Litmus**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-litmus-sso"></a>Configurar Litmus SSO

1. Para automatizar a configuração dentro do Litmus, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar Litmus** irá direcioná-lo para a aplicação Litmus. A partir daí, forneça as credenciais de administração para assinar em Litmus. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o Litmus manualmente, numa janela diferente do navegador, inscreva-se no site da empresa Litmus como administrador.

1. Clique na **Segurança** a partir do painel de navegação esquerdo.

    ![A screenshot mostra o item de Segurança selecionado.](./media/litmus-tutorial/security-img.png)

1. Na secção **de Autenticação SAML configurada,** execute os seguintes passos:

    ![A screenshot mostra a secção de autenticação configure SAML onde pode introduzir os valores descritos.](./media/litmus-tutorial/configure1.png)

    a. Ligue o **interruptor ATIVAR SAML.**

    b. Selecione **Genérico** para o fornecedor.

    c. Insira o nome do **Nome do Fornecedor de Identidade.** para ex. `Azure AD`

1. Efetue os seguintes passos:

    ![A imagem mostra a secção onde pode introduzir os valores descritos.](./media/litmus-tutorial/configure3.png)

    a. Na caixa de texto **SAML 2.0 Endpoint (HTTP),** cole o valor URL de **login,** que copiou a partir do portal Azure.

    b. Abra o ficheiro **certificado** descarregado do portal Azure para o Notepad e cole o conteúdo na caixa de texto **do Certificado X.509.**

    c. Clique **em Guardar as definições DE SAML**.

### <a name="create-litmus-test-user"></a>Criar utilizador de teste de Litmus

1. Numa janela diferente do navegador web, inscreva-se na aplicação Litmus como administrador.

1. Clique nas **Contas** a partir do painel de navegação esquerdo.

    ![A screenshot mostra o item contas selecionado.](./media/litmus-tutorial/accounts-img.png)

1. Clique **em Adicionar novo separador de utilizador.**

    ![A screenshot mostra o item Adicionar Novo Utilizador selecionado.](./media/litmus-tutorial/add-new-user.png)

1. Na secção **Adicionar Utilizador,** execute os seguintes passos:

    ![A screenshot mostra a secção 'Adicionar utilizador' onde pode introduzir os valores descritos.](./media/litmus-tutorial/user-profile.png)

    a. Na caixa de sms **do Email,** insira o endereço de e-mail do utilizador como **B.Simon \@ contoso.com**

    b. Na caixa de texto **Name Name,** insira o primeiro nome do utilizador como **B**.

    c. Na caixa de texto **Do Último Nome,** insira o último nome do utilizador como **Simon**.

    d. Clique **em Criar Utilizador.**

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Litmus Sign on URL onde pode iniciar o fluxo de login.

* Vá diretamente ao URL de inscrição de Litmus e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Litmus para o qual configura o SSO

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo litmus nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Litmus para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Litmus, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).