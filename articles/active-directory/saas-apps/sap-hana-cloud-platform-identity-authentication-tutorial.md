---
title: 'Tutorial: integração do Azure Active Directory com a autenticação de identidade da SAP Cloud Platform | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e a autenticação de identidade da SAP Cloud Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 196de1cacb977305c778e16182b21c8dcfafe13e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161167"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Tutorial: integração do Azure Active Directory com a autenticação de identidade da SAP Cloud Platform

Neste tutorial, você aprenderá a integrar a autenticação de identidade da SAP Cloud Platform com o Azure Active Directory (Azure AD).
A integração da autenticação de identidade da SAP Cloud Platform ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso à autenticação de identidade da SAP Cloud Platform.
* Você pode permitir que seus usuários sejam automaticamente conectados à autenticação de identidade da SAP Cloud Platform (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com a autenticação de identidade da SAP Cloud Platform, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único da autenticação de identidade da SAP Cloud Platform

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* A autenticação de identidade da SAP Cloud Platform dá suporte ao **SP** e ao SSO iniciado pelo **IDP**

Antes de se aprofundar nos detalhes técnicos, é vital entender os conceitos que serão examinados. O Serviços de Federação do Active Directory (AD FS) e a autenticação de identidade da SAP Cloud Platform permitem que você implemente o SSO em aplicativos ou serviços protegidos pelo AD do Azure (como um IdP) com aplicativos e serviços SAP protegidos pela SAP Cloud Autenticação de identidade da plataforma.

Atualmente, a autenticação de identidade da SAP Cloud Platform atua como um provedor de identidade de proxy para aplicativos SAP. Azure Active Directory, por sua vez, atua como o principal provedor de identidade nesta configuração. 

O diagrama a seguir ilustra essa relação:

![Criando um usuário de teste do Azure AD](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Com essa configuração, seu locatário de autenticação de identidade da SAP Cloud Platform é configurado como um aplicativo confiável no Azure Active Directory.

Todos os aplicativos e serviços SAP que você deseja proteger dessa maneira são posteriormente configurados no console de gerenciamento de autenticação de identidade da SAP Cloud Platform.

Portanto, a autorização para conceder acesso a aplicativos e serviços SAP precisa ocorrer na autenticação de identidade da SAP Cloud Platform (em vez de Azure Active Directory).

Ao configurar a autenticação de identidade da SAP Cloud Platform como um aplicativo por meio do Azure Active Directory Marketplace, você não precisa configurar declarações individuais ou asserções SAML.

> [!NOTE]
> Atualmente, somente o SSO da Web foi testado por ambas as partes. Os fluxos necessários para a comunicação de aplicativo para API ou API para API devem funcionar, mas ainda não foram testados. Eles serão testados durante as atividades subsequentes.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Adicionando a autenticação de identidade da SAP Cloud Platform da Galeria

Para configurar a integração da autenticação de identidade da SAP Cloud Platform ao Azure AD, você precisa adicionar a autenticação de identidade da SAP Cloud Platform da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar a autenticação de identidade da SAP Cloud Platform por meio da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **autenticação de identidade da SAP Cloud Platform**, selecione **autenticação de identidade da SAP Cloud Platform** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Autenticação de identidade da SAP Cloud Platform na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o [nome do aplicativo], com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do [nome do aplicativo].

Para configurar e testar o logon único do Azure AD com o [nome do aplicativo], você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar logon único de autenticação de identidade da SAP Cloud Platform](#configure-sap-cloud-platform-identity-authentication-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste de autenticação de identidade da SAP Cloud Platform](#create-sap-cloud-platform-identity-authentication-test-user)** – para ter um equivalente de Brenda Simon na autenticação de identidade da SAP Cloud Platform que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o [nome do aplicativo], execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos de **autenticação de identidade da SAP Cloud Platform** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , se você quiser configurar no modo iniciado **IDP** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs da autenticação de identidade da SAP Cloud Platform](common/idp-intiated.png)

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `<IAS-tenant-id>.accounts.ondemand.com`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador e a URL de resposta reais. Contate a [equipe de suporte ao cliente de autenticação de identidade da SAP Cloud Platform](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) para obter esses valores. Se você não entender o valor do identificador, leia a documentação de autenticação de identidade da SAP Cloud Platform sobre a [configuração do SAML 2,0 do locatário](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    ![Informações de logon único de domínio e URLs da autenticação de identidade da SAP Cloud Platform](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Esse valor não é real. Atualize esse valor com a URL de logon real. Use a URL de logon do aplicativo de negócios específica. Entre em contato com a [equipe de suporte ao cliente de autenticação de identidade da SAP Cloud Platform](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) se tiver alguma dúvida.

6. O aplicativo de autenticação de identidade da SAP Cloud Platform espera as asserções SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos na seção **atributos de usuário** na página de integração de aplicativos. Na página **Configurar logon único com SAML** , clique no botão **Editar** para abrir a caixa de diálogo **atributos de usuário** .

    ![imagem](common/edit-attribute.png)

7. Se seu aplicativo SAP espera um atributo como **FirstName**, adicione o atributo **FirstName** na seção **declarações do usuário** na caixa de diálogo **atributos de usuário** , configure o atributo de token SAML, conforme mostrado na imagem acima, e execute o seguintes etapas:

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![imagem](common/new-save-attribute.png)

    ![imagem](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo FirstName.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , selecione o valor de atributo User. excertoname.

    f. Clique em **OK**

    g. Clique em **Guardar**.

8. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **XML de metadados** das opções especificadas de acordo com seu requisito e salve-o em seu computador.

    ![O link de download do certificado](common/metadataxml.png)

9. Na seção **Configurar a autenticação de identidade da SAP Cloud Platform** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-sap-cloud-platform-identity-authentication-single-sign-on"></a>Configurar logon único de autenticação de identidade da SAP Cloud Platform

1. Para que o SSO seja configurado para seu aplicativo, vá para o console de administração de autenticação de identidade da SAP Cloud Platform. A URL tem o seguinte padrão: `https://<tenant-id>.accounts.ondemand.com/admin`. Em seguida, leia a documentação sobre autenticação de identidade da SAP Cloud Platform em [integração com o Microsoft Azure ad](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html).

2. No portal do Azure, selecione o botão **salvar** .

3. Continue com o seguinte somente se você quiser adicionar e habilitar o SSO para outro aplicativo SAP. Repita as etapas na seção **adicionando autenticação de identidade da SAP Cloud Platform da Galeria**.

4. Na portal do Azure, na página de integração de aplicativos de **autenticação de identidade da SAP Cloud Platform** , selecione **logon vinculado**.

    ![Configurar logon vinculado](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Guarde a configuração.

> [!NOTE]
> O novo aplicativo aproveita a configuração de logon único do aplicativo SAP anterior. Certifique-se de usar os mesmos provedores de identidade corporativa no console de administração de autenticação de identidade da SAP Cloud Platform.

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso à autenticação de identidade da SAP Cloud Platform.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **autenticação de identidade da SAP Cloud Platform**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **autenticação de identidade da SAP Cloud Platform**.

    ![O link de autenticação de identidade da SAP Cloud Platform na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Criar usuário de teste de autenticação de identidade da SAP Cloud Platform

Você não precisa criar um usuário na autenticação de identidade da SAP Cloud Platform. Os usuários que estão no armazenamento de usuários do Azure AD podem usar a funcionalidade SSO.

A autenticação de identidade da SAP Cloud Platform dá suporte à opção de Federação de identidade. Essa opção permite que o aplicativo Verifique se os usuários autenticados pelo provedor de identidade corporativa existem no repositório de usuários da autenticação de identidade da SAP Cloud Platform.

A opção de Federação de identidade está desabilitada por padrão. Se a Federação de identidades estiver habilitada, somente os usuários que forem importados na autenticação de identidade da SAP Cloud Platform poderão acessar o aplicativo.

Para obter mais informações sobre como habilitar ou desabilitar a Federação de identidades com a autenticação de identidade da SAP Cloud Platform, consulte "Habilitar Federação de identidade com autenticação de identidade da SAP Cloud Platform" em [Configurar Federação de identidade com o repositório de usuários da autenticação de identidade da SAP Cloud Platform](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco de autenticação de identidade da SAP Cloud Platform no painel de acesso, você deverá ser conectado automaticamente à autenticação de identidade da SAP Cloud Platform para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
