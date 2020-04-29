---
title: 'Tutorial: Integração de Diretório Ativo Azure com userEcho [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo azure e o UserEcho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bedd916b-8f69-4b50-9b8d-56f4ee3bd3ed
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 59d61eda7002fe46cf99fac63822b2333b2d64b5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67087775"
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Tutorial: Integração de Diretório Ativo Azure com userEcho

Neste tutorial, aprende-se a integrar o UserEcho com o Azure Ative Directory (Azure AD).
Integrar o UserEcho com o Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao UserEcho.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no UserEcho (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o UserEcho, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Subscrição ativada por um único sinal do UserEcho

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* UserEcho suporta **SP** iniciado SSO

## <a name="adding-userecho-from-the-gallery"></a>Adicionar UserEcho da galeria

Para configurar a integração do UserEcho em AD Azure, é necessário adicionar o UserEcho da galeria à sua lista de aplicações SaaS geridas.

**Para adicionar userEcho da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **UserEcho**, selecione **UserEcho** do painel de resultados e, em seguida, clique em adicionar o botão **Adicionar** a aplicação.

     ![UserEcho na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com userEcho com base num utilizador de teste chamado **Britta Simon**.
Para que o início de sessão simples funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no UserEcho.

Para configurar e testar o único sinal de Azure AD com o UserEcho, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure userEcho single sign-on](#configure-userecho-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador](#create-userecho-test-user)** de teste UserEcho - para ter uma contraparte da Britta Simon no UserEcho que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sinal de Azure AD com o UserEcho, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **UserEcho,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![UserEcho Domain e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<companyname>.userecho.com/`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de suporte do [Cliente UserEcho](https://feedback.userecho.com/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

4. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **Configurar userEcho,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-userecho-single-sign-on"></a>Configure UserEcho Single Sign-On

1. Noutra janela do navegador, inscreva-se no site da sua empresa UserEcho como administrador.

2. Na barra de ferramentas em cima, clique no nome do utilizador para expandir o menu e, em seguida, clique em **Configurar**.
   
    ![Configurar um único sinal](./media/userecho-tutorial/tutorial_userecho_06.png) 

3. Clique em **Integrações**.
   
    ![Configurar um único sinal](./media/userecho-tutorial/tutorial_userecho_07.png) 

4. Clique no **Website**e, em seguida, clique em **um único sinal (SAML2)**.
   
    ![Configurar um único sinal](./media/userecho-tutorial/tutorial_userecho_08.png) 

5. Na página **Single sign-on (SAML),** execute os seguintes passos:
   
    ![Configurar um único sinal](./media/userecho-tutorial/tutorial_userecho_09.png)
    
    a. Como **ativado por SAML,** selecione **Sim**.
    
    b. Colar **URL de Login**, que copiou do portal Azure para a caixa de texto **URL SAML SSO.**
    
    c. Coque o URL de **Logout,** que copiou do portal Azure para a caixa de texto URL **de Logout Remoto.**
    
    d. Abra o seu certificado descarregado no Bloco de Notas, copie o conteúdo e, em seguida, cole-o na caixa de texto **x.509.**
    
    e. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **User name** tipo brittasimon@yourcompanydomain.extensionde campo do nome do utilizador . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que a Britta Simon utilize um único signo Azure, concedendo acesso ao UserEcho.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **UserEcho**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **UserEcho**.

    ![O link UserEcho na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-userecho-test-user"></a>Criar utilizador de teste UserEcho

O objetivo desta secção é criar um utilizador chamado Britta Simon no UserEcho.

**Para criar um utilizador chamado Britta Simon no UserEcho, execute os seguintes passos:**

1. Inscreva-se no site da sua empresa UserEcho como administrador.

2. Na barra de ferramentas em cima, clique no nome do utilizador para expandir o menu e, em seguida, clique em **Configurar**.
   
    ![Configurar um único sinal](./media/userecho-tutorial/tutorial_userecho_06.png)

3. Clique em **Utilizadores,** para expandir a secção **Utilizadores.**
   
    ![Configurar um único sinal](./media/userecho-tutorial/tutorial_userecho_10.png)

4. Clique em **Utilizadores**.
   
    ![Configurar um único sinal](./media/userecho-tutorial/tutorial_userecho_11.png)

5. Clique **em Convidar um novo utilizador.**
   
    ![Configurar um único sinal](./media/userecho-tutorial/tutorial_userecho_12.png)

6. No **Convite de um novo** diálogo do utilizador, execute os seguintes passos:
   
    ![Configurar um único sinal](./media/userecho-tutorial/tutorial_userecho_13.png)

    a. Na caixa de texto **Name,** escreva o nome do utilizador como Britta Simon.
    
    b.  Na caixa de texto **e-mail,** Brittasimon@contoso.comdigite o endereço de e-mail do utilizador como .
    
    c. Clique em **Convidar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo UserEcho no Painel de Acesso, deverá ser automaticamente inscrito no UserEcho para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

