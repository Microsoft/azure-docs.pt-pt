---
title: 'Tutorial: Integração do Azure Active Directory com o servidor tableau | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: f9ef179c1a93d8b2f97c47eb4c68d0312d55d3d1
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68825978"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Tutorial: Integração do Azure Active Directory com o servidor tableau

Neste tutorial, você aprenderá a integrar o tableau Server ao Azure Active Directory (Azure AD).
A integração do tableau Server ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao tableau Server.
* Você pode permitir que seus usuários façam logon automaticamente no tableau Server (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao tableau Server, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do tableau Server

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O servidor tableau dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-tableau-server-from-the-gallery"></a>Adicionando o tableau Server da Galeria

Para configurar a integração do tableau Server ao Azure AD, você precisará adicionar o tableau Server da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o tableau Server da galeria, execute as seguintes etapas:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **tableau Server**, selecione **servidor tableau** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Servidor tableau na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o tableau Server, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do tableau Server.

Para configurar e testar o logon único do Azure AD com o tableau Server, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o logon único do tableau Server](#configure-tableau-server-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar usuário de teste do tableau Server](#create-tableau-server-test-user)** – para ter um equivalente de Brenda Simon no tableau Server que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o tableau Server, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **tableau Server** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-** enalimentado para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do tableau Server](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://azure.<domain name>.link`

    b. Na caixa **identificador** , digite uma URL usando o seguinte padrão:`https://azure.<domain name>.link`

    c. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:`https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Os valores anteriores não são valores reais. Atualize os valores com a URL e o identificador reais na página de configuração do servidor tableau, que é explicada posteriormente no tutorial.

5. O aplicativo tableau Server espera um **nome de usuário** de declaração personalizado que precisa ser definido como abaixo. Isso está sendo usado como identificador de usuário em vez de uma declaração de identificador de usuário exclusiva. Você pode gerenciar os valores desses atributos na seção **atributos de usuário & declarações** na página de integração de aplicativos. Clique no botão **Editar** para abrir **atributos de usuário &** caixa de diálogo declarações.

    ![image](common/edit-attribute.png)

6. Na seção **declarações do usuário** , na caixa de diálogo **atributos de & de declarações do usuário** , configure o atributo de token SAML, conforme mostrado na imagem acima, e execute as seguintes etapas:

    | Nome | Atributo de origem | Espaço de Nomes |
    | ---------------| --------------- | ----------- |
    | username | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | | |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Insira o valor do **namespace** .

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

7. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download de certificado](common/metadataxml.png)

8. Na seção **Configurar o servidor do tableau** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-tableau-server-single-sign-on"></a>Configurar o logon único do tableau Server

1. Para que o SSO seja configurado para seu aplicativo, você precisa entrar no seu locatário do tableau Server como administrador.

2. Na guia **configuração** , selecione **identidade do usuário & acesso**e, em seguida, selecione a guia método de **autenticação** .

    ![Configurar o início de sessão único](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Na página **configuração** , execute as seguintes etapas:

    ![Configurar o início de sessão único](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. Para o **método de autenticação**, selecione SAML.

    b. Marque a caixa de seleção **habilitar autenticação SAML para o servidor**.

    c. URL de retorno do servidor tableau — a URL que os usuários do tableau Server acessarão, como <http://tableau_server>. O `http://localhost` uso do não é recomendado. Não há suporte para o uso de uma URL com uma barra `http://tableau_server/`à direita (por exemplo,). Copie a **URL de retorno do servidor tableau** e cole-a na caixa de texto **URL de logon** na seção **configuração básica do SAML** no portal do Azure

    d. ID da entidade SAML — a ID da entidade identifica exclusivamente a instalação do servidor tableau para o IdP. Você pode inserir a URL do servidor do tableau novamente aqui, se desejar, mas não precisa ser a URL do servidor do tableau. Copie a **ID da entidade SAML** e cole-a na caixa de texto **identificador** na seção **configuração básica do SAML** no portal do Azure

    e. Clique no **arquivo baixar metadados XML** e abra-o no aplicativo editor de texto. Localize a URL do serviço do consumidor de asserção com http post e o índice 0 e copie a URL. Agora, Cole-o na caixa de texto **URL de resposta** na seção **configuração básica do SAML** no portal do Azure

    f. Localize o arquivo de metadados de Federação baixado de portal do Azure e, em seguida, carregue-o no **arquivo de metadados do IDP SAML**.

    g. Insira os nomes dos atributos que o IdP usa para manter os nomes de usuário, nomes de exibição e endereços de email.

    h. Clique em **Guardar**

    > [!NOTE]
    > O cliente precisa carregar qualquer certificado na configuração de SSO do SAML do tableau Server e ele será ignorado no fluxo de SSO. Se precisar de ajuda para configurar o SAML no tableau Server, consulte este artigo [Configurar SAML](https://help.tableau.com/current/server/en-gb/saml_config_steps_tsm_ui.htm).

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No tipo de campo **nome de usuário**`brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao servidor tableau.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **servidor tableau**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **tableau Server**.

    ![O link do servidor tableau na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-tableau-server-test-user"></a>Criar usuário de teste do tableau Server

O objetivo desta seção é criar um usuário chamado Brenda Simon no tableau Server. Você precisa provisionar todos os usuários no servidor tableau.

Esse nome de usuário deve corresponder ao valor que você configurou no atributo personalizado de **nome**de usuário do Azure AD. Com o mapeamento correto, a integração deve funcionar Configurando o logon único do Azure AD.

> [!NOTE]
> Se precisar criar um usuário manualmente, você precisará entrar em contato com o administrador do servidor tableau em sua organização.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do tableau Server no painel de acesso, você deverá ser conectado automaticamente ao servidor do tableau para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

