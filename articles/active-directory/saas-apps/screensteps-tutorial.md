---
title: 'Tutorial: Integração do Diretório Ativo Azure com ScreenSteps [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo azure e o ScreenSteps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 864a9243a9f737506fd4d8cbc3940d7a86711f20
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67091653"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Tutorial: Integração de Diretório Ativo Azure com ScreenSteps

Neste tutorial, aprende-se a integrar screenSteps com o Azure Ative Directory (Azure AD).
Integrar ScreenSteps com AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao ScreenSteps.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no ScreenSteps (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com screenSteps, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por sinal único ScreenSteps

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* ScreenSteps suporta **SP** iniciado SSO

## <a name="adding-screensteps-from-the-gallery"></a>Adicionar ScreenSteps da galeria

Para configurar a integração do ScreenSteps no AD Azure, é necessário adicionar ScreenSteps da galeria à sua lista de aplicações SaaS geridas.

**Para adicionar ScreenSteps da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **ScreenSteps**, selecione **ScreenSteps** do painel de resultados e, em seguida, clique em adicionar o botão **Adicionar** a aplicação.

     ![ScreenSteps na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com ScreenSteps com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no ScreenSteps.

Para configurar e testar o único sinal de Azure AD com screenSteps, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure screenSteps single sign-On](#configure-screensteps-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador](#create-screensteps-test-user)** de teste ScreenSteps - para ter uma contraparte de Britta Simon no ScreenSteps que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sinal de Azure AD com screenSteps, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **ScreenSteps,** selecione **single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![ScreenSteps Domain e URLs informações únicas de inscrição](common/sp-signonurl.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<tenantname>.ScreenSteps.com`

    > [!NOTE]
    > Este valor não é real. Atualize este valor com o URL de Sign-On real, o que é explicado mais tarde neste tutorial.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **Configurar ScreenSteps,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-screensteps-single-sign-on"></a>Configure ScreenSteps Single Sign-On

1. Numa janela diferente do navegador web, inicie sessão no site da empresa ScreenSteps como administrador.

1. Clique em **Definições de Conta**.

    ![Gestão de conta](./media/screensteps-tutorial/ic778523.png "Gestão de contas")

1. Clique **em Iniciar Um Único Sinal**.

    ![Autenticação remota](./media/screensteps-tutorial/ic778524.png "Autenticação remota")

1. Clique **em criar um ponto final de inscrição único**.

    ![Autenticação remota](./media/screensteps-tutorial/ic778525.png "Autenticação remota")

1. Na secção Criar um **ponto final de inscrição único,** execute os seguintes passos:

    ![Criar um ponto final de autenticação](./media/screensteps-tutorial/ic778526.png "Criar um ponto final de autenticação")

    a. Na caixa de texto **Title,** escreva um título.

    b. Na **lista** modo, selecione **SAML**.

    c. Clique em **Criar**.

1. **Editar** o novo ponto final.

    ![Editar ponto final](./media/screensteps-tutorial/ic778528.png "Editar ponto final")

1. Na secção Editar Um Ponto Final de **Inscrição Única,** execute os seguintes passos:

    ![Ponto final de autenticação remota](./media/screensteps-tutorial/ic778527.png "Ponto final de autenticação remota")

    a. Clique em **carregar novo ficheiro De Certificado SAML**e, em seguida, faça upload do certificado, que descarregou do portal Azure.

    b. Colar o valor URL de **Login,** que copiou do portal Azure para a caixa de texto URL **de Login Remoto.**

    c. Colar valor URL de **Logout,** que copiou do portal Azure para a caixa de texto **URL Log out.**

    d. Selecione um **Grupo** para atribuir utilizadores quando estes são provisionados.

    e. Clique em **Atualizar**.

    f. Copie o URL do **Consumidor SAML** para a área de sobredisque e cole na caixa de texto **url sign-on** na secção **basic SAML Configuração** no portal Azure.

    g. Volte ao ponto final de **inscrição único de edição**.

    h. Clique no botão **'Ver', para** utilizar este ponto final para todos os utilizadores que iniciarem sessão no ScreenSteps. Em alternativa, pode clicar no botão **Adicionar ao Site** para utilizar este ponto final para sites específicos no **ScreenSteps**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No tipo de campo de **nome do utilizador****brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao ScreenSteps.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **ScreenSteps**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **ScreenSteps**.

    ![O link ScreenSteps na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-screensteps-test-user"></a>Criar o utilizador de teste ScreenSteps

Nesta secção, cria-se uma utilizadora chamada Britta Simon no ScreenSteps. Trabalhe com a equipa de suporte ao Cliente [ScreenSteps](https://www.screensteps.com/contact)para adicionar os utilizadores na plataforma ScreenSteps. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo ScreenSteps no Painel de Acesso, deve ser automaticamente inscrito nos ScreenSteps para os quais configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)