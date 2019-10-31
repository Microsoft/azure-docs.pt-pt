---
title: 'Tutorial: integração do Azure Active Directory com o lifesize Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o lifesize Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e886a1fa3f590ac94dbf088520e6770690ee21ac
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159570"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Tutorial: integração do Azure Active Directory com o lifesize Cloud

Neste tutorial, você aprenderá a integrar o lifesize Cloud ao Azure Active Directory (Azure AD).
A integração do lifesize Cloud ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao lifesize Cloud.
* Você pode permitir que seus usuários façam logon automaticamente no lifesize Cloud (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao lifesize Cloud, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do lifesize Cloud

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O lifesize Cloud dá suporte ao SSO iniciado por **SP**

* O lifesize Cloud dá suporte ao provisionamento **automatizado** de usuários

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Adicionando o lifesize Cloud da Galeria

Para configurar a integração do lifesize Cloud ao Azure AD, você precisará adicionar o lifesize Cloud da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o lifesize Cloud da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **lifesize Cloud**, selecione **lifesize Cloud** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Lifesize Cloud na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o lifesize Cloud, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do lifesize Cloud.

Para configurar e testar o logon único do Azure AD com o lifesize Cloud, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do lifesize Cloud](#configure-lifesize-cloud-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do lifesize Cloud](#create-lifesize-cloud-test-user)** – para ter um equivalente de Brenda Simon no lifesize Cloud que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o lifesize Cloud, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **lifesize Cloud** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do lifesize Cloud](common/sp-identifier-relay.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://login.lifesizecloud.com/ls/?acs`

    b. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://login.lifesizecloud.com/<companyname>`

    c. Clique em **definir URLs adicionais**.

    d. Na caixa de texto **estado de retransmissão** , digite uma URL usando o seguinte padrão: `https://webapp.lifesizecloud.com/?ent=<identifier>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de logon, o identificador e o estado de retransmissão reais. Contate a [equipe de suporte ao cliente do lifesize Cloud](https://www.lifesize.com/en/support) para obter a URL de logon e os valores de identificador e você pode obter o valor do estado de retransmissão da configuração de SSO que é explicada posteriormente no tutorial. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download do certificado](common/certificatebase64.png)

6. Na seção **Configurar a nuvem do lifesize** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-lifesize-cloud-single-sign-on"></a>Configurar o logon único do lifesize Cloud

1. Para que o SSO seja configurado para seu aplicativo, faça logon no aplicativo lifesize Cloud com privilégios de administrador.

2. No canto superior direito, clique em seu nome e, em seguida, clique nas **Configurações avançadas**.

    ![Configurar logon único](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

3. Nas configurações avançadas, agora clique no link **configuração de SSO** . Ele abrirá a página de configuração de SSO para sua instância.

    ![Configurar logon único](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

4. Agora, configure os seguintes valores na interface do usuário de configuração de SSO.

    ![Configurar logon único](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)

    a. Na caixa de texto **emissor do provedor de identidade** , Cole o valor do identificador do **Azure ad** que você copiou do portal do Azure.

    b.  Na caixa de texto **URL de logon** , Cole o valor da **URL de logon** que você copiou do portal do Azure.

    c. Abra seu certificado codificado em base 64 no bloco de notas baixado de portal do Azure, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **certificado X. 509** .
  
    d. Nos mapeamentos de atributo SAML para a caixa de texto nome, insira o valor como `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    e. No mapeamento de atributo SAML para a caixa de texto **sobrenome** , insira o valor como `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    f. No mapeamento de atributo SAML para a caixa de texto de **email** , insira o valor como `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

5. Para verificar a configuração, você pode clicar no botão **testar** .

    >[!NOTE]
    >Para testes bem-sucedidos, você precisa concluir o assistente de configuração no Azure AD e também fornecer acesso a usuários ou grupos que podem executar o teste.

6. Habilite o SSO verificando o botão **habilitar SSO** .

7. Agora, clique no botão **Atualizar** para que todas as configurações sejam salvas. Isso irá gerar o valor de Relaystate. Copie o valor de Relaystate, que é gerado na caixa de texto, Cole-o na TextBox **estado de retransmissão** na seção **domínio e URLs do lifesize Cloud** .

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao lifesize Cloud.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **lifesize Cloud**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **lifesize Cloud**.

    ![O link do lifesize Cloud na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-lifesize-cloud-test-user"></a>Criar usuário de teste do lifesize Cloud

Nesta seção, você criará um usuário chamado Brenda Simon no lifesize Cloud. O lifesize Cloud oferece suporte ao provisionamento automático de usuário. Após a autenticação bem-sucedida no Azure AD, o usuário será provisionado automaticamente no aplicativo.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do lifesize Cloud no painel de acesso, você deverá obter a página de logon do aplicativo lifesize Cloud. Aqui, você precisa inserir seu nome de usuário e, depois disso, será redirecionado para a página inicial do aplicativo.

Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
