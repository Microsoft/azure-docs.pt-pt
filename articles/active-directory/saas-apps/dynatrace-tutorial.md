---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o dynaTrace | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o dynaTrace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 267ad37f-feda-4fac-bd15-7610174caf45
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ba96dd2fcfb995afa6e3b1302a2c8b075abfd90
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968702"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-dynatrace"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o dynaTrace

Neste tutorial, você aprenderá a integrar o dynaTrace com o Azure Active Directory (Azure AD). Ao integrar o dynaTrace ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao dynaTrace.
* Habilite seus usuários a serem conectados automaticamente ao dynaTrace com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do dynaTrace.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O dynaTrace dá suporte ao **SP e** ao SSO iniciado pelo IDP
* O dynaTrace dá suporte ao provisionamento **de usuário just in time**

> [!NOTE]
> O identificador deste aplicativo é um valor de cadeia de caracteres fixo. Somente uma instância pode ser configurada em um locatário.

## <a name="adding-dynatrace-from-the-gallery"></a>Adicionando o dynaTrace da Galeria

Para configurar a integração do dynaTrace ao Azure AD, você precisará adicionar o dynaTrace da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **dynaTrace** na caixa de pesquisa.
1. Selecione **dynaTrace** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-dynatrace"></a>Configurar e testar o logon único do Azure AD para o dynaTrace

Configure e teste o SSO do Azure AD com o dynaTrace usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no dynaTrace.

Para configurar e testar o SSO do Azure AD com o dynaTrace, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do dynaTrace](#configure-dynatrace-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do dynaTrace](#create-dynatrace-test-user)** – para ter um equivalente de B. Simon em dynaTrace que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **dynaTrace** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , o aplicativo é pré-configurado no modo iniciado pelo **IDP** e as URLs necessárias já foram preenchidas previamente com o Azure. O usuário precisa salvar a configuração clicando no botão **salvar** .

1. Clique em **definir URLs adicionais** e conclua a etapa a seguir para configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma url: `https://sso.dynatrace.com/`

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , encontre XML de metadados de **Federação**. Selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/metadataxml.png)

1. Na seção **certificado de autenticação SAML** , selecione o botão **Editar** para abrir a caixa de diálogo **certificado de autenticação SAML** . Conclua os seguintes passos:

    ![Editar certificado de autenticação SAML](common/edit-certificate.png)

    a. A configuração da **opção de assinatura** é preenchida previamente. Verifique as configurações de acordo com sua organização.

    b. Clique em **Guardar**.

    ![Opção de assinatura Communifire](./media/dynatrace-tutorial/tutorial-dynatrace-signing-option.png)

1. Na seção **Configurar dynaTrace** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao dynaTrace.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **dynaTrace**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-dynatrace-sso"></a>Configurar o SSO do dynaTrace

Para configurar o logon único no lado do **dynaTrace** , é necessário enviar o arquivo XML de **metadados de Federação** baixado e as URLs copiadas apropriadas do portal do Azure para a [equipe de suporte do dynaTrace](https://www.dynatrace.com/services-support/). Eles definem essa configuração para que a conexão de SSO do SAML seja definida corretamente em ambos os lados.

### <a name="create-dynatrace-test-user"></a>Criar usuário de teste do dynaTrace

Nesta seção, um usuário chamado B. Simon é criado em dynaTrace. O dynaTrace dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no dynaTrace, um novo será criado após a autenticação.

## <a name="test-sso"></a>Testar SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do dynaTrace no painel de acesso, você deverá ser conectado automaticamente ao dynaTrace, para o qual você configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o dynaTrace com o Azure AD](https://aad.portal.azure.com/)
