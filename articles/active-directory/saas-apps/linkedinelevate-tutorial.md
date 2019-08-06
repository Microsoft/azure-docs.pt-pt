---
title: 'Tutorial: Integração do Azure Active Directory com o LinkedIn Elevate | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o LinkedIn Elevate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b7cb8d6ab34a632e36ea2fd1c87005a038bc523
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823709"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Tutorial: Integração do Azure Active Directory com o LinkedIn Elevate

Neste tutorial, você aprenderá a integrar o LinkedIn Elevate ao Azure Active Directory (Azure AD).
A integração do LinkedIn Elevate ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao LinkedIn Elevate.
* Você pode permitir que seus usuários façam logon automaticamente no LinkedIn Elevate (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o LinkedIn Elevate, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do LinkedIn Elevate

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O LinkedIn Elevate dá suporte a SP iniciado por **controlador de logon e IDP**

* O LinkedIn Elevate dá suporte ao provisionamento **de usuário just in time**

* O LinkedIn Elevate dá suporte ao [ provisionamento automatizado de usuários](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Adicionando o LinkedIn Elevate da Galeria

Para configurar a integração do LinkedIn Elevate ao Azure AD, você precisará adicionar o LinkedIn Elevate da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o LinkedIn Elevate da galeria, execute as seguintes etapas:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **LinkedIn Elevate**, selecione **LinkedIn Elevate** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![LinkedIn Elevate na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o LinkedIn Elevate, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do LinkedIn Elevate.

Para configurar e testar o logon único do Azure AD com o LinkedIn Elevate, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o logon único do LinkedIn Elevate](#configure-linkedin-elevate-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar usuário de teste do LinkedIn Elevate](#create-linkedin-elevate-test-user)** – para ter um equivalente de Brenda Simon no LinkedIn Elevate que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o LinkedIn Elevate, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **LinkedIn Elevate** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-** enalimentado para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do LinkedIn Elevate](common/idp-intiated.png)

    a. Na caixa de texto **identificador** , insira o valor **ID da entidade** , você copiará o valor da ID da entidade no portal do LinkedIn explicado posteriormente neste tutorial.

    b. Na caixa de texto **URL de resposta** , insira o valor da URL de acesso do **consumidor de asserção (ACS)** , você copiará o valor da URL do ACS (acesso do consumidor de asserção) do portal do LinkedIn explicado posteriormente neste tutorial.

5. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    ![Informações de logon único de domínio e URLs do LinkedIn Elevate](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

6. O aplicativo LinkedIn Elevate espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, onde o **nameidentifier** é mapeado com **User. UserPrincipalName**. O aplicativo LinkedIn Elevate espera que nameidentifier seja mapeado com **User. mail**, portanto, você precisa editar o mapeamento de atributo clicando no ícone Editar e alterar o mapeamento de atributo.

    ![image](common/edit-attribute.png)

7. Além de acima, o aplicativo LinkedIn Elevate espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção declarações do usuário, na caixa de diálogo **atributos de usuário** , execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | Nome | Atributo de origem|
    | -------| -------------|
    | Departamento | user.department |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

8. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download de certificado](common/metadataxml.png)

9. Na seção **Configurar o LinkedIn Elevate** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-linkedin-elevate-single-sign-on"></a>Configurar o logon único do LinkedIn Elevate

1. Em uma janela diferente do navegador da Web, faça logon em seu locatário do LinkedIn Elevate como administrador.

1. No **centro de contas**, clique em **configurações globais** em **configurações**. Além disso, selecione **elevar – elevar o teste do AAD** na lista suspensa.

    ![Configurar o início de sessão único](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Clique **ou clique aqui para carregar e copiar campos individuais do formulário** e execute as seguintes etapas:

    ![Configurar o início de sessão único](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. Copie a **ID da entidade** e cole-a na caixa de texto **identificador** na **configuração básica do SAML** no portal do Azure.

    b. Copie a **URL do ACS (acesso do consumidor de asserção)** e cole-a na caixa de texto **URL de resposta** na **configuração básica do SAML** no portal do Azure.

1. Acesse a seção **configurações de administração do LinkedIn** . Carregue o arquivo XML que você baixou do portal do Azure clicando na opção carregar arquivo XML.

    ![Configurar o início de sessão único](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Clique **em** ativar para habilitar o SSO. O status do SSO será alterado de **não conectado** para **conectado**

    ![Configurar o início de sessão único](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , `brittasimon@yourcompanydomain.extension`digite. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao LinkedIn Elevate.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **LinkedIn Elevate**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **LinkedIn Elevate**.

    ![O link de elevação do LinkedIn na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-linkedin-elevate-test-user"></a>Criar usuário de teste do LinkedIn Elevate

O aplicativo LinkedIn Elevate dá suporte ao provisionamento de usuário just-in-time e, após a autenticação, os usuários serão criados no aplicativo automaticamente. Na página Configurações de administrador no portal do LinkedIn Elevate, inverta a opção **atribuir licenças automaticamente** ao provisionamento ativo just in time e isso também atribuirá uma licença ao usuário. O LinkedIn Elevate também dá suporte ao provisionamento automático de usuário. você pode encontrar mais detalhes [aqui](linkedinelevate-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuário.

   ![Criar um utilizador de teste do Azure AD](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do LinkedIn Elevate no painel de acesso, você deverá ser conectado automaticamente ao LinkedIn Elevate para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)