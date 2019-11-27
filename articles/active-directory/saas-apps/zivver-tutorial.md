---
title: 'Tutorial: integração do Azure Active Directory com o ZIVVER | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ZIVVER.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 64cb7ea0-df6c-4963-84d8-6f435980e2de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/22/2019
ms.author: jeedes
ms.openlocfilehash: 02b292c5db3d20f56d7b8291ea31d8da9863809b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233247"
---
# <a name="tutorial-azure-active-directory-integration-with-zivver"></a>Tutorial: integração do Azure Active Directory com o ZIVVER

Neste tutorial, você aprenderá a integrar o ZIVVER com o Azure Active Directory (Azure AD).
A integração do ZIVVER ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao ZIVVER.
* Você pode permitir que seus usuários sejam conectados automaticamente ao ZIVVER (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao ZIVVER, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do ZIVVER

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* ZIVVER dá suporte ao SSO iniciado pelo **IDP**

## <a name="adding-zivver-from-the-gallery"></a>Adicionando o ZIVVER da Galeria

Para configurar a integração do ZIVVER ao Azure AD, você precisará adicionar o ZIVVER da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o ZIVVER da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **ZIVVER**, selecione **ZIVVER** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![ZIVVER na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o ZIVVER, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no ZIVVER.

Para configurar e testar o logon único do Azure AD com o ZIVVER, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do ZIVVER](#configure-zivver-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do ZIVVER](#create-zivver-test-user)** – para ter um equivalente de Brenda Simon no ZIVVER que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o ZIVVER, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ZIVVER** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do ZIVVER](common/idp-identifier.png)

    Na caixa de texto **identificador** , digite uma URL: `https://app.zivver.com/SAML/Zivver`

5. O aplicativo ZIVVER espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, onde o **nameidentifier** é mapeado com **User. UserPrincipalName**. O aplicativo ZIVVER espera que **nameidentifier** seja mapeado com **User. mail**, portanto, você precisa editar o mapeamento de atributo clicando no ícone **Editar** e alterar o mapeamento de atributo.

    ![image](common/edit-attribute.png)

6. Além de acima, o aplicativo ZIVVER espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | Nome | Espaço de Nomes | Atributo de origem|
    | ---------------| --------------- |
    | ZivverAccountKey | https:\//zivver.com/SAML/Attributes | User. ObjectId |

    >[!NOTE]
    >Se você estiver usando uma instalação híbrida com Active Directory ferramenta local e Azure AD Connect, o valor deverá ser definido como `user.objectGUID`

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **Guardar**.

7. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** e clique em **copiar** ícone para copiar a **URL de metadados de Federação do aplicativo** das opções determinadas de acordo com seu requisito e salvá-la no computador.

    ![O link de download da URL do certificado](./media/zivver-tutorial/metadataxmlurl.png)

8. Na seção **Configurar ZIVVER** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-zivver-single-sign-on"></a>Configurar o logon único do ZIVVER

1. Em uma janela diferente do navegador da Web, entre no [site](https://app.zivver.com/login) da empresa do ZIVVER como um administrador.

2. Clique no ícone **configurações da organização** na parte inferior esquerda da janela do navegador.

3. Vá para **logon único**.

4. Abra o arquivo XML de metadados de Federação que você baixou do portal do Azure.

5. Na caixa de texto **URL de metadados do provedor de identidade** , Cole a URL de metadados de Federação do **aplicativo** que você salvou anteriormente da portal do Azure.

6. Marque a caixa de seleção **Ativar SSO**.

7. Clique em **GUARDAR**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao ZIVVER.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **ZIVVER**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **ZIVVER**.

    ![O link do ZIVVER na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-zivver-test-user"></a>Criar usuário de teste do ZIVVER

Nesta seção, você criará um usuário chamado Brenda Simon no ZIVVER. Trabalhe com a [equipe de suporte do ZIVVER](https://support.zivver.com/) para adicionar os usuários na plataforma do ZIVVER. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do ZIVVER no painel de acesso, você deverá ser conectado automaticamente ao ZIVVER para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

