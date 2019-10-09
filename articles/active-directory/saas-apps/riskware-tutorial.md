---
title: 'Tutorial: Integração do Azure Active Directory com o risco | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o risco.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 6eaa1be81d3ac0733c0829bc45e1b62f8aae5755
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027112"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Tutorial: Integração do Azure Active Directory com o risco

Neste tutorial, você aprenderá a integrar o risco com o Azure Active Directory (Azure AD).
A integração do risco ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao risco.
* Você pode permitir que os usuários sejam automaticamente conectados ao risco (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao risco, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único de risco

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O risco dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-riskware-from-the-gallery"></a>Adicionando o risco da Galeria

Para configurar a integração do risco ao Azure AD, você precisa adicionar o risco da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o risco da galeria, execute as seguintes etapas:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **risco**, selecione **risco** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Risco na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o risco, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do risco.

Para configurar e testar o logon único do Azure AD com o Risk, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o logon único de risco](#configure-riskware-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar usuário de teste do riskr](#create-riskware-test-user)** – para ter um equivalente de Brenda Simon em risco que esteja vinculado à representação de usuário do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o risco, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos de **risco** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs de risco](common/sp-identifier.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:
    
    | Ambiente| Padrão de URL|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PROD| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. Na caixa de texto **identificador (ID da entidade)** , digite a URL:
    
    | Ambiente| Padrão de URL|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | PROD| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize o valor com a URL de logon real. Contate a [equipe de suporte ao cliente de risco](mailto:support@pansoftware.com.au) para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Na seção **Configurar riscos** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-riskware-single-sign-on"></a>Configurar o logon único de risco

1. Em uma janela diferente do navegador da Web, entre em seu site de empresa de risco como administrador.

1. No canto superior direito, clique em **manutenção** para abrir a página manutenção.

    ![Manutenção de configurações de risco](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na página manutenção, clique em **autenticação**.

    ![Autenticação de configuração de risco](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. Na página **configuração de autenticação** , execute as seguintes etapas:

    ![Authenconfig de configuração de risco](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Selecione **tipo** como **SAML** para autenticação.

    b. Na caixa de texto **código** , digite seu código como AZURE_UAT.

    c. Na caixa de texto **Descrição** , digite sua descrição, como a configuração do Azure para sso.

    d. Na caixa de texto **página de logon único** , Cole o valor da **URL de logon** copiado de portal do Azure.

    e. Na caixa de texto **página de saída** , Cole o valor da **URL de logout** copiado de portal do Azure.

    f. Na caixa de texto **campo do formulário de postagem** , digite o nome do campo presente em resposta post que contém SAML como SAMLResponse

    g. Na caixa de texto **nome da marca de identidade XML** , digite o atributo, que contém o identificador exclusivo na resposta SAML como NameID.

    h. Abra o **XML de metadados** baixado de portal do Azure no bloco de notas, copie o certificado do arquivo de metadados e cole-o na caixa de texto **certificado**

    i. Na caixa de texto **URL do consumidor** , Cole o valor da **URL de resposta**, que você obtém da equipe de suporte.

    j. Na caixa de texto **emissor** , Cole o valor do **identificador**, que você obtém da equipe de suporte.

    > [!Note]
    > Contate a [equipe de suporte ao cliente de risco](mailto:support@pansoftware.com.au) para obter esses valores

    k. Selecione **usar pós** caixa de seleção.

    l. Selecione a caixa de seleção **usar solicitação SAML** .

    m. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite `brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao risco.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **risco**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **risco**.

    ![O link de risco na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-riskware-test-user"></a>Criar usuário de teste do Riskr

Para permitir que os usuários do Azure AD entrem no risco, eles devem ser provisionados no risco. Em risco, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no risco como administrador de segurança.

1. No canto superior direito, clique em **manutenção** para abrir a página manutenção. 

    ![Manutenção da configuração de risco](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na página manutenção, clique em **pessoas**.

    ![Pessoas de configuração de risco](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Selecione a guia **detalhes** e execute as seguintes etapas:

    ![Detalhes de configuração do risco](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Selecione **tipo de pessoa** como funcionário.

    b. Na caixa de texto **nome** , insira o nome do usuário, como **Brenda**.

    c. Na caixa de texto **sobrenome** , insira o sobrenome do usuário, como **Simon**.

1. Na guia **segurança** , execute as seguintes etapas:

    ![Segurança de configuração de risco](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. Na seção **autenticação** , selecione o modo de **autenticação** , que você tem configurado como a configuração do Azure para sso.

    b. Na seção **detalhes de logon** , na caixa de texto ID de **usuário** , insira o email do usuário, como `brittasimon@contoso.com`.

    c. Na caixa de texto **senha** , digite a senha do usuário.

1. Na guia **organização** , execute as seguintes etapas:

    ![Org. de configuração de risco](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Selecione a opção como organização **Level1** .

    b. Na seção **área de trabalho principal da pessoa** , na caixa de texto **local** , digite seu local.

    c. Na seção **funcionário** , selecione **status do funcionário** como casual.

    d. Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco de risco no painel de acesso, você deverá ser conectado automaticamente ao risco para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
