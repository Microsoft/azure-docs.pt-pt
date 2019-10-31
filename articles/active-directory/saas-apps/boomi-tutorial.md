---
title: 'Tutorial: integração de Azure Active Directory com o "pronto" | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Prontoi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d016782a7217aa2eae341e5f9af4f0e141df9c0f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73157673"
---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Tutorial: integração de Azure Active Directory com o "pronto"

Neste tutorial, você aprenderá a integrar o entanto com o Azure Active Directory (AD do Azure).
A integração do pronto ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao "pronto".
* Você pode permitir que seus usuários façam logon automaticamente no "pronto" (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o "pronto", você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do prontoi

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* Prontoi dá suporte ao SSO iniciado pelo **IDP**

## <a name="adding-boomi-from-the-gallery"></a>Adicionando o "Prontoi da Galeria"

Para configurar a integração do entanto com o Azure AD, você precisa adicionar o "Prontoi" da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Prontoi da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite " **pronto** **para selecionar"** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Pronto para a lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Prontoi, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Prontoi.

Para configurar e testar o logon único do Azure AD com o Prontoi, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configure o logon único do prontoi](#configure-boomi-single-sign-on)** para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do prontoi](#create-boomi-test-user)** – para ter um equivalente de Brenda Simon em pronto, que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Prontoi, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo de **prontoi** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na página **Configurar logon único com SAML** , clique no botão **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Informações de logon único de domínio e URLs do prontoi](common/idp-intiated.png)

    a. Na caixa de texto **identificador** , digite uma URL: `https://platform.boomi.com/`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://platform.boomi.com/sso/<boomi-tenant>/saml`

    > [!NOTE]
    > O valor da URL de resposta não é real. Atualize o valor com a URL de resposta real. Contate a [equipe de suporte ao cliente](https://boomi.com/company/contact/) do entanto para obter esse valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. O aplicativo prontoi espera as asserções SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos na seção **atributos de usuário** na página de integração de aplicativos. Na página **Configurar logon único com SAML** , clique no botão **Editar** para abrir a caixa de diálogo **atributos de usuário** .

    ![imagem](common/edit-attribute.png)

6. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , configure o atributo de token SAML, conforme mostrado na imagem acima, e execute as seguintes etapas:

    | Nome |  Atributo de origem|
    | ---------------|  --------- |
    | FEDERATION_ID | User. mail |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![imagem](common/new-save-attribute.png)

    ![imagem](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **OK**

    g. Clique em **Guardar**.

7. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download do certificado](common/certificatebase64.png)

8. Na seção **Configurar o pronto para** copiar, copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-boomi-single-sign-on"></a>Configurar o logon único do Prontoi

1. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa pronto para ser um administrador. 

2. Navegue até **nome da empresa** e vá para **Configurar**.

3. Clique na guia **Opções de SSO** e execute as etapas a seguir.

    ![Configurar o logon único no lado do aplicativo](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Marque a caixa de seleção **habilitar logon único do SAML** .

    b. Clique em **importar** para carregar o certificado baixado do Azure ad para o **certificado do provedor de identidade**.

    c. Na caixa de texto **URL de logon do provedor de identidade** , coloque o valor da URL de **logon** da janela de configuração de aplicativo do Azure AD.

    d. Como **local da ID da Federação**, selecione a **ID da Federação está no botão de opção do elemento de atributo FEDERATION_ID** .

    e. Clique no botão **salvar** .

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo-lhe acesso ao "pronto".

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **pronto**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **prontoi**.

    ![O link pronto para entrar na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-boomi-test-user"></a>Criar usuário de teste do Prontoi

Para permitir que os usuários do AD do Azure façam logon no entanto, eles devem ser provisionados no "pronto". No caso do "pronto", o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para provisionar uma conta de usuário, execute as seguintes etapas:

1. Faça logon em seu site de empresa do "pronto" como administrador.

2. Depois de fazer logon, navegue até **Gerenciamento de usuários** e vá para **usuários**.

    ![Utilizadores](./media/boomi-tutorial/tutorial_boomi_001.png "Utilizadores")

3. Clique no ícone **+** e a caixa de diálogo **Adicionar/manter funções de usuário** será aberta.

    ![Utilizadores](./media/boomi-tutorial/tutorial_boomi_002.png "Utilizadores")

    ![Utilizadores](./media/boomi-tutorial/tutorial_boomi_003.png "Utilizadores")

    a. Na caixa de texto **endereço de email do usuário** , digite o email do usuário, como BrittaSimon@contoso.com.

    b. Na caixa de texto **nome** , digite o nome do usuário, como Brenda.

    c. Na caixa de texto **sobrenome** , digite o sobrenome do usuário, como Simon.

    d. Insira a ID da **Federação**do usuário. Cada usuário deve ter uma ID de Federação que identifique exclusivamente o usuário na conta.

    e. Atribua a função de **usuário padrão** ao usuário. Não atribua a função de administrador, pois isso daria acesso normal à atmosfera, bem como ao acesso de logon único.

    f. Clique em **OK**.

    > [!NOTE]
    > O usuário não receberá um email de notificação de boas-vindas contendo uma senha que pode ser usada para fazer logon na conta do AtomSphere porque sua senha é gerenciada por meio do provedor de identidade. Você pode usar qualquer outra ferramenta de criação de conta de usuário pronto para uso ou APIs fornecidas por "pronto para" para provisionar contas de usuário do AAD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco "pronto" no painel de acesso, você deve entrar automaticamente no pronto para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

