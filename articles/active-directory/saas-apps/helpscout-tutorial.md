---
title: 'Tutorial: integração do Azure Active Directory com o help Scout | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o help Scout.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b71ccbc6cfdb9d3d37fc46b0e932fa98eee2fb43
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159101"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Tutorial: integração do Azure Active Directory com o help Scout

Neste tutorial, você aprenderá a integrar o help Scout ao Azure Active Directory (Azure AD).
A integração do Help Scout ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Help Scout.
* Você pode permitir que seus usuários sejam conectados automaticamente ao Help Scout (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Help Scout, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único do Help Scout

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O help Scout é compatível com o SSO iniciado por **SP e IDP**
* O help Scout dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-help-scout-from-the-gallery"></a>Adicionando o help Scout da Galeria

Para configurar a integração do Help Scout ao Azure AD, você precisará adicionar o help Scout da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Help Scout** na caixa de pesquisa.
1. Selecione **Help Scout** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o help Scout, com base em um usuário de teste chamado **B. Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Help Scout.

Para configurar e testar o logon único do Azure AD com o help Scout, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do Help Scout](#configure-help-scout-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do Help Scout](#create-help-scout-test-user)** – para ter um equivalente de B. Simon no Help Scout que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o help Scout, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Help Scout** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

1. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

1. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Help Scout](common/idp-intiated.png)

    a. O **identificador** é o **URI do público-alvo (ID da entidade do provedor de serviços)** do Help Scout, começa com `urn:`

    b. A **URL de resposta** é a URL de **postback (URL de serviço do consumidor de asserção)** do Help Scout, começa com `https://` 

    > [!NOTE]
    > Os valores nessas URLs são apenas para demonstração. Você precisa atualizar esses valores da URL de resposta e do identificador reais. Você obtém esses valores da guia **logon único** na seção autenticação, que é explicada posteriormente no tutorial.

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    ![Informações de logon único de domínio e URLs do Help Scout](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon** , digite uma URL como: `https://secure.helpscout.net/members/login/`

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download do certificado](common/certificatebase64.png)

1. Na seção **Configurar o help Scout** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no portal do Azure chamado B. Simon.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.

    ![Os links "usuários e grupos" e "todos os usuários"](common/users.png)

2. Selecione **novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo usuário](common/user-properties.png)

    a. No campo **nome** , digite **B. Simon**.
  
    b. No campo **nome de usuário** , digite **B. Simon\@yourcompanydomain. Extension**  
    Por exemplo, B.Simon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao Help Scout.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **ajuda Scout**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Help Scout**.

    ![O link do Help Scout na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-help-scout-sso"></a>Configurar o SSO do Help Scout

1. Para automatizar a configuração no Help Scout, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **Configurar o help Scout** o direcionará para o aplicativo Help Scout. A partir daí, forneça as credenciais de administrador para entrar no Help Scout. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-7.

    ![Configuração da instalação](common/setup-sso.png)

1. Se você quiser configurar o help Scout manualmente, abra uma nova janela do navegador da Web e entre no site da empresa do Help Scout como administrador e execute as seguintes etapas:

1. Clique em **gerenciar** no menu superior e selecione **empresa** no menu suspenso.

    ![Configurar logon único](./media/helpscout-tutorial/settings1.png)

1. Selecione **autenticação** no painel de navegação à esquerda.

    ![Configurar logon único](./media/helpscout-tutorial/settings2.png)

1. Isso levará você para a seção de configurações do SAML e executará as seguintes etapas:

    ![Configurar logon único](./media/helpscout-tutorial/settings3.png)

    a. Copie o valor da **URL de postback (URL do serviço do consumidor de asserção)** e cole o valor na caixa de texto **URL de resposta** na seção **configuração básica do SAML** no portal do Azure.

    b. Copie o valor do **URI do público (ID da entidade do provedor de serviços)** e cole o valor na caixa de texto **identificador** na seção **configuração básica do SAML** no portal do Azure.

1. Alterne **habilitar SAML** em e execute as seguintes etapas:

    ![Configurar logon único](./media/helpscout-tutorial/settings4.png)

    a. Na caixa de texto **URL de logon único** , Cole o valor da **URL de logon**copiado de portal do Azure.

    b. Clique em **carregar certificado** para carregar o **certificado (Base64)** baixado de portal do Azure.

    c. Insira os domínios de email da sua organização e.x.-`contoso.com` na caixa de texto **domínios de email** . Você pode separar vários domínios com uma vírgula. A qualquer momento, um usuário ou administrador do Help Scout que insira esse domínio específico na [página de logon do Help Scout](https://secure.helpscout.net/members/login/) será roteado para o provedor de identidade para autenticar com suas credenciais.

    d. Por fim, você pode alternar a opção **forçar logon SAML** se quiser que os usuários façam logon apenas no Help Scout por meio desse método. Se você ainda deseja deixar a opção de entrar com suas credenciais do Help Scout, você pode deixá-la desativada. Mesmo que esteja habilitado, um proprietário de conta sempre será capaz de fazer logon no Help Scout com a senha da conta.

    e. Clique em **Guardar**.

### <a name="create-help-scout-test-user"></a>Criar usuário de teste do Help Scout

Nesta seção, um usuário chamado B. Simon é criado no Help Scout. O help Scout dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no Help Scout, um novo será criado após a autenticação.

### <a name="test-sso"></a>Testar SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do Help Scout no painel de acesso, você deverá ser conectado automaticamente ao Help Scout para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o help Scout com o Azure AD](https://aad.portal.azure.com/)