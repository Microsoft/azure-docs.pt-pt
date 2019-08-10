---
title: 'Tutorial: Integração do Azure Active Directory com o Recognize | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Recognize.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: c0a513ad8fe772839813615d065616f2c911a031
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68943346"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Tutorial: Integração do Azure Active Directory com o Recognize

Neste tutorial, você aprenderá a integrar o Recognize ao Azure Active Directory (Azure AD).
A integração do Recognize ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Recognize.
* Você pode permitir que seus usuários sejam automaticamente conectados para reconhecê-lo (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Recognize, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Reconhecer assinatura habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O reconhecimento dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-recognize-from-the-gallery"></a>Adicionando o Recognize da Galeria

Para configurar a integração do Recognize ao Azure AD, você precisará adicionar o Recognize da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Recognize da galeria, execute as seguintes etapas:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Recognize**, selecione Recognize no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Reconhecer na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o Recognize, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Recognize.

Para configurar e testar o logon único do Azure AD com o Recognize, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o logon único Recognize](#configure-recognize-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar usuário de teste](#create-recognize-test-user)** do Recognize – para ter um equivalente de Brenda Simon no Recognize que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Recognize, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do Recognize, selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-** enalimentado para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:

    >[!NOTE]
    >Você obterá o **arquivo de metadados do provedor de serviços** na seção **Configurar o logon único** de Recognize do tutorial.

    a. Clique em **carregamento de ficheiro de metadados**.

    ![Carregar ficheiro de metadados](common/upload-metadata.png)

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    ![escolher arquivo de metadados](common/browse-upload-metadata.png)

    c. Depois que o arquivo de metadados for carregado com êxito, o valor do **identificador** obter automaticamente populado na seção configuração básica do SAML.

    ![Informações de logon único de domínio e URLs do Recognize](common/sp-identifier.png)

     Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://recognizeapp.com/<your-domain>/saml/sso`

    > [!Note]
    > Se o valor do **identificador** não for preenchido automaticamente, você obterá o valor do identificador abrindo a URL de metadados do provedor de serviços na seção Configurações de SSO que é explicada posteriormente na seção **Configurar o logon único Recognize** do destina. O valor da URL de logon não é real. Atualize o valor com a URL de logon real. Contate a [equipe de suporte ao cliente](mailto:support@recognizeapp.com) do Recognize para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Na seção **Configurar reconhecimento** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-recognize-single-sign-on"></a>Configurar o logon único Recognize

1. Em uma janela diferente do navegador da Web, entre no seu locatário do Recognize como administrador.

2. No canto superior direito, clique em **menu**. Vá para **administrador da empresa**.
   
    ![Configurar o logon único no lado do aplicativo](./media/recognize-tutorial/tutorial_recognize_000.png)

3. No painel de navegação esquerdo, clique em **configurações**.
   
    ![Configurar o logon único no lado do aplicativo](./media/recognize-tutorial/tutorial_recognize_001.png)

4. Execute as etapas a seguir na seção **configurações de SSO** .
   
    ![Configurar o logon único no lado do aplicativo](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Como **habilitar o SSO**, selecione **ativado**.

    b. Na caixa de texto **ID da entidade do IDP** , Cole o valor do identificador do **Azure ad** que você copiou do portal do Azure.
    
    c. Na caixa de texto **URL de destino do SSO** , Cole o valor da URL de **logon** que você copiou do portal do Azure.
    
    d. Na caixa de texto **URL de destino do SLO** , Cole o valor da URL de **logout** que você copiou do portal do Azure. 
    
    e. Abra o arquivo de **certificado (Base64)** baixado no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **certificado** .
    
    f. Clique no botão **salvar configurações** . 

5. Ao lado da seção **configurações de SSO** , copie a URL na URL de metadados do **provedor de serviço**.
   
    ![Configurar o logon único no lado do aplicativo](./media/recognize-tutorial/tutorial_recognize_003.png)

6. Abra o **link URL de metadados** em um navegador em branco para baixar o documento de metadados. Em seguida, copie o valor de EntityDescriptor (ID da entidade) do arquivo e cole-o na caixa de texto **identificador** na **configuração básica do SAML** no portal do Azure.
    
    ![Configurar o logon único no lado do aplicativo](./media/recognize-tutorial/tutorial_recognize_004.png)

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao Recognize.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **reconhecer**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **reconhecer**.

    ![O link reconhecer na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-recognize-test-user"></a>Criar usuário de teste do Recognize

Para permitir que os usuários do AD do Azure façam logon no Recognize, eles devem ser provisionados no Recognize. No caso do Recognize, o provisionamento é uma tarefa manual.

Este aplicativo não dá suporte ao provisionamento do SCIM, mas tem uma sincronização de usuário alternativa que provisiona os usuários. 

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no site da empresa Recognize como administrador.

2. No canto superior direito, clique em **menu**. Vá para **administrador da empresa**.

3. No painel de navegação esquerdo, clique em **configurações**.

4. Execute as etapas a seguir na seção **sincronização do usuário** .
   
    ![Novo usuário](./media/recognize-tutorial/tutorial_recognize_005.png "Novo usuário")
   
    a. Como **sincronização habilitada**, selecione **ativado**.
   
    b. Como **escolher o provedor de sincronização**, selecione **Microsoft/Office 365**.
   
    c. Clique em **executar sincronização do usuário**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco reconhecer no painel de acesso, você deverá entrar automaticamente no Recognize para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

