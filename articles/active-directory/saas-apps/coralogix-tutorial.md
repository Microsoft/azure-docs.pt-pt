---
title: 'Tutorial: integração do Azure Active Directory com o Coralogix | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Coralogix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba79bfc1-992e-4924-b76a-8eb0dfb97724
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/2/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8589c366c029ab51c7cd740a1b63cff7c0481a51
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158462"
---
# <a name="tutorial-azure-active-directory-integration-with-coralogix"></a>Tutorial: integração do Azure Active Directory com o Coralogix

Neste tutorial, você aprenderá a integrar o Coralogix com o Azure Active Directory (Azure AD).
A integração do Coralogix ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Coralogix.
* Você pode permitir que seus usuários sejam conectados automaticamente ao Coralogix (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central: o portal do Azure.

Para obter mais informações sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Coralogix, você precisará dos seguintes itens:

- Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma [avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
- Uma assinatura habilitada para logon único do Coralogix. 

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Coralogix dá suporte ao SSO iniciado por SP.

## <a name="add-coralogix-from-the-gallery"></a>Adicionar o Coralogix da Galeria

Para configurar a integração do Coralogix ao Azure AD, primeiro adicione o Coralogix da Galeria à sua lista de aplicativos SaaS gerenciados.

Para adicionar o Coralogix da galeria, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Coralogix**. Selecione **Coralogix** no painel de resultados e, em seguida, selecione o botão **Adicionar** para adicionar o aplicativo.

     ![Coralogix na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Coralogix, com base em um usuário de teste chamado Brenda Simon.
Para que o logon único funcione, você precisa estabelecer um vínculo entre um usuário do Azure AD e o usuário relacionado no Coralogix.

Para configurar e testar o logon único do Azure AD com o Coralogix, primeiro conclua os seguintes blocos de construção:

1. [Configure o logon único do Azure ad](#configure-azure-ad-single-sign-on) para habilitar seus usuários a usar esse recurso.
2. [Configure o logon único do Coralogix](#configure-coralogix-single-sign-on) para definir as configurações de logon único no lado do aplicativo.
3. [Crie um usuário de teste do Azure ad](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com o Brenda Simon.
4. [Atribua o usuário de teste do Azure ad](#assign-the-azure-ad-test-user) para permitir que o Brenda Simon use o logon único do Azure AD.
5. [Crie um usuário de teste do Coralogix](#create-a-coralogix-test-user) para ter um equivalente de Brenda Simon no Coralogix que esteja vinculado à representação do usuário no Azure AD.
6. [Teste o logon único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Coralogix, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Coralogix** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione **SAML** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , selecione o ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na caixa de diálogo **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Coralogix](common/sp-identifier.png)

    a. Na caixa **URL de logon** , insira uma URL com o seguinte padrão: `https://<SUBDOMAIN>.coralogix.com`

    b. Na caixa de texto **identificador (ID da entidade)** , insira uma URL, como:
    
    `https://api.coralogix.com/saml/metadata.xml`

    ou

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize o valor com a URL de logon real. Contate a [equipe de suporte ao cliente do Coralogix](mailto:info@coralogix.com) para obter o valor. Você também pode consultar os padrões na seção **configuração básica do SAML** no portal do Azure.

5. O aplicativo Coralogix espera as asserções SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos na seção **atributos de usuário** na página de integração de aplicativos. Na página **Configurar logon único com SAML** , selecione o botão **Editar** para abrir a caixa de diálogo **atributos de usuário** .

    ![imagem](common/edit-attribute.png)

6. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , edite as declarações usando o ícone **Editar** . Você também pode adicionar as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML, conforme mostrado na imagem anterior. Em seguida, execute as seguintes etapas:
    
    a. Selecione o **ícone Editar** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    imagem de ![](./media/coralogix-tutorial/tutorial_usermail.png) ![de imagem](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. Na lista **escolher formato do identificador de nome** , selecione **endereço de email**.

    c. Na lista **atributo de origem** , selecione **User. mail**.

    d. Selecione **Guardar**.

7. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , selecione **baixar** para baixar o XML de metadados de **Federação** das opções fornecidas de acordo com seus requisitos. Em seguida, salve-o em seu computador.

    ![O link de download do certificado](common/metadataxml.png)

8. Na seção **Configurar Coralogix** , copie as URLs apropriadas.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-coralogix-single-sign-on"></a>Configurar o logon único do Coralogix

Para configurar o logon único no lado do **Coralogix** , envie o XML de **metadados de Federação** baixado e as URLs copiadas do portal do Azure para a [equipe de suporte do Coralogix](mailto:info@coralogix.com). Eles garantem que a conexão de SSO do SAML seja definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no portal do Azure chamado Brenda Simon.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.

    ![Os links "usuários e grupos" e "todos os usuários"](common/users.png)

2. Na parte superior da tela, selecione **novo usuário**.

    ![Botão novo usuário](common/new-user.png)

3. Na caixa de diálogo **usuário** , execute as etapas a seguir.

    ![A caixa de diálogo usuário](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite "brittasimon@yourcompanydomain.extension". Por exemplo, nesse caso, você pode inserir "brittasimon@contoso.com".

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao Coralogix.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Coralogix**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Coralogix**.

    ![O link do Coralogix na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Selecione o botão **Adicionar usuário** . Em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista de usuários. Em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista. Em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , selecione o botão **atribuir** .

### <a name="create-a-coralogix-test-user"></a>Criar um usuário de teste do Coralogix

Nesta seção, você criará um usuário chamado Brenda Simon no Coralogix. Trabalhe com a [equipe de suporte do Coralogix](mailto:info@coralogix.com) para adicionar os usuários na plataforma do Coralogix. Você deve criar e ativar os usuários antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o portal do myapps.

Ao selecionar o bloco do Coralogix no portal do myapps, você deverá ser conectado automaticamente ao Coralogix. Para obter mais informações sobre o portal do myapps, consulte [o que é o portal do myapps?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

