---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Egnyte Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Egnyte.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f8eb0f0d566d656436da11141ea7f6c521b7b82
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983729"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>Tutorial: Azure Ative Directory integração individual (SSO) com egnyte

Neste tutorial, aprenderá a integrar a Egnyte com o Azure Ative Directory (Azure AD). Quando integrar egnyte com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Egnyte.
* Permita que os seus utilizadores sejam automaticamente inscritos na Egnyte com as suas contas Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Egnyte single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* Egnyte apoia **SP** iniciado SSO
* Assim que configurar a Egnyte, pode impor o controlo da Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controle de sessão com Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-egnyte-from-the-gallery"></a>Adicionando Egnyte da galeria

Para configurar a integração da Egnyte em Azure AD, você precisa adicionar Egnyte da galeria à sua lista de aplicações geridas saaS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. No Add da secção **da galeria,** **digite Egnyte** na caixa de pesquisa.
1. **Selecione Egnyte** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configura e testa o único sign-on azure ad com egnyte com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Egnyte.

Para configurar e testar o único sinal de Azure AD com a Egnyte, é necessário completar os seguintes blocos de construção:

Para configurar e testar o Azure AD SSO com a Egnyte, complete os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configure egnyte SSO](#configure-egnyte-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie o utilizador de teste Egnyte](#create-egnyte-test-user)** - para ter uma contrapartida de B.Simon em Egnyte que esteja ligada à representação da AD Azure do utilizador.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o único signo da Azure AD com a Egnyte, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **egnyte,** selecione **Single sign-on**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Egnyte Domain e URLs informações únicas de inscrição](common/sp-signonurl.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão: `https://<companyname>.egnyte.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de logon real. Contacte a equipa de suporte ao [Cliente da Egnyte](https://www.egnyte.com/corp/contact_egnyte.html) para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download de certificado](common/certificatebase64.png)

5. Na secção **Configurar egnyte,** copie os URL(s) adequados de acordo com o seu requisito.

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
  
    b. No campo **nome de usuário** , digite **brendafernandes\@yourcompanydomain. Extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso a Egnyte.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Egnyte**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Egnyte**.

    ![O link Egnyte na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-egnyte-sso"></a>Configure Egnyte SSO

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa Egnyte como administrador.

2. Clique em **Definições**.
   
    ![Definições](./media/egnyte-tutorial/ic787819.png "Definições")

3. No menu, clique em **Definições**.

    ![Definições](./media/egnyte-tutorial/ic787820.png "Definições")

4. Clique no separador **Configuração** e, em seguida, clique em **Segurança**.

    ![Segurança](./media/egnyte-tutorial/ic787821.png "Segurança")

5. Na secção **de autenticação de sinal único,** execute os seguintes passos:

    ![Sinal único na autenticação](./media/egnyte-tutorial/ic787822.png "Sinal único na autenticação")   
    
    a. Como autenticação única de **inscrição,** selecione **SAML 2.0**.
   
    b. Como **fornecedor de identidade,** selecione **AzureAD**.
   
    c. Colhe o URL de **Login** copiado do portal Azure para a caixa de texto URL do **fornecedor de identidade.**
   
    d. O **Identificador AD** Da Paste Azure, que copiou do portal Azure para a caixa de texto ID da **entidade fornecedora** de identidade.
      
    e. Abra o seu certificado codificado base-64 no bloco de notas descarregado do portal Azure, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto do certificado do fornecedor de **identidade.**
   
    f. Como **mapeamento predefinido do utilizador,** selecione **endereço de e-mail**.
   
    g. As **Use o valor do emitente específico**do domínio, selecione **desativado**.
   
    h. Clique em **Guardar**.

### <a name="create-egnyte-test-user"></a>Criar o utilizador de teste Egnyte

Para permitir que os utilizadores da AD Azure assinem a Egnyte, devem ser aprovisionados em Egnyte. No caso da Egnyte, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da sua empresa **Egnyte** como administrador.

2. Vá a **Definições \> Utilizadores e Grupos**.

3. Clique em **Adicionar Novo Utilizador**e, em seguida, selecione o tipo de utilizador que pretende adicionar.
   
    ![Utilizadores](./media/egnyte-tutorial/ic787824.png "Utilizadores")

4. Na secção **New Power User,** execute os seguintes passos:
    
    ![Novo Utilizador Padrão](./media/egnyte-tutorial/ic787825.png "Novo Utilizador Padrão")   

    a. Na caixa de texto **por e-mail,** introduza o e-mail de utilizadores como **Brittasimon\@contoso.com**.

    b. Na caixa de texto **Username,** introduza o nome de utilizador de utilizador como **Brittasimon**.

    c. Selecione **um único sinal como** tipo de **autenticação**.
   
    d. Clique em **Guardar**.
    
    >[!NOTE]
    >O titular da conta Azure Ative Directory receberá um e-mail de notificação.
    >

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador egnyte ou APIs fornecidas pela Egnyte para fornecer contas de utilizador da Azure AD.
>

### <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no azulejo Egnyte no Painel de Acesso, deve ser automaticamente inscrito no Egnyte para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [O que é o controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
