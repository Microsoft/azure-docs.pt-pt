---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com o inchannel | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o inchannel.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c3546eab-96b5-489b-a309-b895eb428053
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4adc22982c8c7fa7b7a856ded01f88ee548bde93
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121971"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicechannel"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com o inchannel

Neste tutorial, você aprenderá a integrar o perchannel com o Azure Active Directory (Azure AD). Ao integrar o inchannel ao Azure AD, você pode:

* Controle no Azure AD que tem acesso ao inchannel.
* Habilite seus usuários a serem conectados automaticamente ao perchannel com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do antichannel.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O antichannel dá suporte ao SSO iniciado pelo **IDP**
* O antichannel dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-servicechannel-from-the-gallery"></a>Adicionando o inchannel da Galeria

Para configurar a integração do onchannel ao Azure AD, você precisa adicionar o inchannel da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **inchannel** na caixa de pesquisa.
1. Selecione o **inchannel** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-servicechannel"></a>Configurar e testar o logon único do Azure AD para o inchannel

Configure e teste o SSO do Azure AD com o inchannel usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no inchannel.

Para configurar e testar o SSO do Azure AD com o inchannel, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do antichannel](#configure-servicechannel-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do inchannel](#create-servicechannel-test-user)** – para ter um equivalente de B. Simon no inchannel que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **perchannel** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite o valor como:`http://adfs.<domain>.com/adfs/service/trust`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:`https://<customer domain>.servicechannel.com/saml/acs`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador e a URL de resposta reais. Aqui, sugerimos que você use o valor exclusivo da cadeia de caracteres no identificador. Contate a [equipe de suporte ao cliente do inchannel](https://servicechannel.zendesk.com/hc/en-us) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. A declaração de função é pré-configurada para que você não precise configurá-la, mas ainda precisa criá-las no Azure AD usando este [artigo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management). Você pode consultar o guia do inchannel [aqui](https://servicechannel.zendesk.com/hc/articles/217514326-Azure-AD-Configuration-Example) para obter mais diretrizes sobre declarações.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar o perchannel** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao inchannel.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **inchannel**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-servicechannel-sso"></a>Configurar o SSO do antichannel

Para configurar o logon único no lado do **onchannel** , é necessário enviar o **certificado (Base64)** baixado e as URLs copiadas apropriadas do portal do Azure para a [equipe de suporte do inchannel](https://servicechannel.zendesk.com/hc/en-us). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-servicechannel-test-user"></a>Criar usuário de teste do inchannel

O aplicativo dá suporte ao provisionamento de usuário just-in-time e, após a autenticação, os usuários serão criados automaticamente no aplicativo. Para o provisionamento de usuário completo, entre em contato com a [equipe de suporte do inchannel](https://servicechannel.zendesk.com/hc/).

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do enchannel no painel de acesso, você deverá ser conectado automaticamente ao próprio inchannel para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o inchannel com o Azure AD](https://aad.portal.azure.com/)