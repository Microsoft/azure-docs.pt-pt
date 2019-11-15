---
title: 'Tutorial: integração de Azure Active Directory com o gateway de recompensa | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e recompensar o gateway.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 34336386-998a-4d47-ab55-721d97708e5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: fcd12b0c47dc587153df51d4d30eb2abe4cc2657
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081943"
---
# <a name="tutorial-azure-active-directory-integration-with-reward-gateway"></a>Tutorial: integração de Azure Active Directory com o gateway de recompensa

Neste tutorial, você aprenderá a integrar o recompensa gateway ao Azure Active Directory (Azure AD).
A integração do recompensa gateway ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao gateway de recompensa.
* Você pode permitir que seus usuários se conectem automaticamente para recompensar o gateway (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o recompensa gateway, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Obter assinatura habilitada para logon único do gateway

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O gateway de recompensa dá suporte ao SSO iniciado pelo **IDP**

## <a name="adding-reward-gateway-from-the-gallery"></a>Adicionando o gateway de recompensa da Galeria

Para configurar a integração do gateway de recompensa ao Azure AD, você precisará adicionar o recompensa gateway da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o gateway de recompensa da galeria, execute as seguintes etapas:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **recompensa gateway**, selecione **recompensa gateway** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Recompensa gateway na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o recompensa gateway, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do gateway de recompensa.

Para configurar e testar o logon único do Azure AD com o recompensa gateway, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o logon único do recompensa gateway](#configure-reward-gateway-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar usuário de teste do gateway de recompensa](#create-reward-gateway-test-user)** – para ter um equivalente de Brenda Simon no gateway de recompensa que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o recompensa gateway, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Gateway de recompensa** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na página **Configurar logon único com SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do gateway de recompensa](common/idp-intiated.png)

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão:
    
    | |
    |--|
    | `https://<companyname>.rewardgateway.com`|
    | `https://<companyname>.rewardgateway.co.uk/`|
    | `https://<companyname>.rewardgateway.co.nz/`|
    | `https://<companyname>.rewardgateway.com.au/`|

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:
    
    | |
    |--|
    |  `https://<companyname>.rewardgateway.com/Authentication/EndLogin?idp=<Unique Id>`|
    | `https://<companyname>.rewardgateway.co.uk/Authentication/EndLogin?idp=<Unique Id>`|
    | `https://<companyname>.rewardgateway.co.nz/Authentication/EndLogin?idp=<Unique Id>`|
    | `https://<companyname>.rewardgateway.com.au/Authentication/EndLogin?idp=<Unique Id>`|

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador e a URL de resposta reais. Para obter esses valores, comece a configurar uma integração no portal do gerente de recompensa. Os detalhes podem ser encontrados em https://success.rewardgateway.com/hc/en-us/articles/360038650573-Microsoft-Azure-for-Authentication

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Na seção **Configurar gateway de recompensa** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-reward-gateway-single-sign-on"></a>Configurar o logon único do recompensa gateway

Para configurar o logon único no lado do **recompensa gateway** , comece a configurar uma integração no portal do gerente de recompensa. Use os metadados baixados para obter o certificado de autenticação e carregá-los durante a configuração. Os detalhes podem ser encontrados em https://success.rewardgateway.com/hc/en-us/articles/360038650573-Microsoft-Azure-for-Authentication

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite `brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao gateway de recompensa.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Gateway de recompensa**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **recompensar gateway**.

    ![O link do gateway de recompensa na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-reward-gateway-test-user"></a>Criar usuário de teste do gateway de recompensa

Nesta seção, você criará um usuário chamado Brenda Simon no gateway de recompensa. Trabalhe com a [equipe de suporte do recompensa gateway](mailto:clientsupport@rewardgateway.com) para adicionar os usuários na plataforma do gateway de recompensa. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do gateway de recompensa no painel de acesso, você deverá ser conectado automaticamente ao gateway de recompensa para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
