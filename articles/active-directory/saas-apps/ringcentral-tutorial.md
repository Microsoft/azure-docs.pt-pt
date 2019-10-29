---
title: 'Tutorial: integração do Azure Active Directory com o RingCentral | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: de7cf57d177902efdbb44524703481e8c65c75c5
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991476"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Tutorial: integrar o RingCentral ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o RingCentral com o Azure Active Directory (Azure AD). Ao integrar o RingCentral ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao RingCentral.
* Habilite seus usuários a serem conectados automaticamente ao RingCentral com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do RingCentral.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* RingCentral dá suporte ao SSO iniciado pelo **IDP**

## <a name="adding-ringcentral-from-the-gallery"></a>Adicionando o RingCentral da Galeria

Para configurar a integração do RingCentral ao Azure AD, você precisará adicionar o RingCentral da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **RingCentral** na caixa de pesquisa.
1. Selecione **RingCentral** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Configure e teste o SSO do Azure AD com o RingCentral usando um usuário de teste chamado **Brenda Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no RingCentral.

Para configurar e testar o SSO do Azure AD com o RingCentral, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do RingCentral](#configure-ringcentral-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do RingCentral](#create-ringcentral-test-user)** – para ter um equivalente de B. Simon em RingCentral que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **RingCentral** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:

    1. Clique em **carregar arquivo de metadados**.
    1. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e clique em **carregar**.
    1. Depois que o arquivo de metadados for carregado com êxito, os valores do **identificador** e da **URL de resposta** serão preenchidos automaticamente na seção **configuração básica do SAML** .

    > [!Note]
    > Você Obtém o **arquivo de metadados do provedor de serviços** na página de configuração do SSO do RingCentral, que é explicada posteriormente no tutorial.

1. Se você não tiver um **arquivo de metadados do provedor de serviços**, insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL:

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. Na caixa de texto **URL de resposta** , digite uma URL:

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique no botão Copiar para copiar a URL de **metadados de Federação do aplicativo** e salvá-la no computador.

    ![O link de download do certificado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado Brenda Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo usuário** na parte superior da tela.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao RingCentral.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **RingCentral**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-ringcentral-sso"></a>Configurar o SSO do RingCentral

1. Para automatizar a configuração no RingCentral, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **Configurar o RingCentral** irá direcioná-lo para o aplicativo RingCentral. A partir daí, forneça as credenciais de administrador para entrar no RingCentral. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-7.

    ![Configuração da instalação](common/setup-sso.png)

1. Se você quiser configurar o RingCentral manualmente, abra uma nova janela do navegador da Web e entre no site da empresa do RingCentral como administrador e execute as seguintes etapas:

1. Na parte superior, clique em **ferramentas**.

    ![imagem](./media/ringcentral-tutorial/ringcentral1.png)

1. Navegue até **logon único**.

    ![imagem](./media/ringcentral-tutorial/ringcentral2.png)

1. Na página **logon único** , na seção configuração de **SSO** , na **etapa 1** , clique em **Editar** e execute as seguintes etapas:

    ![imagem](./media/ringcentral-tutorial/ringcentral3.png)

1. Na página **Configurar logon único** , execute as seguintes etapas:

    ![imagem](./media/ringcentral-tutorial/ringcentral4.png)

    a. Clique em **procurar** para carregar o arquivo de metadados que você baixou de portal do Azure.

    b. Depois de carregar os metadados, os valores são preenchidos automaticamente na seção **informações gerais de SSO** .

    c. Na seção **mapeamento de atributo** , selecione **mapear atributo de email** como `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Clique em **Guardar**.

    e. Na **etapa 2** , clique em **baixar** para baixar o **arquivo de metadados do provedor de serviços** e carregá-lo na seção **configuração básica do SAML** para preencher automaticamente o **identificador** e os valores da **URL de resposta** em portal do Azure.

    ![imagem](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Na mesma página, navegue até a seção **habilitar SSO** e execute as seguintes etapas:

    ![imagem](./media/ringcentral-tutorial/ringcentral5.png)

    * Selecione **habilitar serviço SSO**.

    * Selecione **permitir que os usuários façam logon com SSO ou credencial RingCentral**.

    * Clique em **Guardar**.

### <a name="create-ringcentral-test-user"></a>Criar usuário de teste do RingCentral

Nesta seção, você criará um usuário chamado Brenda Simon no RingCentral. Trabalhe com a [equipe de suporte ao cliente do RingCentral](https://success.ringcentral.com/RCContactSupp) para adicionar os usuários na plataforma do RingCentral. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-sso"></a>Testar SSO

Ao selecionar o bloco RingCentral no painel de acesso, você deverá entrar automaticamente no RingCentral para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o RingCentral com o Azure AD](https://aad.portal.azure.com/)