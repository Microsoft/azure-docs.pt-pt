---
title: 'Tutorial: integração do Azure Active Directory com o MobileIron | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o MobileIron.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 3e4bbd5b-290e-4951-971b-ec0c1c11aaa2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea4e997c6f2d0826c8914c671d625cc1c49bb018
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160456"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Tutorial: integração do Azure Active Directory com o MobileIron

Neste tutorial, você aprenderá a integrar o MobileIron com o Azure Active Directory (Azure AD).
A integração do MobileIron ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao MobileIron.
* Você pode permitir que seus usuários sejam conectados automaticamente ao MobileIron (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao MobileIron, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do MobileIron

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O MobileIron dá suporte ao **SP e** ao SSO iniciado pelo IDP

## <a name="adding-mobileiron-from-the-gallery"></a>Adicionando o MobileIron da Galeria

Para configurar a integração do MobileIron ao Azure AD, você precisará adicionar o MobileIron da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o MobileIron da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **MobileIron**, selecione **MobileIron** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![MobileIron na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o MobileIron, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no MobileIron.

Para configurar e testar o logon único do Azure AD com o MobileIron, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do MobileIron](#configure-mobileiron-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do MobileIron](#create-mobileiron-test-user)** – para ter um equivalente de Brenda Simon no MobileIron que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o MobileIron, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **MobileIron** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IDP** :

    ![Informações de logon único de domínio e URLs do MobileIron](common/idp-intiated.png)

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://www.mobileiron.com/<key>`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<host>.mobileiron.com/saml/SSO/alias/<key>`

    c. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    ![Informações de logon único de domínio e URLs do MobileIron](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<host>.mobileiron.com/user/login.html`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Você obterá os valores de chave e host do portal administrativo do MobileIron, que é explicado posteriormente no tutorial.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download do certificado](common/metadataxml.png)

### <a name="configure-mobileiron-single-sign-on"></a>Configurar o logon único do MobileIron

1. Em uma janela diferente do navegador da Web, faça logon em seu site de empresa do MobileIron como administrador.

2. Vá para **administrador** > **identidade** e selecione a opção **AAD** no campo **informações no Cloud IDP configuração** .

    ![Botão configurar administrador de logon único](./media/mobileiron-tutorial/tutorial_mobileiron_admin.png)

3. Copie os valores da **chave** e do **host** e cole-os para concluir as URLs na seção **configuração básica do SAML** em portal do Azure.

    ![Botão configurar administrador de logon único](./media/mobileiron-tutorial/key.png)

4. No **campo exportar arquivo de metadados do AAD e importar para o MobileIron Cloud,** clique em **escolher arquivo** para carregar os metadados baixados de portal do Azure. Clique em **concluído** depois de carregado.

    ![Botão configurar metadados do administrador de logon único](./media/mobileiron-tutorial/tutorial_mobileiron_adminmetadata.png)

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao MobileIron.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **MobileIron**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **MobileIron**.

    ![O link do MobileIron na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-mobileiron-test-user"></a>Criar usuário de teste do MobileIron

Para permitir que os usuários do Azure AD façam logon no MobileIron, eles devem ser provisionados no MobileIron.  
No caso do MobileIron, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do MobileIron como administrador.

1. Vá para **usuários** e clique em **Adicionar** > **único usuário**.

    ![Botão configurar usuário de logon único](./media/mobileiron-tutorial/tutorial_mobileiron_user.png)

1. Na página da caixa de diálogo **"usuário único"** , execute as seguintes etapas:

    ![Botão configurar usuário de logon único](./media/mobileiron-tutorial/tutorial_mobileiron_useradd.png)

    a. Na caixa de texto **endereço de email** , insira o email do usuário, como brittasimon@contoso.com.

    b. Na caixa de texto **nome** , insira o nome do usuário, como Brenda.

    c. Na caixa de texto **sobrenome** , insira o sobrenome do usuário, como Simon.

    d. Clique em **Concluído**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do MobileIron no painel de acesso, você deverá ser conectado automaticamente ao MobileIron para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

