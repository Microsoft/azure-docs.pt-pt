---
title: 'Tutorial: integração do Azure Active Directory com o OpenAthens | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o OpenAthens.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81e37e78246a8c021cfdea015a9c4fe7695f5e44
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933989"
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Tutorial: integração do Azure Active Directory com o OpenAthens

Neste tutorial, você aprenderá a integrar o OpenAthens com o Azure Active Directory (Azure AD).
A integração do OpenAthens ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao OpenAthens.
* Você pode permitir que seus usuários sejam conectados automaticamente ao OpenAthens (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao OpenAthens, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do OpenAthens

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* OpenAthens dá suporte ao SSO iniciado pelo **IDP**

* O OpenAthens dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-openathens-from-the-gallery"></a>Adicionando o OpenAthens da Galeria

Para configurar a integração do OpenAthens ao Azure AD, você precisará adicionar o OpenAthens da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o OpenAthens da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **OpenAthens**, selecione **OpenAthens** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![OpenAthens na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o OpenAthens, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no OpenAthens.

Para configurar e testar o logon único do Azure AD com o OpenAthens, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do OpenAthens](#configure-openathens-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do OpenAthens](#create-openathens-test-user)** – para ter um equivalente de Brenda Simon no OpenAthens que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o OpenAthens, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **OpenAthens** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

5. Na seção **configuração básica do SAML** , carregue o **arquivo de metadados do provedor de serviços**, as etapas para as quais são mencionadas mais adiante neste tutorial.

    a. Clique em **carregar arquivo de metadados**.

    ![openathens carregar metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e clique em **carregar**.

    ![Openathens procurar metadados de carregamento](common/browse-upload-metadata.png)

    c. Depois que o arquivo de metadados for carregado com êxito, o valor do **identificador** obter automaticamente preenchido na caixa de texto seção de **configuração básica do SAML** :

    ![Informações de logon único de domínio e URLs do OpenAthens](common/idp-identifier.png)

6. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download do certificado](common/metadataxml.png)

### <a name="configure-openathens-single-sign-on"></a>Configurar o logon único do OpenAthens

1. Em uma janela diferente do navegador da Web, faça logon em seu site de empresa do OpenAthens como administrador.

2. Selecione **conexões** na lista na guia **Gerenciamento** . 

    ![Configurar o início de sessão único](./media/openathens-tutorial/tutorial_openathens_application1.png)

3. Selecione **SAML 1.1/2.0**e, em seguida, selecione o botão **Configurar** .

    ![Configurar o início de sessão único](./media/openathens-tutorial/tutorial_openathens_application2.png)
    
4. Para adicionar a configuração, selecione o botão **procurar** para carregar o arquivo Metadata. XML que você baixou do portal do Azure e, em seguida, selecione **Adicionar**.

    ![Configurar o início de sessão único](./media/openathens-tutorial/tutorial_openathens_application3.png)

5. Execute as etapas a seguir na guia **detalhes** .

    ![Configurar o início de sessão único](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. Em **mapeamento de nome de exibição**, selecione **usar atributo**.

    b. Na caixa de texto **atributo de nome de exibição** , insira o valor `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Em **mapeamento de usuário exclusivo**, selecione **usar atributo**.

    d. Na caixa de texto **atributo de usuário exclusivo** , insira o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. Em **status**, marque todas as três caixas de seleção.

    f. Em **criar contas locais**, selecione **automaticamente**.

    g. Selecione **salvar alterações**.

    h. Na guia **</>** terceira parte confiável, copie a **URL de metadados** e abra-a no navegador para baixar o arquivo XML de **metadados do SP** . Carregue este arquivo de metadados do SP na seção de **configuração básica do SAML** no Azure AD.

    ![Configurar o início de sessão único](./media/openathens-tutorial/tutorial_openathens_application5.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no portal do Azure chamado Brenda Simon.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.

    ![Os links "usuários e grupos" e "todos os usuários"](common/users.png)

2. Selecione **novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo usuário](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite **brendafernandes\@yourcompanydomain. Extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao OpenAthens.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **OpenAthens**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **OpenAthens**.

    ![O link do OpenAthens na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-openathens-test-user"></a>Criar usuário de teste do OpenAthens

Nesta seção, um usuário chamado Brenda Simon é criado em OpenAthens. O OpenAthens dá suporte ao **provisionamento de usuário just-in-time**, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no OpenAthens, um novo será criado após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do OpenAthens no painel de acesso, você deverá ser conectado automaticamente ao OpenAthens para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

