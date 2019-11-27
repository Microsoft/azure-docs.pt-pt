---
title: 'Tutorial: integração do Azure Active Directory com o ThoughtWorks mistura | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ThoughtWorks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: a12d4dca61734275ef0e56dfe2a800c64dc52540
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233297"
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Tutorial: integração do Azure Active Directory com o ThoughtWorks mistura

Neste tutorial, você aprenderá a integrar o Thoughtworkss à mistura com o Azure Active Directory (Azure AD).
A integração do ThoughtWorks mistura ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao ThoughtWorks mistura.
* Você pode permitir que seus usuários façam logon automaticamente no ThoughtWorks (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao ThoughtWorks mistura, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do ThoughtWorks mistura

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O ThoughtWorks mistura dá suporte ao SSO iniciado por **SP**

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Adicionando o ThoughtWorks mistura da Galeria

Para configurar a integração do ThoughtWorks mistura com o Azure AD, você precisará adicionar o ThoughtWorks de mistura da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o ThoughtWorks mistura da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **ThoughtWorks mistura**, selecione **ThoughtWorks mistura** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![ThoughtWorks mistura na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o ThoughtWorks mistura, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ThoughtWorks.

Para configurar e testar o logon único do Azure AD com o ThoughtWorks mistura, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do ThoughtWorks mistura](#configure-thoughtworks-mingle-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do Thoughtworksé](#create-thoughtworks-mingle-test-user)** – para ter um equivalente de Brenda Simon no ThoughtWorks de vendas que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o ThoughtWorks mistura, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ThoughtWorks mistura** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do ThoughtWorks mistura](common/sp-signonurl.png)

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de logon real. Contate a [equipe de suporte ao cliente do ThoughtWorks mistura](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support) para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Na seção **Configurar a mistura de ThoughtWorks** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-thoughtworks-mingle-single-sign-on"></a>Configurar o logon único do ThoughtWorks mistura

1. Entre no site da empresa do **Thoughtworksé** como administrador.

2. Clique na guia **admin** e, em seguida, clique em **configuração de SSO**.
   
    ![Guia administrador](./media/thoughtworks-mingle-tutorial/ic785157.png "Configuração de SSO")

3. Na seção **configuração de SSO** , execute as seguintes etapas:
   
    ![Configuração de SSO](./media/thoughtworks-mingle-tutorial/ic785158.png "Configuração de SSO")
    
    a. Para carregar o arquivo de metadados, clique em **escolher arquivo**. 

    b. Clique em **salvar alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao ThoughtWorks mistura.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **ThoughtWorks mistura**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **ThoughtWorks mistura**.

    ![O link do ThoughtWorks mistura na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-thoughtworks-mingle-test-user"></a>Criar usuário de teste do ThoughtWorks mistura

Para que os usuários do Azure AD possam entrar, eles devem ser provisionados no aplicativo ThoughtWorks mistura usando seus nomes de usuário Azure Active Directory. No caso do ThoughtWorks mistura, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuário, execute as seguintes etapas:**

1. Entre no site da empresa do Thoughtworksé como administrador.

2. Clique em **perfil**.
   
    ![Seu primeiro projeto](./media/thoughtworks-mingle-tutorial/ic785160.png "Seu primeiro projeto")

3. Clique na guia **admin** e, em seguida, clique em **usuários**.
   
    ![Utilizadores](./media/thoughtworks-mingle-tutorial/ic785161.png "Utilizadores")

4. Clique em **novo usuário**.
   
    ![Novo usuário](./media/thoughtworks-mingle-tutorial/ic785162.png "Novo usuário")

5. Na página da caixa de diálogo **novo usuário** , execute as seguintes etapas:
   
    ![Caixa de diálogo novo usuário](./media/thoughtworks-mingle-tutorial/ic785163.png "Novo usuário")  
 
    a. Digite o **nome de entrada**, o **nome de exibição**, **escolha senha**, **confirme a senha** de uma conta válida do Azure AD que você deseja provisionar nas caixas de texto relacionadas. 

    b. Como **tipo de usuário**, selecione **usuário completo**.

    c. Clique em **criar este perfil**.

>[!NOTE]
>Você pode usar qualquer outra ferramenta de criação de conta de usuário do ThoughtWorks, ou APIs fornecidas pelo ThoughtWorks, para provisionar contas de usuário do Azure AD.
> 

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco ThoughtWorks de mistura no painel de acesso, você deverá ser conectado automaticamente à mistura ThoughtWorks para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

