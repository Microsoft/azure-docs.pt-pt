---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com a AcquireIO  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o AcquireIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 97815e13-32ec-4470-b075-1253f5814f4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01bcc3678485119afae1d567d97eff9dcebe6b95
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714617"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-acquireio"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com a AcquireIO

Neste tutorial, aprenderá a integrar a AcquireIO com o Azure Ative Directory (Azure AD). Quando integrar a AcquireIO com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à AcquireIO.
* Ative que os seus utilizadores sejam automaticamente inscritos na AcquireIO com as suas contas Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Adquirir uma única subscrição (SSO) ativada pela acquireio.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* AcquireIO suporta **IDP** iniciado SSO

## <a name="adding-acquireio-from-the-gallery"></a>Adicionar AcquireIO da galeria

Para configurar a integração da AcquireIO em Azure AD, precisa de adicionar a AcquireIO da galeria à sua lista de aplicações geridas do SaaS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. No Add da secção **galeria,** digite **AcquireIO** na caixa de pesquisa.
1. **Selecione AcquireIO** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-acquireio"></a>Configure e teste Azure AD único sign-on para AcquireIO

Configure e teste Azure AD SSO com AcquireIO utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na AcquireIO.

Para configurar e testar o Azure AD SSO com a AcquireIO, complete os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configure AcquireIO SSO](#configure-acquireio-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Create AcquireIO test user](#create-acquireio-test-user)** - para ter uma contrapartida de B.Simon em AcquireIO que está ligada à representação azure AD do utilizador.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **AcquireIO,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** escreva um URL utilizando o seguinte padrão: `https://app.acquire.io/ad/<acquire_account_uid>`

    > [!NOTE]
    > O valor não é real. Você receberá o URL de resposta real que é explicado mais tarde na secção **Configure AcquireIO** do tutorial. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na secção **Configuração de AcquireIO,** copie os URL(s) adequados com base no seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso à AcquireIO.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **AcquireIO**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-acquireio-sso"></a>Configure AcquireIO SSO

1. Para automatizar a configuração dentro do AcquireIO, é necessário instalar a extensão de **'Sign-in' de Aplicações Seguras,** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **Configurar AcquireIO,** que o direciona para a aplicação AcquireIO. A partir daí, forneça as credenciais de administração para iniciar sessão na AcquireIO. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-6.

    ![Configuração da instalação](common/setup-sso.png)

1. Se pretender configurar o AcquireIO manualmente, numa janela de navegador web diferente, inscreva-se na AcquireIO como Administrador.

1. Do lado esquerdo do menu, clique na **App Store**.

     ![Configuração AcquireIO](./media/acquireio-tutorial/config01.png)

1. Desloque-se até ao **Diretório Ativo** e clique em **Instalar**.

    ![Configuração AcquireIO](./media/acquireio-tutorial/config02.png)

1. No pop-up Ative Directory, execute os seguintes passos:

    ![Configuração AcquireIO](./media/acquireio-tutorial/config03.png)

    a. Clique em **Copiar** para copiar o URL de resposta para a sua instância e cole-o na caixa de texto URL de **resposta** na secção **de configuração Básica SAML** no portal Azure.

    b. Na caixa de texto **login URL,** cola o valor do URL de **Login,** que copiou do portal Azure.

    c. Abra o certificado codificado Base64 no Bloco de Notas, copie o seu conteúdo e cole-o na caixa de texto **x.509.**

    d. Clique em **Ligar agora**.

### <a name="create-acquireio-test-user"></a>Criar utilizador de teste AcquireIO

Para permitir que os utilizadores da Azure AD entrem na AcquireIO, devem ser aprovisionados na AcquireIO. Na AcquireIO, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Numa janela de navegador web diferente, inscreva-se na AcquireIO como Administrador.

1. Do lado esquerdo do menu, clique em **Perfis** e navegue para **Adicionar Perfil**.

     ![Configuração AcquireIO](./media/acquireio-tutorial/config04.png)

1. No pop-up do **cliente Add,** execute os seguintes passos:

    ![Configuração AcquireIO](./media/acquireio-tutorial/config05.png)

    a. Em **Nome** da caixa de texto, introduza o nome de utilizador como **B.simon**.

    b. Na caixa de texto **por e-mail,** introduza o e-mail de utilizador como **B.simon@contoso.com** .

    c. Clique em **Submit** (Submeter).

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no azulejo AcquireIO no Painel de Acesso, deverá ser automaticamente inscrito no AcquireIO para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente adquirir io com a Azure AD](https://aad.portal.azure.com/)
