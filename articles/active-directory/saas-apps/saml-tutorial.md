---
title: 'Tutorial: integração de Azure Active Directory com o aplicativo LOB habilitado para token 1,1 do SAML | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e o aplicativo LOB habilitado para token 1,1 do SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ced1d88d-0e48-40d5-9aea-ef991cd9d270
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6de8906f15b170511c5548eeaf13173bd39a2734
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160057"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Tutorial: integração de Azure Active Directory com o aplicativo LOB habilitado para token 1,1 do SAML

Neste tutorial, você aprenderá a integrar o aplicativo LOB habilitado para token 1,1 do SAML com o Azure Active Directory (Azure AD).
A integração do aplicativo LOB habilitado para token 1,1 do SAML com o Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao aplicativo LOB habilitado para token 1,1 do SAML.
* Você pode permitir que seus usuários se conectem automaticamente ao aplicativo LOB habilitado para token 1,1 do SAML (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o aplicativo LOB habilitado para token 1,1 do SAML, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do aplicativo LOB do SAML 1,1 token habilitado

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O aplicativo LOB habilitado para token 1,1 do SAML dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-saml-11-token-enabled-lob-app-from-the-gallery"></a>Adicionando o aplicativo LOB habilitado para token 1,1 do SAML da Galeria

Para configurar a integração do aplicativo LOB habilitado para token 1,1 SAML no Azure AD, você precisa adicionar o aplicativo LOB habilitado para token 1,1 SAML da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o aplicativo LOB habilitado para token 1,1 do SAML por meio da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **aplicativo LOB habilitado para token 1,1 do SAML**, selecione **aplicativo LOB habilitado para token 1,1 do SAML** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Aplicativo LOB habilitado para token 1,1 SAML na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o aplicativo LOB habilitado para token 1,1 do SAML com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no token SAML 1,1 habilitado aplicativo LOB.

Para configurar e testar o logon único do Azure AD com o aplicativo LOB habilitado para token 1,1 do SAML, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do aplicativo LOB habilitado para Token 1,1 do SAML](#configure-saml-11-token-enabled-lob-app-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do aplicativo LOB habilitado para token 1,1 do SAML](#create-saml-11-token-enabled-lob-app-test-user)** – para ter um equivalente de Brenda Simon em SAML 1,1 token habilitado para o aplicativo LOB que está vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o aplicativo LOB habilitado para token 1,1 do SAML, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos de aplicativo **LOB habilitado para token 1,1 do SAML** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do aplicativo LOB habilitado para token 1,1 SAML](common/sp-identifier.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://your-app-url`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `https://your-app-url`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contate a equipe de suporte ao cliente do aplicativo LOB habilitado para token 1,1 do SAML para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download do certificado](common/certificatebase64.png)

6. Na seção **Configurar aplicativo LOB habilitado para Token 1,1 do SAML** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-saml-11-token-enabled-lob-app-single-sign-on"></a>Configurar o logon único do aplicativo LOB habilitado para token 1,1 do SAML

Para configurar o logon único no lado do **aplicativo LOB habilitado para token 1,1 do SAML** , é necessário enviar o **certificado (Base64)** baixado e as URLs copiadas apropriadas de portal do Azure para a equipe de suporte do aplicativo LOB habilitada para o token 1,1 do SAML. Eles definem essa configuração para que a conexão de SSO do SAML seja definida corretamente em ambos os lados.

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao aplicativo LOB habilitado para token 1,1 do SAML.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **aplicativo LOB habilitado para token 1,1 do SAML**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **aplicativo LOB habilitado para Token 1,1 do SAML**.

    ![O link do aplicativo LOB habilitado para token 1,1 do SAML na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-saml-11-token-enabled-lob-app-test-user"></a>Criar usuário de teste do aplicativo LOB habilitado para token 1,1 do SAML

Nesta seção, você criará um usuário chamado Brenda Simon no aplicativo LOB habilitado para token 1,1 do SAML. Trabalhe com a equipe de suporte de aplicativos LOB habilitada para token 1,1 do SAML para adicionar os usuários na plataforma de aplicativo LOB habilitada para o token 1,1 do SAML. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco aplicativo LOB habilitado para token 1,1 do SAML no painel de acesso, você deverá ser conectado automaticamente ao aplicativo LOB habilitado para token 1,1 do SAML para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

