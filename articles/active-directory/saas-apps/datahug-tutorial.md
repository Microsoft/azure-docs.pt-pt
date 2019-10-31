---
title: 'Tutorial: integração do Azure Active Directory com o Datahug | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Datahug.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c0dc1ea-7ff4-4554-b60b-0f2fa9f5abaa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9271d8b5657769ce70c46b5e428d995ddc642608
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158442"
---
# <a name="tutorial-azure-active-directory-integration-with-datahug"></a>Tutorial: integração do Azure Active Directory com o Datahug

Neste tutorial, você aprenderá a integrar o Datahug com o Azure Active Directory (Azure AD).
A integração do Datahug ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Datahug.
* Você pode permitir que seus usuários sejam conectados automaticamente ao Datahug (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Datahug, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Datahug

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Datahug dá suporte ao **SP** e ao SSO iniciado pelo **IDP**

## <a name="adding-datahug-from-the-gallery"></a>Adicionando o Datahug da Galeria

Para configurar a integração do Datahug ao Azure AD, você precisará adicionar o Datahug da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Datahug da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Datahug**, selecione **Datahug** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Datahug na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Datahug, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Datahug.

Para configurar e testar o logon único do Azure AD com o Datahug, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Datahug](#configure-datahug-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do Datahug](#create-datahug-test-user)** – para ter um equivalente de Brenda Simon no Datahug que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Datahug, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Datahug** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Datahug](common/idp-intiated.png)

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://apps.datahug.com/identity/<uniqueID>`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://apps.datahug.com/identity/<uniqueID>/acs`

5. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    ![Informações de logon único de domínio e URLs do Datahug](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon** , digite uma URL: `https://apps.datahug.com/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador e a URL de resposta reais. Contate a [equipe de suporte ao cliente do Datahug](https://datahug.com/about/contact-us/) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

6. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de metadados de **Federação** das opções especificadas de acordo com seu requisito e salve-o em seu computador.

    ![O link de download do certificado](common/metadataxml.png)

7. Na seção **certificado de autenticação SAML** , clique no botão **Editar** para abrir a caixa de diálogo **certificado de autenticação SAML** e execute as etapas a seguir.

    ![Editar certificado de autenticação SAML](common/edit-certificate.png)

    a. Selecione **assinar ASSERÇÃO SAML** da **opção de assinatura**.

    b. Selecione **SHA-1** no **algoritmo de assinatura**.
    
    c. Clicar em **Guardar**

    ![Opção de assinatura Communifire](./media/datahug-tutorial/tutorial_datahug_signingoption.png)

8. Na seção **Configurar Datahug** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-datahug-single-sign-on"></a>Configurar o logon único do Datahug

Para configurar o logon único no lado do **Datahug** , é necessário enviar o XML de **metadados de Federação** baixado e as URLs copiadas apropriadas de portal do Azure para a equipe de suporte do [Datahug](https://datahug.com/about/contact-us/). Eles definem essa configuração para que a conexão de SSO do SAML seja definida corretamente em ambos os lados.

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao Datahug.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Datahug**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Datahug**.

    ![O link do Datahug na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-datahug-test-user"></a>Criar usuário de teste do Datahug

Para permitir que os usuários do Azure AD entrem no Datahug, eles devem ser provisionados no Datahug.  
Quando Datahug, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no site da empresa do Datahug como um administrador.

2. Passe o mouse sobre o **engrenagem** no canto superior direito e clique em **configurações**
   
    ![Adicionar funcionário](./media/datahug-tutorial/1.png)

3. Escolha **pessoas** e clique na guia **Adicionar usuários**

    ![Adicionar funcionário](./media/datahug-tutorial/2.png)

4. Digite o email da pessoa para a qual você gostaria de criar uma conta e clique em **Adicionar**.

    ![Adicionar funcionário](./media/datahug-tutorial/3.png)

    > [!NOTE] 
    > Você pode enviar o email de registro ao usuário selecionando a caixa de seleção **Enviar email de boas-vindas** .  
    > Se você estiver criando uma conta para o Salesforce, não envie o email de boas-vindas.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do Datahug no painel de acesso, você deverá ser conectado automaticamente ao Datahug para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

