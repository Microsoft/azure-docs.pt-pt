---
title: 'Tutorial: Integração de Diretório Sonérório Ativo Azure com talentLMS Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o TalentLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 970ab9de270b1227884a13ac578d4c439043b20c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233370"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Tutorial: Integração de Diretório Ativo Azure com talentLMS

Neste tutorial, aprende-se a integrar o TalentLMS com o Azure Ative Directory (Azure AD).
Integrar o TalentLMS com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao TalentLMS.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no TalentLMS (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o TalentLMS, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura de inscrição única TalentLMS ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* TalentLMS apoia **SP** iniciado SSO

## <a name="adding-talentlms-from-the-gallery"></a>Adicionar TalentLMS da galeria

Para configurar a integração do TalentLMS em Azure AD, precisa de adicionar talentLMS da galeria à sua lista de aplicações geridas do SaaS.

**Para adicionar TalentLMS da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **TalentLMS,** selecione **TalentLMS** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

    ![TalentLMS na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o single sign-on azure com talentLMS com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no TalentLMS.

Para configurar e testar o único sign-on azure ad com o TalentLMS, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Single TalentLMS](#configure-talentlms-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create TalentLMS test user](#create-talentlms-test-user)** - para ter uma contrapartida de Britta Simon no TalentLMS que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com talentLMS, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **TalentLMS,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![TalentLMS Domain e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<tenant-name>.TalentLMSapp.com`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de apoio ao [cliente TalentLMS](https://www.talentlms.com/contact) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na secção Certificado de **Assinatura SAML,** clique no botão **Editar** para abrir o diálogo do Certificado de **Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

6. Na secção **Certificado de Assinatura SAML,** **copie** a IMPRESSÃO DIGITAL e guarde-a no seu computador.

    ![Copiar valor de impressão digital](common/copy-thumbprint.png)

7. Na secção **Configurar TalentLMS,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-talentlms-single-sign-on"></a>Configure TalentLMS Single Sign-On

1. Numa janela de navegador web diferente, inscreva-se no site da sua empresa TalentLMS como administrador.

1. Na secção **Definições & Conta,** clique no separador **Utilizadores.**

    ![Definições de & de conta](./media/talentlms-tutorial/IC777296.png "Definições de & de conta")

1. Clique num **único sign-on (SSO)**,

1. Na secção De Sessão Individual, execute os seguintes passos:

    ![Inscrição única](./media/talentlms-tutorial/IC777297.png "Início de Sessão Único")

    a. A partir da lista de tipo de **integração SSO,** selecione **SAML 2.0**.

    b. Na caixa de texto **do fornecedor de identidade (IDP),** colhe o valor do **Identificador Azure AD,** que copiou do portal Azure.

    c. Colar o valor de **impressão digital** do portal Azure na caixa de texto **impressões digitais do Certificado.**

    d.  Na caixa de texto URL de **entrada remota,** colhe o valor do URL de **Login,** que copiou do portal Azure.

    e. Na caixa de texto **URL de inscrição remota,** colhe o valor do URL de **Logout,** que copiou do portal Azure.

    f. Preencha o seguinte:

    * Na caixa de texto **TargetedID,** escreva`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * Na caixa de texto **de primeiro nome,** escreva`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * Na caixa de texto **de apelido,** escreva`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * Na caixa de texto **por e-mail,** escreva`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **User name** tipo `brittasimon@yourcompanydomain.extension`de campo do nome do utilizador . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao TalentLMS.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **TalentLMS**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **TalentLMS**.

    ![O link TalentLMS na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-talentlms-test-user"></a>Criar o utilizador de teste TalentLMS

Para permitir que os utilizadores de Anúncios Azure entrem no TalentLMS, devem ser aprovisionados no TalentLMS. No caso do TalentLMS, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu inquilino **TalentLMS.**

1. Clique em **Utilizadores**e, em seguida, clique em **Adicionar Utilizador**.

1. Na página de diálogo do **utilizador Adicionar,** execute os seguintes passos:

    ![Adicionar Utilizador](./media/talentlms-tutorial/IC777299.png "Adicionar Utilizador")  

    a. Na caixa de texto **de primeiro nome,** introduza o primeiro nome de utilizador como **Britta**.

    b. Na caixa de texto **De apelido,** introduza o último nome de utilizador como **Simon**.
 
    c. Na caixa de texto de endereço de `brittasimon\@contoso.com` **e-mail,** introduza o e-mail do utilizador como .

    d. Clique em **Adicionar Utilizador**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador TalentLMS ou APIs fornecidas pelo TalentLMS para fornecer contas de utilizadores da Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo TalentLMS no Painel de Acesso, deve ser automaticamente inscrito no TalentLMS para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

