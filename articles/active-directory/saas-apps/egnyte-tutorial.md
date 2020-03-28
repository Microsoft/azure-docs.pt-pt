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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76983729"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>Tutorial: Azure Ative Directory integração individual (SSO) com egnyte

Neste tutorial, aprenderá a integrar a Egnyte com o Azure Ative Directory (Azure AD). Quando integrar egnyte com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Egnyte.
* Permita que os seus utilizadores sejam automaticamente inscritos na Egnyte com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Egnyte single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Egnyte apoia **SP** iniciado SSO
* Assim que configurar a Egnyte, pode impor o controlo da Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-egnyte-from-the-gallery"></a>Adicionando Egnyte da galeria

Para configurar a integração da Egnyte em Azure AD, você precisa adicionar Egnyte da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** **digite Egnyte** na caixa de pesquisa.
1. **Selecione Egnyte** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com egnyte com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Egnyte.

Para configurar e testar o único sinal de Azure AD com a Egnyte, é necessário completar os seguintes blocos de construção:

Para configurar e testar o Azure AD SSO com a Egnyte, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure egnyte SSO](#configure-egnyte-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie o utilizador de teste Egnyte](#create-egnyte-test-user)** - para ter uma contrapartida de B.Simon em Egnyte que esteja ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com a Egnyte, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **egnyte,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Egnyte Domain e URLs informações únicas de inscrição](common/sp-signonurl.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<companyname>.egnyte.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte a equipa de suporte ao [Cliente da Egnyte](https://www.egnyte.com/corp/contact_egnyte.html) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

4. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

5. Na secção **Configurar egnyte,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **tipo** de campo de nome utilizador **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso a Egnyte.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Egnyte**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Egnyte**.

    ![O link Egnyte na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

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

2. Vá a **Definições \> Os utilizadores & Grupos**.

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

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Egnyte no Painel de Acesso, deve ser automaticamente inscrito no Egnyte para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
