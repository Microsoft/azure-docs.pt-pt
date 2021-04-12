---
title: 'Tutorial: Integração do Azure Ative Directory com a Plataforma de Produtividade ClickUp | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Plataforma de Produtividade ClickUp.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2021
ms.author: jeedes
ms.openlocfilehash: 29bc02466825aa2ea2c1a9ece2826b1262293392
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285217"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Tutorial: Integração do Azure Ative Directory com a Plataforma de Produtividade ClickUp

Neste tutorial, você vai aprender como integrar a Plataforma de Produtividade ClickUp com O Azure Ative Directory (Azure AD). Quando integra a Plataforma de Produtividade ClickUp com AD Azure, pode:

* Control em Azure AD que tem acesso à Plataforma de Produtividade ClickUp.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Plataforma de Produtividade ClickUp com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada pela Plataforma de Produtividade ClickUp (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* A Plataforma de Produtividade ClickUp suporta sSO iniciado **sp.**

## <a name="add-clickup-productivity-platform-from-the-gallery"></a>Adicionar plataforma de produtividade ClickUp da galeria

Para configurar a integração da Plataforma de Produtividade ClickUp em AD Azure, precisa adicionar a Plataforma de Produtividade ClickUp da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva a Plataforma de Produtividade ClickUp** na caixa de pesquisa.
1. Selecione **a Plataforma de Produtividade ClickUp** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-clickup-productivity-platform"></a>Configure e teste Azure AD SSO para a plataforma de produtividade clickUp

Configure e teste Azure AD SSO com a Plataforma de Produtividade ClickUp usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Plataforma de Produtividade ClickUp.

Para configurar e testar O Azure AD SSO com a Plataforma de Produtividade ClickUp, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Plataforma de Produtividade ClickUp SSO](#configure-clickup-productivity-platform-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. Crie o utilizador de **[teste da Plataforma de Produtividade ClickUp](#create-clickup-productivity-platform-test-user)** - para ter uma contrapartida de B.Simon na Plataforma de Produtividade ClickUp que está ligada à representação AD AZure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações da **Plataforma de Produtividade ClickUp,** encontre a secção **Gerir** e selecione um único sinal **de saúde**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL, digite** o URL: `https://app.clickup.com/login/sso`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > O valor do Identificador não é real. Atualize este valor com o identificador real, que é explicado mais tarde neste tutorial.

5. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

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

Nesta secção, você permitirá que B.Simon use o Azure single sign-on, concedendo acesso à Plataforma de Produtividade ClickUp.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **ClickUp Productivity Platform**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-clickup-productivity-platform-sso"></a>Configure a plataforma de produtividade ClickUp SSO

1. Numa janela diferente do navegador web, inscreva-se no seu inquilino da Plataforma de Produtividade ClickUp como administrador.

2. Clique no **perfil do Utilizador** e, em seguida, selecione **Definições**.

    ![O screenshot mostra o inquilino de produtividade clickUp com o ícone Definições selecionado.](./media/clickup-productivity-platform-tutorial/configure-0.png)

    ![A imagem mostra definições.](./media/clickup-productivity-platform-tutorial/configure-1.png)

3. Selecione **Microsoft**, em Fornecedor de Sign-On (SSO).

    ![A screenshot mostra o painel de autenticação com a Microsoft selecionada.](./media/clickup-productivity-platform-tutorial/configure-2.png)

4. Na **página Configure Microsoft Single Sign On,** execute os seguintes passos:

    ![O screenshot mostra o sinal único do Microsoft configurar na página onde pode copiar a Entidade I D e salvar os metadados U R L da Federação Azure.](./media/clickup-productivity-platform-tutorial/configure-3.png)

    a. Clique em **Copiar** para copiar o valor do ID da Entidade e cole-o na caixa de texto **identifier (Entity ID)** na secção **configuração BÁSICA SAML** no portal Azure.

    b. Na caixa de texto **URL da Federação Azure,** cole o valor do url de metadados da Federação de Aplicações, que copiou a partir do portal Azure e, em seguida, clique em **Guardar**.

5. Para completar a configuração, clique em **Autenticar com a Microsoft para completar a configuração** e autenticar com a conta microsoft.

    ![A screenshot mostra o Authenticate com a Microsoft para completar o botão de configuração.](./media/clickup-productivity-platform-tutorial/configure-4.png)


### <a name="create-clickup-productivity-platform-test-user"></a>Criar utilizador de teste de plataforma de produtividade ClickUp

1. Numa janela diferente do navegador web, inscreva-se no seu inquilino da Plataforma de Produtividade ClickUp como administrador.

2. Clique no **perfil de Utilizador** e, em seguida, selecione **Pessoas**.

    ![A screenshot mostra o inquilino da Produtividade ClickUp.](./media/clickup-productivity-platform-tutorial/configure-0.png)

    ![A screenshot mostra a ligação Pessoas selecionada.](./media/clickup-productivity-platform-tutorial/user-1.png)

3. Introduza o endereço de e-mail do utilizador na caixa de texto e clique em **Convidar**.

    ![O Screenshot mostra as Definições dos Utilizadores da Equipa onde pode convidar as pessoas por e-mail.](./media/clickup-productivity-platform-tutorial/user-2.png)

    > [!NOTE]
    > O utilizador receberá a notificação e deve aceitar o convite para ativar a conta.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de início de sessão da Plataforma de Produtividade ClickUp, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de início de sessão da Plataforma de Produtividade ClickUp e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo da Plataforma de Produtividade ClickUp nas Minhas Apps, este irá redirecionar para o URL de sinal de início da plataforma de produtividade clickUp. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Assim que configurar a Plataforma de Produtividade ClickUp, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).