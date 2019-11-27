---
title: 'Tutorial: integração do Azure Active Directory com o TOPdesk-Secure | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TOPdesk-Secure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 393557531fe69a494a16e0f4c49ac07891e490ec
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233406"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Tutorial: integração do Azure Active Directory com o TOPdesk-Secure

Neste tutorial, você aprenderá a integrar o TOPdesk-Secure ao Azure Active Directory (Azure AD).
A integração do TOPdesk-Secure ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao TOPdesk-Secure.
* Você pode permitir que seus usuários sejam automaticamente conectados ao TOPdesk-Secure (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao TOPdesk-Secure, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* TOPdesk-assinatura habilitada para logon único seguro

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* TOPdesk-Secure dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-topdesk---secure-from-the-gallery"></a>Adicionando o TOPdesk-Secure da Galeria

Para configurar a integração do TOPdesk-Secure ao Azure AD, você precisará adicionar o TOPdesk-Secure da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o TOPdesk-Secure da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **TOPdesk-Secure**, selecione **TOPdesk-Secure** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![TOPdesk-seguro na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o TOPdesk-Secure, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do TOPdesk-Secure.

Para configurar e testar o logon único do Azure AD com o TOPdesk-Secure, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do TOPdesk-Secure](#configure-topdesk---secure-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar um usuário de teste do TOPdesk-Secure](#create-topdesk---secure-test-user)** – para ter um equivalente de Brenda Simon no TOPdesk-Secure que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o TOPdesk-Secure, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **TOPdesk-Secure** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do TOPdesk-Secure](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<companyname>.topdesk.net`

    b. Na caixa **URL do identificador** , preencha a URL de metadados do TOPdesk que você pode recuperar da configuração do TOPdesk. Ele deve usar o seguinte padrão: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`

    c. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de logon, o identificador e a URL de resposta reais. Contate a [equipe de suporte ao cliente do TOPdesk-Secure](https://www.topdesk.com/us/support/) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Na seção **Configurar TOPdesk-Secure** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-topdesk---secure-single-sign-on"></a>Configurar o logon único do TOPdesk-seguro

1. Faça logon em seu site de empresa do **TOPdesk-Secure** como administrador.

2. No menu **TOPdesk** , clique em **configurações**.

    ![Definições](./media/topdesk-secure-tutorial/ic790598.png "Definições")

3. Clique em **configurações de logon**.

    ![Configurações de logon](./media/topdesk-secure-tutorial/ic790599.png "Configurações de logon")

4. Expanda o menu **configurações de logon** e clique em **geral**.

    ![Geral](./media/topdesk-secure-tutorial/ic790600.png "Geral")

5. Na seção **seguro** da seção de configuração de **logon do SAML** , execute as seguintes etapas:

    ![Configurações técnicas](./media/topdesk-secure-tutorial/ic790855.png "Configurações técnicas")

    a. Clique em **baixar** para baixar o arquivo de metadados públicos e, em seguida, salve-o localmente no seu computador.

    b. Abra o arquivo de metadados e localize o nó **AssertionConsumerService** .

    ![Serviço de consumidor de asserção](./media/topdesk-secure-tutorial/ic790856.png "Serviço de consumidor de asserção")

    c. Copie o valor **AssertionConsumerService** , Cole esse valor na caixa de texto URL de resposta na seção **domínio e URLs do TOPdesk-Secure** .

6. Para criar um arquivo de certificado, execute as seguintes etapas:

    ![Certificado](./media/topdesk-secure-tutorial/ic790606.png "Certificado")

    a. Abra o arquivo de metadados baixado em portal do Azure.

    b. Expanda o nó **RoleDescriptor** que tem um **xsi: Type** de **alimentado: ApplicationServiceType**.

    c. Copie o valor do nó **X509Certificate** .

    d. Salve o valor de **X509Certificate** copiado localmente no seu computador em um arquivo.

7. Na seção **público** , clique em **Adicionar**.

    ![Agrega](./media/topdesk-secure-tutorial/ic790607.png "Adicionar")

8. Na página da caixa de diálogo **Assistente de configuração do SAML** , execute as seguintes etapas:

    ![Assistente de configuração do SAML](./media/topdesk-secure-tutorial/ic790608.png "Assistente de configuração do SAML")

    a. Para carregar o arquivo de metadados baixado do portal do Azure, em **metadados de Federação**, clique em **procurar**.

    b. Para carregar o arquivo de certificado, em **certificado (RSA)** , clique em **procurar**.

    c. Para a **chave privada (RSA, PKCS8, der)** , você pode carregar sua própria chave privada ou pode entrar em contato com a [equipe de suporte do cliente TOPdesk-Secure](https://www.topdesk.com/us/support) para obter a chave privada.

    d. Para carregar o arquivo de logotipo obtido da equipe de suporte do TOPdesk, em **ícone de logotipo**, clique em **procurar**.

    e. Na caixa de texto **atributo de nome de usuário** , digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. Na caixa de texto **nome de exibição** , digite um nome para a sua configuração.

    g. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite **brendafernandes\@yourcompanydomain. Extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo-lhe acesso ao TOPdesk-Secure.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **TOPdesk-seguro**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **TOPdesk-Secure**.

    ![O link TOPdesk-Secure na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-topdesk---secure-test-user"></a>Criar TOPdesk-usuário de teste seguro

Para permitir que os usuários do AD do Azure façam logon no TOPdesk-Secure, eles devem ser provisionados no TOPdesk-Secure.  
No caso do TOPdesk-Secure, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuário, execute as seguintes etapas:

1. Faça logon em seu site de empresa do **TOPdesk-Secure** como administrador.

2. No menu na parte superior, clique em **TOPdesk \> novo \> suporte aos arquivos \> operador**.

    ![Operador](./media/topdesk-secure-tutorial/ic790610.png "Operador")

3. Na caixa de diálogo **novo operador** , execute as seguintes etapas:

    ![Novo operador](./media/topdesk-secure-tutorial/ic790611.png "Novo operador")

    a. Clique na guia **geral** .

    b. Na caixa de texto **sobrenome** , digite o sobrenome do usuário, como **Simon**.

    c. Selecione um **site** para a conta na seção **local** .

    d. Na caixa de texto **nome de logon** da seção de **logon do TOPdesk** , digite um nome de logon para o usuário.

    e. Clique em **Guardar**.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação de conta de usuário TOPdesk ou APIs fornecidas pelo TOPdesk-seguro para provisionar contas de usuário do Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco TOPdesk-Secure no painel de acesso, você deverá entrar automaticamente no TOPdesk-Secure para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

