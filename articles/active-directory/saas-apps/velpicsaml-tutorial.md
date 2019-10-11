---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o SAML do Velpic | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAML Velpic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f699e2244cde53200a9885602bd20c899c0ce4cd
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241567"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-velpic-saml"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com SAML Velpic

Neste tutorial, você aprenderá a integrar o Velpic SAML ao Azure Active Directory (Azure AD). Ao integrar o Velpic SAML ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Velpic SAML.
* Habilite seus usuários a serem conectados automaticamente ao Velpic SAML com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Velpic SAML.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Velpic SAML dá suporte ao SSO iniciado por **SP**

## <a name="adding-velpic-saml-from-the-gallery"></a>Adicionando o Velpic SAML da Galeria

Para configurar a integração do Velpic SAML ao Azure AD, você precisará adicionar o Velpic SAML da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Velpic SAML** na caixa de pesquisa.
1. Selecione **SAML do Velpic** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.    

## <a name="configure-and-test-azure-ad-single-sign-on-for-velpic-saml"></a>Configurar e testar o logon único do Azure AD para o Velpic SAML

Configure e teste o SSO do Azure AD com o Velpic SAML usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Velpic SAML.

Para configurar e testar o SSO do Azure AD com o Velpic SAML, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do SAML](#configure-velpic-saml-sso)** para o Velpic-para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do VELPIC SAML](#create-velpic-saml-test-user)** – para ter um equivalente de B. Simon no SAML Velpic que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Velpic SAML** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<sub-domain>.velpicsaml.net`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Observe que a URL de logon será fornecida pela equipe SAML Velpic e o valor do identificador estará disponível quando você configurar o plug-in de SSO no lado do Velpic SAML. Você precisa copiar esse valor da página do aplicativo Velpic SAML e colá-lo aqui.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/metadataxml.png)

1. Na seção **Configurar o SAML do Velpic** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo usuário** na parte superior da tela.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao Velpic SAML.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **VELPIC SAML**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-velpic-saml-sso"></a>Configurar SSO do SAML do Velpic

1. Para automatizar a configuração no SAML do Velpic, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar o VELPIC SAML** direcionará você para o aplicativo Velpic SAML. A partir daí, forneça as credenciais de administrador para entrar no SAML do Velpic. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-8.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar manualmente o Velpic SAML, abra uma nova janela do navegador da Web e entre no site da sua empresa Velpic SAML como administrador e execute as seguintes etapas:

4. Clique na guia **gerenciar** e vá para a seção **integração** , em que você precisa clicar no botão **plug-ins** para criar um novo plug-in para entrar.

    ![-](./media/velpicsaml-tutorial/velpic_1.png)

5. Clique no botão **' Adicionar plugin '** .
    
    ![-](./media/velpicsaml-tutorial/velpic_2.png)

6. Clique no bloco **SAML** na página Adicionar plug-in.
    
    ![-](./media/velpicsaml-tutorial/velpic_3.png)

7. Insira o nome do novo plug-in SAML e clique no botão **' Adicionar '** .

    ![-](./media/velpicsaml-tutorial/velpic_4.png)

8. Insira os detalhes da seguinte maneira:

    ![-](./media/velpicsaml-tutorial/velpic_5.png)

    a. Na caixa de texto **nome** , digite o nome do plug-in SAML.

    b. Na caixa de texto **URL do emissor** , Cole o **identificador do Azure ad** que você copiou da janela **Configurar logon** do portal do Azure.

    c. Na **configuração de metadados do provedor** , carregue o arquivo XML de metadados que você baixou de portal do Azure.

    d. Você também pode optar por habilitar o provisionamento just in time do SAML habilitando a caixa de seleção **' auto criar novos usuários '** . Se um usuário não existir no Velpic e esse sinalizador não estiver habilitado, o logon do Azure falhará. Se o sinalizador estiver habilitado, o usuário será provisionado automaticamente no Velpic no momento do logon. 

    e. Copie a **URL de logon único** da caixa de texto e cole-a na portal do Azure.
    
    f. Clique em **Guardar**.

### <a name="create-velpic-saml-test-user"></a>Criar usuário de teste do Velpic SAML

Normalmente, essa etapa não é necessária, pois o aplicativo dá suporte ao provisionamento de usuário just-in-time. Se o provisionamento automático de usuário não estiver habilitado, a criação manual de usuário poderá ser feita conforme descrito abaixo.

Entre no site da empresa do Velpic SAML como um administrador e execute as seguintes etapas:
    
1. Clique na guia gerenciar e vá para a seção usuários e, em seguida, clique no botão novo para adicionar usuários.

    ![Adicionar usuário](./media/velpicsaml-tutorial/velpic_7.png)

2. Na página da caixa de diálogo **"criar novo usuário"** , execute as etapas a seguir.

    ![Usuário](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. Na caixa de texto **nome** , digite o primeiro nome de B.

    b. Na caixa de texto **sobrenome** , digite o sobrenome de Simon.

    c. Na caixa de texto **nome de usuário** , digite o nome de usuário B. Simon.

    d. Na caixa de texto **email** , digite o endereço de email da conta B.Simon@contoso.com.

    e. O restante das informações é opcional, você pode preenchê-la se necessário.
    
    f. Clique em **GUARDAR**.

## <a name="test-sso"></a>Testar SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

1. Ao clicar no bloco SAML do Velpic no painel de acesso, você deverá obter a página de logon do aplicativo Velpic SAML. Você deve ver o botão **"fazer logon com o Azure AD"** na página de entrada.

    ![-](./media/velpicsaml-tutorial/velpic_6.png)

1. Clique no botão **"fazer logon com o Azure AD"** para fazer logon no Velpic usando sua conta do Azure AD.

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o SAML do Velpic com o Azure AD](https://aad.portal.azure.com/)

