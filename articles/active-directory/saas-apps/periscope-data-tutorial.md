---
title: 'Tutorial: integração do Azure Active Directory com os dados do Periscope | Microsoft Docs'
description: Saiba como configurar o logon único entre os dados Azure Active Directory e Periscope.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3f378edb-9ac9-494d-a84a-03357b923ee1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: ffa0176c4dde6334e70b9a56b066a677d1610b48
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160207"
---
# <a name="tutorial-azure-active-directory-integration-with-periscope-data"></a>Tutorial: integração do Azure Active Directory com os dados do Periscope

Neste tutorial, você aprenderá a integrar os dados do Periscope com o Azure Active Directory (Azure AD).
A integração do Periscope data ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso aos dados do Periscope.
* Você pode permitir que seus usuários façam logon automaticamente no Periscope data (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Periscope data, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único de dados do Periscope

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* Os dados do Periscope dão suporte ao SSO iniciado pelo **SP**

## <a name="adding-periscope-data-from-the-gallery"></a>Adicionando dados do Periscope da Galeria

Para configurar a integração dos dados do Periscope ao Azure AD, você precisará adicionar os dados do Periscope da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar dados do Periscope da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Periscope data**, selecione **dados do Periscope** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Periscope dados na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Periscope data, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Periscope Data.

Para configurar e testar o logon único do Azure AD com o Periscope data, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único de dados do Periscope](#configure-periscope-data-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do Periscope data](#create-periscope-data-test-user)** – para ter um equivalente de Brenda Simon nos dados do Periscope vinculados à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Periscope data, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Periscope data** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs de dados do Periscope](common/sp-identifier.png)

    a. Na caixa de texto **URL de logon** , digite qualquer uma das URLs:
    
    | |
    |--|
    | `https://app.periscopedata.com/` |
    | `https://app.periscopedata.com/app/<SITENAME>` |

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `https://app.periscopedata.com/<SITENAME>/sso`

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize os valores com a URL de logon real. Contate a [equipe de suporte ao cliente do Periscope data](mailto:support@periscopedata.com) para obter esse valor e o valor do identificador que você obterá da seção **Configurar logon único de dados do Periscope** , que é explicada posteriormente no tutorial. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique no botão Copiar para copiar a URL de **metadados de Federação do aplicativo** e salvá-la no computador.

    ![O link de download do certificado](common/copy-metadataurl.png)

### <a name="configure-periscope-data-single-sign-on"></a>Configurar o logon único de dados do Periscope

1. Em uma janela diferente do navegador da Web, entre no Periscope data como administrador.

2. Abra o menu de engrenagem na parte inferior esquerda e abra o menu de **segurança** > de **cobrança** e execute as etapas a seguir. Somente os administradores têm acesso a essas configurações.

    ![Informações de configuração de dados do Periscope](./media/periscope-data-tutorial/configure01.png)

    a. Copie a **URL de metadados de Federação do aplicativo** da etapa #5 **certificado de autenticação SAML** e abra-a em um navegador. Isso abrirá um documento XML.

    b. Na caixa de texto **logon único** , selecione **Azure Active Directory**.

    c. Localize a marca **SingleSignOnService** e cole o valor **local** na caixa de texto **URL de SSO** .

    d. Localize a marca **SingleLogoutService** e cole o valor **local** na caixa de texto **URL do SLO** .

    e. Copie o valor do **identificador** para sua instância e cole-o na caixa de texto **identificador (ID da entidade)** da seção **configuração básica do SAML** em portal do Azure.

    f. Localize a primeira marca do arquivo XML, copie o valor de **EntityId** e cole-o na caixa de texto **emissor** .

    g. Localize a marca **IDPSSODescriptor** com o protocolo SAML. Dentro dessa seção, localize a marca **keydescriptor** com **use = Signing**. Copie o valor de **X509Certificate** e cole-o na caixa de texto **certificado** .

    h. Sites com vários espaços podem escolher o espaço padrão na lista suspensa **espaço padrão** . Esse será o espaço que novos usuários são adicionados ao fazer logon no Periscope data pela primeira vez e são provisionados por meio do Active Directory logon único.

    i. Por fim, clique em **salvar** e **confirme** a alteração das configurações de SSO digitando **logout**.

    ![Informações de configuração de dados do Periscope](./media/periscope-data-tutorial/configure02.png)

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso aos dados do Periscope.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **dados do Periscope**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Periscope data**.

    ![O link de dados Periscope na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-periscope-data-test-user"></a>Criar usuário de teste do Periscope data

Para permitir que os usuários do Azure AD façam logon nos dados do Periscope, eles devem ser provisionados nos dados do Periscope. Nos dados do Periscope, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon nos dados do Periscope como administrador.

2. Clique no ícone de **configurações** na parte inferior esquerda do menu e navegue até **permissões**.

    ![Informações de configuração de dados do Periscope](./media/periscope-data-tutorial/configure03.png)

3. Clique em **Adicionar usuário** e execute as seguintes etapas:

      ![Informações de configuração de dados do Periscope](./media/periscope-data-tutorial/configure04.png)

    a. Na caixa de texto **nome** , insira o nome do usuário, como **Brenda**.

    b. Na caixa de texto **sobrenome** , insira o sobrenome do usuário, como **Simon**.

    c. Na caixa de texto **email** , insira o email do usuário, como **brendafernandes\@contoso.com**.

    d. Clique em **Adicionar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco de dados do Periscope no painel de acesso, você deverá ser conectado automaticamente aos dados do Periscope para os quais você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

