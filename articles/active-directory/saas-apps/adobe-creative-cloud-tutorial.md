---
title: 'Tutorial: integração do Azure Active Directory com o Adobe Creative Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f3fdd1aa4fa655cfffb9e9a2f29d551133613a8
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73154007"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Tutorial: integração do Azure Active Directory com o Adobe Creative Cloud

Neste tutorial, você aprenderá a integrar o Adobe Creative Cloud ao Azure Active Directory (AD do Azure).
A integração do Adobe Creative Cloud ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Adobe Creative Cloud.
* Você pode permitir que seus usuários sejam automaticamente conectados ao Adobe Creative Cloud (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Adobe Creative Cloud, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Adobe Creative Cloud

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* Adobe Creative Cloud dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Adicionando Adobe Creative Cloud da Galeria

Para configurar a integração do Adobe Creative Cloud ao Azure AD, você precisará adicionar o Adobe Creative Cloud da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Adobe Creative Cloud da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Adobe Creative Cloud**, selecione **Adobe Creative Cloud** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Adobe Creative Cloud na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Adobe Creative Cloud, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Adobe Creative Cloud.

Para configurar e testar o logon único do Azure AD com o Adobe Creative Cloud, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Adobe Creative Cloud](#configure-adobe-creative-cloud-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar Adobe Creative Cloud usuário de teste](#create-adobe-creative-cloud-test-user)** – para ter um equivalente de Brenda Simon no Adobe Creative Cloud que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Adobe Creative Cloud, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Adobe Creative Cloud** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Adobe Creative Cloud](common/sp-identifier.png)

    a. Na caixa de texto **URL de logon** , digite o valor como: `https://adobe.com`.

    b. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > O valor do identificador não é real. Atualize esse valor com o identificador real. Contate a [equipe de suporte ao cliente do Adobe Creative Cloud](https://www.adobe.com/au/creativecloud/business/teams/plans.html) para obter esse valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Adobe Creative Cloud aplicativo espera que as asserções SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos na seção **atributos de usuário** na página de integração de aplicativos. Na página **Configurar logon único com SAML** , clique no botão **Editar** para abrir a caixa de diálogo **atributos de usuário** .

    ![imagem](common/edit-attribute.png)

6. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , configure o atributo de token SAML, conforme mostrado na imagem acima, e execute as seguintes etapas:

    | Nome | Atributo de origem|
    |----- | --------- |
    | firstName | User. excertoname |
    | lastName | User. sobrenome |
    | E-mail | User. mail

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![imagem](common/new-save-attribute.png)

    ![imagem](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **OK**

    g. Clique em **Guardar**.

    > [!NOTE]
    > Os usuários precisam ter uma licença válida do Office 365 ExO para que o valor de declaração de email seja preenchido na resposta SAML.

7. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download do certificado](common/certificatebase64.png)

8. Na seção **configurar Adobe Creative Cloud** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-adobe-creative-cloud-single-sign-on"></a>Configurar o logon único do Adobe Creative Cloud

1. Em uma janela diferente do navegador da Web, entre no [console do administrador do Adobe](https://adminconsole.adobe.com) como administrador.

2. Vá para **configurações** na barra de navegação superior e escolha **identidade**. A lista de domínios é aberta. Clique em **Configurar** link em seu domínio. Em seguida, execute as etapas a seguir na seção **configuração de logon único necessária** . Para obter mais informações, consulte [configurar um domínio](https://helpx.adobe.com/enterprise/using/set-up-domain.html)

    ![Definições](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "Definições")

    a. Clique em **procurar** para carregar o certificado baixado do Azure ad para o **certificado IDP**.

    b. Na caixa de texto **emissor do IDP** , coloque o valor da **ID da entidade SAML** que você copiou da seção **Configurar logon** no portal do Azure.

    c. Na caixa de texto **URL de logon do IDP** , coloque o valor da URL do serviço de SSO do **SAML** que você copiou da seção **Configurar logon** no portal do Azure.

    d. Selecione **http-redirecionar** como **Associação IDP**.

    e. Selecione **endereço de email** como **configuração de logon do usuário**.

    f. Clique no botão **salvar** .

3. Agora, o painel apresentará o arquivo XML **"baixar metadados"** . Ele contém a URL do EntityDescriptor da Adobe e a URL do AssertionConsumerService. Abra o arquivo e configure-os no aplicativo do Azure AD.

    ![Configurar o logon único no lado do aplicativo](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Use o valor EntityDescriptor Adobe fornecido para o **identificador** na caixa de diálogo **definir configurações do aplicativo** .

    b. Use o valor AssertionConsumerService Adobe fornecido para a **URL de resposta** na caixa de diálogo **definir configurações do aplicativo** .

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao Adobe Creative Cloud.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Adobe Creative Cloud**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Adobe Creative Cloud**.

    ![O link Adobe Creative Cloud na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-adobe-creative-cloud-test-user"></a>Criar Adobe Creative Cloud usuário de teste

Para permitir que os usuários do AD do Azure façam logon no Adobe Creative Cloud, eles devem ser provisionados no Adobe Creative Cloud. No caso do Adobe Creative Cloud, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1. Entre no site do [console do administrador do Adobe](https://adminconsole.adobe.com) como administrador.

2. Adicione o usuário no console da Adobe como ID federada e atribua-os a um perfil de produto. Para obter informações detalhadas sobre como adicionar usuários, consulte [Adicionar usuários no console de administração do Adobe](https://helpx.adobe.com/enterprise/using/users.html#Addusers) 

3. Neste ponto, digite seu endereço de email/UPN no formulário de entrada da Adobe, pressione TAB e você deve ser federado de volta ao Azure AD:
   * Acesso via Web: www\.adobe.com > entrar
   * No utilitário de aplicativo da área de trabalho > entrar
   * No aplicativo > Ajuda > entrar

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco Adobe Creative Cloud no painel de acesso, você deverá ser conectado automaticamente à Adobe Creative Cloud para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
  
- [Configurar um domínio (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
  
- [Configurar o Azure para uso com o Adobe SSO (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)
