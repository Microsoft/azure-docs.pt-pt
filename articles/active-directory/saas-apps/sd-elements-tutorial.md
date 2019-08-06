---
title: 'Tutorial: Integração do Azure Active Directory com os elementos SD | Microsoft Docs'
description: Saiba como configurar o logon único entre os elementos Azure Active Directory e SD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f0386307-bb3b-4810-8d4b-d0bfebda04f4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: a0272de238055151e73c13687f25745c3f402eb4
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68826153"
---
# <a name="tutorial-azure-active-directory-integration-with-sd-elements"></a>Tutorial: Integração do Azure Active Directory com os elementos SD

Neste tutorial, você aprenderá a integrar os elementos SD com o Azure Active Directory (Azure AD).
A integração dos elementos SD ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso aos elementos SD.
* Você pode permitir que seus usuários sejam automaticamente conectados a elementos SD (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com os elementos SD, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único de elementos SD

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* Os elementos SD dão suporte ao SSO iniciado pelo **IDP**

## <a name="adding-sd-elements-from-the-gallery"></a>Adicionando elementos SD da Galeria

Para configurar a integração de elementos SD ao Azure AD, você precisa adicionar elementos SD da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar elementos SD da galeria, execute as seguintes etapas:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **elementos SD**, selecione **elementos SD** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Elementos SD na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com os elementos do SD, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado dos elementos SD.

Para configurar e testar o logon único do Azure AD com os elementos SD, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o logon único de elementos SD](#configure-sd-elements-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar usuário de teste de elementos SD](#create-sd-elements-test-user)** – para ter um equivalente de Brenda Simon nos elementos SD que esteja vinculado à representação de usuário do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com os elementos SD, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SD Elements** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-** enalimentado para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na página **Configurar logon único com SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do SD Elements](common/idp-intiated.png)

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão:`https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:`https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador e a URL de resposta reais. Contate a [equipe de suporte ao cliente de elementos SD](mailto:support@sdelements.com) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Seu aplicativo SD Elements espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo **atributos de usuário** .

    ![image](common/edit-attribute.png)

6. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML, conforme mostrado na imagem acima, e execute as seguintes etapas:

    | Nome |  Atributo de origem|
    | --- | --- |
    | email |user.mail |
    | FirstName |user.givenname |
    | LastName |User. sobrenome |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

7. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download de certificado](common/certificatebase64.png)

8. Na seção **configurar elementos SD** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-sd-elements-single-sign-on"></a>Configurar o logon único de elementos SD

1. Para obter o logon único habilitado, entre em contato com a [equipe de suporte dos elementos SD](mailto:support@sdelements.com) e forneça o arquivo de certificado baixado.

1. Em uma janela diferente do navegador, faça logon em seu locatário do SD Elements como administrador.

1. No menu na parte superior, clique em **sistema**e, em seguida, **logon único**.

    ![Configurar o início de sessão único](./media/sd-elements-tutorial/tutorial_sd-elements_09.png)

1. Na caixa de diálogo **configurações de logon único** , execute as seguintes etapas:

    ![Configurar o início de sessão único](./media/sd-elements-tutorial/tutorial_sd-elements_10.png)

    a. Como **tipo de SSO**, selecione **SAML**.

    b. Na caixa de texto **ID da entidade do provedor de identidade** , Cole o valor do identificador do **Azure ad**que você copiou do portal do Azure.

    c. Na caixa de texto **serviço de logon único do provedor de identidade** , Cole o valor da URL de **logon**copiado de portal do Azure.

    d. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No tipo de campo **nome de usuário** **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso aos elementos SD.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **elementos SD**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **elementos SD**.

    ![O link de elementos SD na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-sd-elements-test-user"></a>Criar usuário de teste de elementos SD

O objetivo desta seção é criar um usuário chamado Brenda Simon nos elementos SD. No caso de elementos SD, a criação de elementos SD usuários é uma tarefa manual.

**Para criar Brenda Simon em elementos SD, execute as seguintes etapas:**

1. Em uma janela do navegador da Web, faça logon em seu site de empresa de elementos do SD como administrador.

1. No menu na parte superior, clique em **Gerenciamento de usuários**e em **usuários**.

    ![Criando um usuário de teste de elementos SD](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

1. Clique em **Adicionar novo usuário**.

    ![Criando um usuário de teste de elementos SD](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)

1. Na caixa de diálogo **Adicionar novo usuário** , execute as seguintes etapas:

    ![Criando um usuário de teste de elementos SD](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 

    a. Na caixa de texto **email** , insira o email do usuário **brittasimon@contoso.com** como.

    b. Na caixa de texto **nome** , insira o nome do usuário, como **Brenda**.

    c. Na caixa de texto **sobrenome** , insira o sobrenome do usuário, como **Simon**.

    d. Como **função**, selecione **usuário**.

    e. Clique em **criar usuário**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando você clica no bloco elementos SD no painel de acesso, deve ser automaticamente conectado aos elementos SD para os quais você configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)