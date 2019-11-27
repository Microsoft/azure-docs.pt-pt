---
title: 'Tutorial: integração do Azure Active Directory com o TalentLMS | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TalentLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 970ab9de270b1227884a13ac578d4c439043b20c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233370"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Tutorial: integração do Azure Active Directory com o TalentLMS

Neste tutorial, você aprenderá a integrar o TalentLMS com o Azure Active Directory (Azure AD).
A integração do TalentLMS ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao TalentLMS.
* Você pode permitir que seus usuários sejam conectados automaticamente ao TalentLMS (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao TalentLMS, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do TalentLMS

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O TalentLMS dá suporte ao SSO iniciado por **SP**

## <a name="adding-talentlms-from-the-gallery"></a>Adicionando o TalentLMS da Galeria

Para configurar a integração do TalentLMS ao Azure AD, você precisará adicionar o TalentLMS da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o TalentLMS da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **TalentLMS**, selecione **TalentLMS** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![TalentLMS na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o TalentLMS, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no TalentLMS.

Para configurar e testar o logon único do Azure AD com o TalentLMS, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do TalentLMS](#configure-talentlms-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do TalentLMS](#create-talentlms-test-user)** – para ter um equivalente de Brenda Simon no TalentLMS que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o TalentLMS, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **TalentLMS** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do TalentLMS](common/sp-identifier.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<tenant-name>.TalentLMSapp.com`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contate a [equipe de suporte ao cliente do TalentLMS](https://www.talentlms.com/contact) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Na seção **certificado de autenticação SAML** , clique no botão **Editar** para abrir a caixa de diálogo **certificado de autenticação SAML** .

    ![Editar certificado de autenticação SAML](common/edit-certificate.png)

6. Na seção **certificado de autenticação SAML** , copie a **impressão digital** e salve-a no computador.

    ![Copiar valor de impressão digital](common/copy-thumbprint.png)

7. Na seção **Configurar TalentLMS** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-talentlms-single-sign-on"></a>Configurar o logon único do TalentLMS

1. Em uma janela diferente do navegador da Web, entre no site da empresa do TalentLMS como um administrador.

1. Na seção **configurações de & de conta** , clique na guia **usuários** .

    ![Configurações de & de conta](./media/talentlms-tutorial/IC777296.png "Configurações de & de conta")

1. Clique em **SSO (logon único)** ,

1. Na seção logon único, execute as seguintes etapas:

    ![Logon único](./media/talentlms-tutorial/IC777297.png "Logon único")

    a. Na lista **tipo de integração de SSO** , selecione **SAML 2,0**.

    b. Na caixa de texto **provedor de identidade (IDP)** , Cole o valor do **identificador do Azure ad**que você copiou do portal do Azure.

    c. Cole o valor de **impressão digital** de portal do Azure na caixa de texto **impressão digital do certificado** .

    d.  Na caixa de texto **URL de entrada remota** , Cole o valor da **URL de logon**copiado de portal do Azure.

    e. Na caixa de texto **URL de saída remota** , Cole o valor da **URL de logout**copiado de portal do Azure.

    f. Preencha o seguinte:

    * Na caixa de texto **TargetId** , digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * Na caixa de texto **nome** , digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * Na caixa de texto **sobrenome** , digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * Na caixa de texto **email** , digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao TalentLMS.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **TalentLMS**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **TalentLMS**.

    ![O link do TalentLMS na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-talentlms-test-user"></a>Criar usuário de teste do TalentLMS

Para permitir que os usuários do Azure AD entrem no TalentLMS, eles devem ser provisionados no TalentLMS. No caso do TalentLMS, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no seu locatário do **TalentLMS** .

1. Clique em **usuários**e, em seguida, clique em **Adicionar usuário**.

1. Na página da caixa de diálogo **Adicionar usuário** , execute as seguintes etapas:

    ![Adicionar usuário](./media/talentlms-tutorial/IC777299.png "Adicionar Utilizador")  

    a. Na caixa de texto **nome** , insira o nome do usuário, como **Brenda**.

    b. Na caixa de texto **sobrenome** , insira o sobrenome do usuário, como **Simon**.
 
    c. Na caixa de texto **endereço de email** , insira o email do usuário, como `brittasimon\@contoso.com`.

    d. Clique em **Adicionar usuário**.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação de conta de usuário TalentLMS ou APIs fornecidas pelo TalentLMS para provisionar contas de usuário do Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do TalentLMS no painel de acesso, você deverá ser conectado automaticamente ao TalentLMS para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

