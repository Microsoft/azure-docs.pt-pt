---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Freshservice | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Freshservice.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: fb8b12cdb8fd9ed37ac4086d213183e800d6febc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651765"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Tutorial: Azure Ative Directory integração única (SSO) com Freshservice

Neste tutorial, você vai aprender a integrar Freshservice com Azure Ative Directory (Azure AD). Quando integra o Freshservice com AZure AD, pode:

* Controlo em Azure AD que tem acesso à Freshservice.
* Ative os seus utilizadores a serem automaticamente inscritos na Freshservice com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Inscrição única de serviço fresco (SSO) ativada.

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Freshservice suporta **SSO** iniciado sp

## <a name="add-freshservice-from-the-gallery"></a>Adicione Freshservice da galeria

Para configurar a integração da Freshservice no Azure AD, é necessário adicionar freshservice da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva **Freshservice** na caixa de pesquisa.
1. Selecione **Freshservice** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-freshservice"></a>Configure e teste Azure AD SSO para freshservice

Configure e teste Azure AD SSO com Freshservice utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Freshservice.

Para configurar e testar a Azure AD SSO com Freshservice, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure freshservice SSO](#configure-freshservice-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Freshservice test user](#create-freshservice-test-user)** - para ter uma contraparte de B.Simon em Freshservice que está ligada à representação AD AZure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Freshservice,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<company-name>.freshservice.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<company-name>.freshservice.com`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<company-name>.freshservice.com/login/saml`
    
    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real em URL, Identifier e URL de resposta. Contacte [a equipa de suporte do Cliente Freshservice](https://support.freshservice.com/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **'Configurar o Serviço Fresco'** no **portal Azure,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Freshservice.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Freshservice**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-freshservice-sso"></a>Configure o Serviço Fresco SSO

1. Para automatizar a configuração dentro do Freshservice, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar O Freshservice** irá direcioná-lo para a aplicação Freshservice. A partir daí, forneça as credenciais de administração para assinar na Freshservice. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar o Freshservice manualmente, inicie sessão no site da empresa Freshservice como administrador.

1. No menu à esquerda, clique em **Administração** e selecione **Helpdesk Security** nas **Definições Gerais**.

    ![Administração](./media/freshservice-tutorial/configure-1.png "Administrador")

1. Na **Segurança**, clique em **Ir ao Freshservice 360 Security**.

    ![Segurança](./media/freshservice-tutorial/configure-2.png "Segurança")

1. Na secção **Segurança,** execute os seguintes passos:

    ![Início de Sessão Único](./media/freshservice-tutorial/configure-3.png "Início de Sessão Único")
  
    a. Para **um único sinal ligado**, selecione **On**.

    b. No **Método de Início** de Sessão, selecione **SAML SSO**.

    c. No ID da Entidade fornecido pela caixa de texto **IdP,** cole o valor ID da **Entidade,** que copiou do portal Azure.

    d. Na caixa de texto **URL SSO SAML,** cole o valor URL do **Login,** que copiou a partir do portal Azure.

    e. Nas **Opções de Assinatura**, selecione **Apenas Afirmações Assinadas** a partir do dropdown.

    f. Na caixa **de textos LOGOUT URL,** cole o valor **URL logout,** que copiou a partir do portal Azure.

    exemplo, Na caixa de texto **do Certificado de Segurança,** cole o valor **do Certificado (Base64),** que obteve anteriormente.
  
    h. Clique em **Guardar**.


## <a name="create-freshservice-test-user"></a>Criar utilizador de teste Freshservice

Para permitir que os utilizadores de Azure AD inscrevam-se no FreshService, devem ser adustados no FreshService. No caso do FreshService, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa **FreshService** como administrador.

2. No menu à esquerda, clique em **Administração.**

3. Na secção Gestão do **Utilizador,** clique em **Solicitadores**.

    ![Solicitadores](./media/freshservice-tutorial/create-user-1.png "Solicitadores")

4. Clique em **Novo Solicitador**.

    ![Novos Solicitadores](./media/freshservice-tutorial/create-user-2.png "Novos Solicitadores")

5. Na secção **Novo Solicitador,** insira os campos necessários e clique em **Guardar**.
    ![Novo Solicitador](./media/freshservice-tutorial/create-user-3.png "Novo Solicitador")  

    > [!NOTE]
    > O titular da conta Azure Ative Directory recebe um e-mail incluindo um link para confirmar a conta antes de ficar ativa
    >  

    > [!NOTE]
    > Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador do FreshService ou APIs fornecidas pela FreshService para fornecer contas de utilizador Azure AD.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de login freshservice onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de login freshservice e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Freshservice nas Minhas Apps, deverá ser automaticamente inscrito no Serviço Fresco para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

 Uma vez configurado o Freshservice, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).