---
title: 'Tutorial: integração do Azure Active Directory com o IdeaScale | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o IdeaScale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16dda6b-fdf9-43cc-9bbb-a523f085a8af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9307bbe58afc45468f222f5b510585d528de532a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158031"
---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Tutorial: integração do Azure Active Directory com o IdeaScale

Neste tutorial, você aprenderá a integrar o IdeaScale com o Azure Active Directory (Azure AD).
A integração do IdeaScale ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao IdeaScale.
* Você pode permitir que seus usuários sejam conectados automaticamente ao IdeaScale (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao IdeaScale, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do IdeaScale

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O IdeaScale dá suporte ao SSO iniciado por **SP**

## <a name="adding-ideascale-from-the-gallery"></a>Adicionando o IdeaScale da Galeria

Para configurar a integração do IdeaScale ao Azure AD, você precisará adicionar o IdeaScale da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o IdeaScale da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **IdeaScale**, selecione **IdeaScale** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![IdeaScale na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o IdeaScale, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no IdeaScale.

Para configurar e testar o logon único do Azure AD com o IdeaScale, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do IdeaScale](#configure-ideascale-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do IdeaScale](#create-ideascale-test-user)** – para ter um equivalente de Brenda Simon no IdeaScale que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o IdeaScale, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **IdeaScale** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do IdeaScale](common/sp-identifier.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<companyname>.ideascale.com`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão:
    
    | |
    |--|
    | `http://<companyname>.ideascale.com`  |
    | `https://<companyname>.ideascale.com` |

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contate a [equipe de suporte ao cliente do IdeaScale](https://support.ideascale.com/) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download do certificado](common/metadataxml.png)

6. Na seção **Configurar IdeaScale** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-ideascale-single-sign-on"></a>Configurar o logon único do IdeaScale

1. Em uma janela diferente do navegador da Web, faça logon em seu site de empresa do IdeaScale como administrador.

2. Vá para **configurações da Comunidade**.

    ![Configurações da Comunidade](./media/ideascale-tutorial/ic790847.png "Configurações da Comunidade")

3. Vá para **segurança \> configurações de logon único**.

    ![Configurações de logon único](./media/ideascale-tutorial/ic790848.png "Configurações de logon único")

4. Como **tipo de logon único**, selecione **SAML 2,0**.

    ![Tipo de logon único](./media/ideascale-tutorial/ic790849.png "Tipo de logon único")

5. Na caixa de diálogo **configurações de logon único** , execute as seguintes etapas:

    ![Configurações de logon único](./media/ideascale-tutorial/ic790850.png "Configurações de logon único")

    a. Na caixa de texto **ID da entidade IDP SAML** , Cole o valor do **identificador do Azure ad** que você copiou do portal do Azure.

    b. Abra o arquivo de metadados baixado de portal do Azure no bloco de notas, copie o conteúdo dele e cole-o na caixa de texto **metadados do IDP do SAML** .

    c. Na caixa de texto **URL de êxito de logout** , Cole o valor da URL de **logout** que você copiou do portal do Azure.

    d. Clique em **salvar alterações**.

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao IdeaScale.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **IdeaScale**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **IdeaScale**.

    ![O link do IdeaScale na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-ideascale-test-user"></a>Criar usuário de teste do IdeaScale

Para permitir que os usuários do AD do Azure façam logon no IdeaScale, eles devem ser provisionados no IdeaScale. No caso do IdeaScale, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **IdeaScale** como administrador.

2. Vá para **configurações da Comunidade**.

    ![Configurações da Comunidade](./media/ideascale-tutorial/ic790847.png "Configurações da Comunidade")

3. Vá para **configurações básicas \> gerenciamento de membros**.

4. Clique em **Adicionar membro**.

    ![Gerenciamento de membros](./media/ideascale-tutorial/ic790852.png "Gerenciamento de membros")

5. Na seção Adicionar novo membro, execute as seguintes etapas:

    ![Adicionar novo membro](./media/ideascale-tutorial/ic790853.png "Adicionar novo membro")

    a. Na caixa de texto **endereços de email** , digite o endereço de email de uma conta válida do Azure AD que você deseja provisionar.

    b. Clique em **salvar alterações**.

    > [!NOTE]
    > O titular da conta Azure Active Directory recebe um email com um link para confirmar a conta antes que ela se torne ativa.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação de conta de usuário IdeaScale ou APIs fornecidas pelo IdeaScale para provisionar contas de usuário do AAD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do IdeaScale no painel de acesso, você deverá ser conectado automaticamente ao IdeaScale para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

