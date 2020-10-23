---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Arnês / Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Harness.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.openlocfilehash: d6a6c8b49582b34c2603e0ddf78b76736f97c183
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92445618"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-harness"></a>Tutorial: Azure Ative Directy integração única (SSO) com Arnês

Neste tutorial, você vai aprender a integrar Harness com Azure Ative Direy (Azure AD). Quando integrar o Harness com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Harness.
* Permita que os seus utilizadores sejam automaticamente inscritos no Harness com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Arreio de assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Harness suporta **SP e IDP** iniciado SSO

## <a name="adding-harness-from-the-gallery"></a>Adicionando arnês da galeria

Para configurar a integração do Harness em Azure AD, precisa adicionar aRnês da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite arnês** na caixa de pesquisa.
1. Selecione **o Arnês** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-harness"></a>Configurar e testar Azure AD único sinal de inscrição para arnês

Configure e teste Azure AD SSO com arnês usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Arnês.

Para configurar e testar o Azure AD SSO com arnês, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Arnês SSO](#configure-harness-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Harness test user](#create-harness-test-user)** - para ter uma contraparte de B.Simon in Harness que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Harness,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://app.harness.io/gateway/api/users/saml-login?accountId=<harness_account_id>`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL:  `https://app.harness.io/`

    > [!NOTE]
    > O valor URL de resposta não é real. Você receberá o URL de resposta real da secção **Configure Harness SSO,** que é explicado mais tarde no tutorial. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção Configurar o **Arnês,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Harness.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Harness**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-harness-sso"></a>Configurar arnês SSO

1. Para automatizar a configuração dentro do Arnês, é necessário instalar a extensão do **navegador 'As aplicações' Secure's,** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Setup Harness** irá direcioná-lo para a aplicação Harness. A partir daí, forneça as credenciais de administração para assinar em Harness. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o Harness manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Harness como administrador e execute os seguintes passos:

4. No topo-direito da página, clique em **Definições de**Autenticação contínua  >  **de gestão de acesso a**segurança  >  **Authentication Settings**.

    ![Screenshot que mostra o menu "Segurança Contínua" com "Gestão de Acesso" e "Definições de autenticação" selecionados.](./media/harness-tutorial/configure01.png)

5. Na secção **SSO Providers,** clique em **+ Adicionar Fornecedores SSO**  >  **SAML**.

    ![Screenshot que mostra os "S S O Providers" com "+ Add S S O Providers - S A M L" selecionados.](./media/harness-tutorial/configure03.png)

6. No pop-up **do SAML Provider,** execute os seguintes passos:

    ![Screenshot que mostra o pop-up "S A M L Provider" com os campos "U R L" e "Display Name" realçados, e os botões "Escolha Ficheiro" e "Enviar" selecionados.](./media/harness-tutorial/configure02.png)

    a. Copie o **No seu Provedor SSO, por favor, ative o login baseado em SAML e, em seguida, introduza a seguinte** instância URL e cole-a na caixa de texto URL resposta na secção de **configuração SAML básica** no portal Azure.

    b. Na caixa de texto **'Mostrar' 'Mostrar',** digite o nome do visor.

    c. Clique **em Escolher o ficheiro** para carregar o ficheiro XML dos Metadados da Federação, que descarregou a partir do Azure AD.

    d. Clique **em SUBMETER.**

### <a name="create-harness-test-user"></a>Criar utilizador de teste de arnês

Para permitir que os utilizadores de Azure AD inscrevam-se no Harness, devem ser a provisionados no Arnês. Em Arnês, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no Harness como administrador.

1. No topo-direito da página, clique em **Utilizadores de**  >  **Gestão**de Acesso de Segurança Contínua  >  **Users**.

    ![Screenshot que mostra o menu "Segurança Contínua" com "Gestão de Acesso" e "Utilizadores" selecionados.](./media/harness-tutorial/configure04.png)

1. No lado direito da página, clique em **+ Adicionar Utilizador**.

    ![Screenshot que mostra a página "Utilizadores" com a ação "+ Adicionar Utilizador" selecionada.](./media/harness-tutorial/configure05.png)

1. No pop-up **do Utilizador Add,** execute os seguintes passos:

    ![Configuração do arnês](./media/harness-tutorial/configure06.png)

    a. Na caixa de texto **do Endereço de E-mail(es), insira** o e-mail do utilizador como `B.simon@contoso.com` .

    b. Selecione os **grupos de utilizador.**

    c. Clique **em Submeter.**

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no telha do Arnês no Painel de Acesso, deverá ser automaticamente inscrito no Arnês para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente arnês com Azure AD](https://aad.portal.azure.com/)