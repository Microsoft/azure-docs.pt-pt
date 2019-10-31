---
title: 'Tutorial: integração de Azure Active Directory com o Vitóriaa | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e o Vitóriaa.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 031a88bb8a8832fbe7acb124436cce6aaeb02e21
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159308"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Tutorial: integração de Azure Active Directory com o Vitóriaa

Neste tutorial, você aprenderá a integrar o Vitóriaa com o Azure Active Directory (Azure AD).
A integração do Vitóriaa ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Vitóriaa.
* Você pode permitir que seus usuários sejam conectados automaticamente ao Vitóriaa (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Vitóriaa, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do vitóriaa

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O vitóriar dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-coupa-from-the-gallery"></a>Adicionando o Vitóriaa da Galeria

Para configurar a integração do Vitóriaa ao Azure AD, você precisa adicionar o Vitóriaa da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Vitóriaa da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **vitóriaa**, selecione **vitóriaa** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Vitóriaa na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Vitóriaa, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Vitóriaa.

Para configurar e testar o logon único do Azure AD com o Vitóriaa, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do vitóriaa](#configure-coupa-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do vitóriaa](#create-coupa-test-user)** – para ter um equivalente de Brenda Simon no vitóriaa que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Vitóriaa, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **vitóriaa** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do vitóriaa](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<companyname>.coupahost.com`

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize esse valor com a URL de logon real. Contate a [equipe de suporte ao cliente do vitóriaa](https://success.coupa.com/Support/Contact_Us?) para obter esse valor.

    b. Na caixa **identificador** , digite uma URL:

    | Ambiente  | URL |
    |:-------------|----|
    | AppDomain | `sso-stg1.coupahost.com`|
    | Produção | `sso-prd1.coupahost.com`|
    | | |

    c. Na caixa de texto **URL de resposta** , digite uma URL:

    | Ambiente | URL |
    |------------- |----|
    | AppDomain | `https://sso-stg1.coupahost.com/sp/ACS.saml2`|
    | Produção | `https://sso-prd1.coupahost.com/sp/ACS.saml2`|
    | | |

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download do certificado](common/metadataxml.png)

6. Na seção **Configurar o vitóriaa** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-coupa-single-sign-on"></a>Configurar o logon único do Vitóriaa

1. Faça logon em seu site de empresa do Vitóriaa como administrador.

2. Vá para **instalação \> controle de segurança**.

    ![Controles de segurança](./media/coupa-tutorial/ic791900.png "Controles de segurança")

3. Na seção **fazer logon usando credenciais de vitóriaa** , execute as seguintes etapas:

    ![Metadados de SP de vitória](./media/coupa-tutorial/ic791901.png "Metadados de SP de vitória")

    a. Selecione **fazer logon usando SAML**.

    b. Clique em **procurar** para carregar os metadados baixados do portal do Azure.

    c. Clique em **Guardar**.

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao Vitóriaa.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **vitóriaa**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **vitóriaa**.

    ![O link de Vitóriaa na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-coupa-test-user"></a>Criar usuário de teste do Vitóriaa

Para permitir que os usuários do AD do Azure façam logon no Vitóriaa, eles devem ser provisionados no Vitóriaa.  

* No caso de Vitóriaa, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **vitóriaa** como administrador.

2. No menu na parte superior, clique em **configuração**e em **usuários**.

    ![Utilizadores](./media/coupa-tutorial/ic791908.png "Utilizadores")

3. Clique em **Criar**.

    ![Create Users](./media/coupa-tutorial/ic791909.png "Criar usuários") (Criar Utilizadores)

4. Na seção **criar usuário** , execute as seguintes etapas:

    ![Detalhes do usuário](./media/coupa-tutorial/ic791910.png "Detalhes do usuário")

    a. Digite o **logon**, o **nome**, o **sobrenome**, a **ID de logon único**, os atributos de **email** de uma conta de Azure Active Directory válida que você deseja provisionar nas caixas de texto relacionadas.

    b. Clique em **Criar**.

    >[!NOTE]
    >O titular da conta Azure Active Directory receberá um email com um link para confirmar a conta antes que ela se torne ativa.
    >

>[!NOTE]
>Você pode usar qualquer outra ferramenta de criação de conta de usuário do Vitóriaa ou APIs fornecidas pelo Vitóriaa para provisionar contas de usuário do AAD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do Vitóriaa no painel de acesso, você deverá ser conectado automaticamente ao Vitóriaa para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

