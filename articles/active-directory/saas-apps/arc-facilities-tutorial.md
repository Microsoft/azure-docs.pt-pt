---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com recursos ARC | Microsoft Docs'
description: Saiba como configurar o logon único entre as instalações de Azure Active Directory e ARC.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: aa05894b-fe71-4a68-9c46-839a7cc164d6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43fd1375ac8ffa850ca3f2c4c2b34e63631a9811
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025931"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-arc-facilities"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com instalações ARC

Neste tutorial, você aprenderá a integrar instalações de arco com o Azure Active Directory (Azure AD). Ao integrar instalações de arco com o Azure AD, você pode:

* Controle no Azure AD quem tem acesso aos recursos do ARC.
* Habilite seus usuários a serem automaticamente conectados a instalações ARC com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) de instalações de ARC.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Instalações ARC dão suporte ao SSO iniciado pelo **IDP**

* As instalações ARC dão suporte ao provisionamento **de usuário just-in-time**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-arc-facilities-from-the-gallery"></a>Adicionando instalações ARC da Galeria

Para configurar a integração de instalações ARC ao Azure AD, você precisa adicionar instalações ARC da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite instalações de **arco** na caixa de pesquisa.
1. Selecione **instalações de arco** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-arc-facilities"></a>Configurar e testar o logon único do Azure AD para instalações ARC

Configure e teste o SSO do Azure AD com instalações ARC usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em instalações ARC.

Para configurar e testar o SSO do Azure AD com instalações ARC, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO de instalações de arco](#configure-arc-facilities-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste de instalações de arco](#create-arc-facilities-test-user)** – para ter um equivalente de B. Simon em instalações Arc vinculadas à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Arc Facilities** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , o aplicativo é pré-configurado e as URLs necessárias já foram preenchidas previamente com o Azure. O usuário precisa salvar a configuração clicando no botão **salvar** .

1. O aplicativo de instalações ARC espera que as asserções SAML estejam em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo atributos de usuário.

    ![image](common/edit-attribute.png)

1. Além de acima, o aplicativo de instalações ARC espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **atributos de usuário & declarações** na caixa de diálogo **declarações de grupo (versão prévia)** , execute as seguintes etapas:

    a. Clique na **caneta** ao lado dos **grupos retornados na declaração**.

    ![image](./media/arc-facilities-tutorial/config01.png)

    ![image](./media/arc-facilities-tutorial/config02.png)

    b. Selecione **todos os grupos** na lista de botões de opção.

    c. Selecione o **atributo de origem** da ID do **grupo**.

    d. Clique em **Guardar**.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **configurar instalações Arc** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso a instalações ARC.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **instalações Arc**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-arc-facilities-sso"></a>Configurar SSO de instalações de arco

Para configurar o logon único no lado do **Arc Facilities** , você precisa enviar o **certificado (Base64)** baixado e as URLs copiadas apropriadas de portal do Azure para a [equipe de suporte de instalações Arc](mailto:support@arcfacilities.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-arc-facilities-test-user"></a>Criar usuário de teste de instalações ARC

Nesta seção, um usuário chamado Brenda Simon é criado em instalações ARC. As instalações ARC dão suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir em instalações ARC, um novo será criado após a autenticação.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco instalações ARC no painel de acesso, você deverá entrar automaticamente nos recursos ARC para os quais você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente as instalações de arco com o Azure AD](https://aad.portal.azure.com/)

