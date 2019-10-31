---
title: 'Tutorial: integração de Azure Active Directory com meus pontos de prêmio principal sub/equipe superior | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e meus pontos de prêmio na parte superior/superior da equipe.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a7a08eed-7a6b-4a83-8f8e-0add6d2fb8cf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: b1de9e394dd1e6b4c47b8de3175dd1e8fba1d87e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161334"
---
# <a name="tutorial-azure-active-directory-integration-with-my-award-points-top-subtop-team"></a>Tutorial: integração de Azure Active Directory com meus pontos de prêmio principal sub/equipe superior

Neste tutorial, você aprenderá a integrar meus pontos de premiação principais da nossa equipe principal com o Azure Active Directory (Azure AD).
A integração de meus pontos de premiação principais da equipe principal com o Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso aos meus pontos principais da equipe principal/principal.
* Você pode permitir que seus usuários entrem automaticamente em meus pontos de prêmio na parte superior/superior da equipe (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com meus pontos de prêmio na primeira/parte superior da equipe, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Meu prêmio meus pontos principais assinatura habilitada para logon único da equipe principal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* Meus pontos de premiação da equipe principal/superior dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-my-award-points-top-subtop-team-from-the-gallery"></a>Adicionando meus pontos principais da equipe principal/principal da Galeria

Para configurar a integração dos meus pontos de prêmio na parte superior/superior da equipe ao Azure AD, você precisa adicionar meus pontos principais da equipe principal/principal da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar meus pontos de prêmio na parte superior/superior da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **meus pontos de prêmio na parte superior/superior da equipe**, selecione **meus pontos principais da equipe principal/superior** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Meu prêmio meus pontos principais da equipe principal/superior na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com meus pontos de prêmio na parte superior/superior da equipe, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em meus pontos de prêmio na parte superior/superior da equipe.

Para configurar e testar o logon único do Azure AD com meus pontos de prêmio na parte superior/superior da equipe, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **Configure meus pontos de prêmio início do logon único da equipe principal/superior** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **Criar meus pontos de premiação principais do usuário de teste da equipe principal** – para ter um equivalente de Brenda Simon em meus pontos de prêmio na parte superior/superior da equipe vinculada à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com meus pontos de prêmio na primeira/parte superior da equipe, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos **principais da equipe do meu prêmio meus pontos** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Meu prêmio aponta para as principais informações de logon único de domínio e URLs da equipe principal](common/sp-signonurl.png)

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://microsoftrr.performnet.com/biwv1auth/Shibboleth.sso/Login?providerId=<Azure AD Identifier>`

    > [!NOTE]
    > O valor não é real. Você obterá o valor `<Azure AD Identifier>` em etapas posteriores neste tutorial.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download do certificado](common/metadataxml.png)

6. Na seção **Configurar meus pontos de prêmio na parte superior/superior da equipe** , copie as URLs apropriadas de acordo com seu requisito. 

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

    >[!NOTE]
    >Acrescente o valor do identificador do Azure AD copiado com a URL de logon no lugar de `<Azure AD Identifier>` na seção **configuração básica do SAML** no portal do Azure.

### <a name="configure-my-award-points-top-subtop-team-single-sign-on"></a>Configurar meus pontos de prêmio início do logon único da equipe principal/principal

Para configurar o logon único no lado do **meu prêmio no primeiro/início da equipe** , é necessário enviar o XML de **metadados de Federação** baixado e as URLs copiadas apropriadas do portal do Azure para a [minha equipe de suporte da equipe principal/principal do meu prêmio](mailto:myawardpoints@biworldwide.com). Eles definem essa configuração para que a conexão de SSO do SAML seja definida corretamente em ambos os lados.

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso aos meus pontos principais da equipe principal/principal.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **meus pontos de prêmio na parte superior/superior da equipe**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **meus pontos de premiação na parte superior/superior da equipe**.

    ![O link do meu prêmio meus pontos principais/principais da equipe na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-my-award-points-top-subtop-team-test-user"></a>Criar meus pontos de prêmio principais usuário de teste da equipe principal

Nesta seção, você criará um usuário chamado Brenda Simon em meus pontos de prêmio na parte superior/superior da equipe. Trabalhe com [minha equipe de suporte da equipe principal/principal do meu prêmio](mailto:myawardpoints@biworldwide.com) para adicionar os usuários nos meus pontos de prêmio na principal plataforma de equipe superior/superior. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Quando você clica no bloco meu prêmio meus pontos na parte superior/superior da equipe no painel de acesso, você deve entrar automaticamente no meu prêmio meus pontos na parte superior/superior da equipe para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
