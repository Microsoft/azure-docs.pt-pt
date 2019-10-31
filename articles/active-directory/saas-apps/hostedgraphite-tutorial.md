---
title: 'Tutorial: integração do Azure Active Directory com o Hosted Graphite | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Hosted Graphite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a1ac4d7f-d079-4f3c-b6da-0f520d427ceb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6c5b689d00bd1adad820043840c43f49666655c
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158073"
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Tutorial: integração do Azure Active Directory com o Hosted Graphite

Neste tutorial, você aprenderá a integrar o Hosted Graphite ao Azure Active Directory (Azure AD).
A integração do Hosted Graphite ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Hosted Graphite.
* Você pode permitir que seus usuários façam logon automaticamente no Hosted Graphite (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Hosted Graphite, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Graphite hospedado

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Graphite hospedado dá suporte a **SP** iniciado pelo
* O Graphite hospedado dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-hosted-graphite-from-the-gallery"></a>Adicionar o Hosted Graphite da Galeria

Para configurar a integração do Hosted Graphite ao Azure AD, você precisará adicionar o Hosted Graphite da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Hosted Graphite da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Hosted Graphite**, selecione **Hosted Graphite** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Graphite hospedados na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Hosted Graphite, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Hosted Graphite.

Para configurar e testar o logon único do Azure AD com o Hosted Graphite, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Hosted Graphite](#configure-hosted-graphite-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do Hosted Graphite](#create-hosted-graphite-test-user)** – para ter um equivalente de Brenda Simon no Hosted Graphite que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Hosted Graphite, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Hosted Graphite** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Graphite hospedado](common/idp-intiated.png)

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://www.hostedgraphite.com/metadata/<user id>`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://www.hostedgraphite.com/complete/saml/<user id>`

5. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    ![Informações de logon único de domínio e URLs do Graphite hospedado](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://www.hostedgraphite.com/login/saml/<user id>/`

    > [!NOTE]
    > Observe que esses não são os valores reais. Você precisa atualizar esses valores com o identificador, a URL de resposta e a URL de logon reais. Para obter esses valores, você pode acessar a configuração do SAML do Access-> no lado do aplicativo ou entrar em contato com a [equipe de suporte do Hosted Graphite](mailto:help@hostedgraphite.com).

6. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download do certificado](common/certificatebase64.png)

7. Na seção **Configurar o Graphite hospedado** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-hosted-graphite-single-sign-on"></a>Configurar o logon único do Hosted Graphite

1. Faça logon em seu locatário do Graphite hospedado como administrador.

2. Vá para a **página de instalação do SAML** na barra lateral (**configuração do Access-> SAML**).

    ![Configurar o logon único no lado do aplicativo](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

3. Confirme se essas URls correspondem à sua configuração feita na seção **configuração básica do SAML** do portal do Azure.

    ![Configurar o logon único no lado do aplicativo](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

4. Nas caixas de entrada **entidade ou ID do emissor** e **URL de logon SSO** , Cole o valor do **identificador do Azure ad** e a **URL de logon** que você copiou do portal do Azure.

    ![Configurar o logon único no lado do aplicativo](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)

5. Selecione **somente leitura** como **função de usuário padrão**.

    ![Configurar o logon único no lado do aplicativo](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

6. Abra seu certificado codificado em base 64 no bloco de notas baixado de portal do Azure, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **certificado X. 509** .

    ![Configurar o logon único no lado do aplicativo](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

7. Clique no botão **salvar** .

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo-lhe acesso ao Hosted Graphite.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Hosted Graphite**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Hosted Graphite**.

    ![O link Graphite hospedado na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-hosted-graphite-test-user"></a>Criar usuário de teste do Hosted Graphite

Nesta seção, um usuário chamado Brenda Simon é criado no Hosted Graphite. O Graphite hospedado dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no Hosted Graphite, um novo será criado após a autenticação.

> [!NOTE]
> Se precisar criar um usuário manualmente, você precisará entrar em contato com a equipe de suporte do Graphite hospedado via <mailto:help@hostedgraphite.com>.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco Hosted Graphite no painel de acesso, você deverá ser automaticamente conectado ao Graphite hospedado para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

