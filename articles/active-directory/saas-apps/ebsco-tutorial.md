---
title: 'Tutorial: Integração do Azure Active Directory com o EBSCO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o EBSCO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 35cb408473da8c6397c5034ae20ac0a50b0953ea
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68944710"
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>Tutorial: Integração do Azure Active Directory com o EBSCO

Neste tutorial, você aprenderá a integrar o EBSCO com o Azure Active Directory (Azure AD).
A integração do EBSCO ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao EBSCO.
* Você pode permitir que seus usuários sejam conectados automaticamente ao EBSCO (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao EBSCO, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do EBSCO

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O EBSCO dá suporte ao **SP** e ao SSO iniciado pelo **IDP**

* O EBSCO dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-ebsco-from-the-gallery"></a>Adicionando o EBSCO da Galeria

Para configurar a integração do EBSCO ao Azure AD, você precisará adicionar o EBSCO da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o EBSCO da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **EBSCO**, selecione **EBSCO** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![EBSCO na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o EBSCO, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no EBSCO.

Para configurar e testar o logon único do Azure AD com o EBSCO, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o logon único do EBSCO](#configure-ebsco-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar usuário de teste do EBSCO](#create-ebsco-test-user)** – para ter um equivalente de Brenda Simon no EBSCO que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o EBSCO, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **EBSCO** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-** enalimentado para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica do SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , execute a seguinte etapa:

    ![Informações de logon único de domínio e URLs do EBSCO](common/idp-identifier.png)

    Na caixa de texto **identificador** , digite uma URL:`pingsso.ebscohost.com`

5. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    ![image](common/both-preintegrated-signon.png)

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize o valor com a URL de logon real. Contate a [equipe de suporte ao cliente do EBSCO](mailto:sso@ebsco.com) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

    o **elementos exclusivos:**  

    o **CustID** = inserir ID de cliente EBSCO exclusivo 

    o **Profile** = os clientes podem personalizar o link para direcionar os usuários para um perfil específico (dependendo do que eles compram do EBSCO). Eles podem inserir uma ID de perfil específica. As principais IDs são EDS (serviço de descoberta EBSCO) e ehost (bancos de dados bancos). As instruções para o mesmo são fornecidas [aqui](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

6. O aplicativo EBSCO espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo **atributos de usuário** .

    ![image](common/edit-attribute.png)

     > [!Note]
    > O atributo **Name** é obrigatório e é mapeado com o **valor do identificador de nome** no aplicativo EBSCO. Isso é adicionado por padrão para que você não precise adicioná-lo manualmente.

7. Além de acima, o aplicativo EBSCO espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo: 

    | Nome | Atributo de origem|
    | ---------------| --------------- |    
    | FirstName   | user.givenname |
    | LastName   | User. sobrenome |
    | Email   | user.mail |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **Guardar**.

8. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download de certificado](common/metadataxml.png)

9. Na seção **Configurar EBSCO** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-ebsco-single-sign-on"></a>Configurar o logon único do EBSCO

Para configurar o logon único no lado do **EBSCO** , é necessário enviar o XML de **metadados** baixado e as URLs copiadas apropriadas de portal do Azure para a [equipe de suporte do EBSCO](mailto:sso@ebsco.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , brittasimon@yourcompanydomain.extensiondigite. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao EBSCO.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **EBSCO**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **EBSCO**.

    ![O link do EBSCO na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-ebsco-test-user"></a>Criar usuário de teste do EBSCO

No caso do EBSCO, o provisionamento do usuário é automático.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

O Azure AD passa os dados necessários para o aplicativo EBSCO. O provisionamento de usuário do EBSCO pode ser automático ou exigir um formulário único. Depende se o cliente tem muitas contas de EBSCOhost preexistentes com configurações pessoais salvas. O mesmo pode ser discutido com a [equipe de suporte do EBSCO](mailto:sso@ebsco.com) durante a implementação. De qualquer forma, o cliente não precisa criar contas EBSCOhost antes do teste.

   >[!Note]
   >Você pode automatizar o provisionamento/personalização de usuários do EBSCOhost. Contate a [equipe de suporte do EBSCO](mailto:sso@ebsco.com) sobre o provisionamento de usuário just-in-time. 

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

1. Ao clicar no bloco do EBSCO no painel de acesso, você deverá ser conectado automaticamente ao seu aplicativo EBSCO.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

2. Depois de fazer logon no aplicativo, clique no botão **entrar** no canto superior direito.

    ![A entrada EBSCO na lista de aplicativos](./media/ebsco-tutorial/tutorial_ebsco_signin.png)
 
3. Você receberá uma solicitação única para emparelhar o logon institucional/SAML com um **link de sua conta do MyEBSCOhost existente para sua conta de instituição agora** ou **criar uma nova conta do MyEBSCOhost e vinculá-la à sua conta da instituição**. A conta é usada para personalização no aplicativo EBSCOhost. Selecione a opção **criar uma nova conta** e você verá que o formulário para personalização é previamente concluído com os valores da resposta SAML, conforme mostrado na captura de tela abaixo. Clique em **' continuar '** para salvar esta seleção.
    
     ![O usuário EBSCO na lista de aplicativos](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. Depois de concluir a configuração acima, limpe cookies/cache e faça logon novamente. Você não precisará entrar manualmente novamente e as configurações de personalização serão lembradas

## <a name="additional-sesources"></a>Sesources adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

