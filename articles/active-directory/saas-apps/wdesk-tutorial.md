---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Wdesk Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Wdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: a4cfcf20fc7a6a3532f65c3e797da6c876844d2c
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985569"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com a Wdesk

Neste tutorial, você vai aprender a integrar a Wdesk com o Azure Ative Directory (Azure AD). Quando integrar a Wdesk com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Wdesk.
* Ative que os seus utilizadores sejam automaticamente inscritos na Wdesk com as suas contas Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Wdesk single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* Wdesk suporta **SP** e **IDP** iniciadoS SSO
* Assim que configurar o Wdesk, pode impor o controlo da Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controle de sessão com Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-wdesk-from-the-gallery"></a>Adicionando Wdesk da galeria

Para configurar a integração da Wdesk em Azure AD, precisa adicionar Wdesk da galeria à sua lista de aplicações saaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. No Add da secção **galeria,** digite **Wdesk** na caixa de pesquisa.
1. Selecione **Wdesk** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configura e testa o único sign-on azure ad com wdesk com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Wdesk.

Para configurar e testar o Azure AD SSO com wdesk, complete os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configure wdesk SSO](#configure-wdesk-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie o utilizador](#create-wdesk-test-user)** de teste Wdesk - para ter uma contraparte de B.Simon na Wdesk que esteja ligada à representação do utilizador da AD Azure.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o único sign-on azure com o Wdesk, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **wdesk,** selecione **Single sign-on**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , execute as seguintes etapas:

    ![Wdesk Domain e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **identificador,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. Na caixa de texto **URL de resposta,** escreva um URL utilizando o seguinte padrão: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    ![Wdesk Domain e URLs informações únicas de inscrição](common/metadata-upload-additional-signon.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Obtém estes valores no portal WDesk quando configura o SSO.

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Na secção **'Set-up Wdesk',** copie os URL(s) adequados de acordo com o seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No tipo de campo do **nome do utilizador** brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso à Wdesk.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Wdesk**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Wdesk**.

    ![O link Wdesk na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-wdesk-sso"></a>Configure Wdesk SSO

1. Numa janela de navegador web diferente, inscreva-se no Wdesk como administrador de segurança.

2. No canto inferior esquerdo, clique **em Administrador** e escolha O Administrador da **Conta:**
 
     ![Configurar o início de sessão único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Na Wdesk Admin, navegue para **a Segurança,** em seguida, **SAML** > **Definições SAML:**

    ![Configurar o início de sessão único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

4. Em **definições gerais,** verifique o **sinal único Ativado SAML ligado:**

    ![Configurar o início de sessão único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. Em detalhes do prestador de **serviços,** execute os seguintes passos:

    ![Configurar o início de sessão único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Copie o URL de **Login** e cole-o na caixa de texto **Sin-on Url** no portal Azure.
   
      b. Copie o **Url de Metadados** e cole-o na caixa de texto **identifier** no portal Azure.
       
      c. Copie a **url do Consumidor** e cole-a na caixa de texto Resposta **Url** no portal Azure.
   
      d. Clique em **Guardar** no portal Azure para guardar as alterações.      

6. Clique em **configurar definições idp** para abrir o diálogo de **definições de idp de edição.** Clique **em Escolher Ficheiro** para localizar o ficheiro **Metadata.xml** que guardou do portal Azure e, em seguida, faça o upload.
    
    ![Configurar o início de sessão único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
7. Clique em **guardar alterações**.

    ![Configurar o início de sessão único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

### <a name="create-wdesk-test-user"></a>Criar o utilizador de teste Wdesk

Para permitir que os utilizadores da AD Azure assinem a Wdesk, devem ser aprovisionados na Wdesk. Em Wdesk, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Inscreva-se no Wdesk como administrador de segurança.

2. Navegue para **administrador > ** Administrador **de Conta.**

     ![Configurar o início de sessão único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Clique em **Membros** em **Pessoas**.

4. Agora clique em **Adicionar Membro** para abrir caixa de diálogo do **membro adicionar.** 
   
    ![Criar um utilizador de teste do Azure AD](./media/wdesk-tutorial/createuser1.png)  

5. Na caixa de texto **do Utilizador,** introduza o nome de utilizador do utilizador como brittasimon@contoso.com e clique no botão **Continuar.**

    ![Criar um utilizador de teste do Azure AD](./media/wdesk-tutorial/createuser3.png)

6.  Insira os detalhes como mostrado abaixo:
  
    ![Criar um utilizador de teste do Azure AD](./media/wdesk-tutorial/createuser4.png)
 
    a. Na caixa de texto **por e-mail,** introduza o e-mail de utilizador como brittasimon@contoso.com.

    b. Na caixa de texto **First Name,** introduza o primeiro nome de utilizador como **Britta**.

    c. Na caixa de texto **De Apelido,** introduza o último nome de utilizador como **Simon**.

7. Clique no botão **Salvar membro.**  

    ![Criar um utilizador de teste do Azure AD](./media/wdesk-tutorial/createuser5.png)

### <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no azulejo Wdesk no Painel de Acesso, deve ser automaticamente inscrito no Wdesk para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [O que é o controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)