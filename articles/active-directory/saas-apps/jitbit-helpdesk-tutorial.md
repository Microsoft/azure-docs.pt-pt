---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Jitbit Helpdesk [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Jitbit Helpdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 29addcd62afd193af83196b2d942e9778ff3f031
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67099414"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Tutorial: Integração de Diretório Ativo Azure com Helpdesk Jitbit

Neste tutorial, aprende-se a integrar o Jitbit Helpdesk com o Azure Ative Directory (Azure AD).
Integrar o Serviço de Ajuda Jitbit com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Jitbit Helpdesk.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Serviço de Ajuda Jitbit (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Jitbit Helpdesk, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura única ativada por sinal de apoio jitbit

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Jitbit Helpdesk suporta **SP** iniciado SSO

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Adicionar O Helpdesk Jitbit da galeria

Para configurar a integração do Jitbit Helpdesk no Azure AD, precisa adicionar o Jitbit Helpdesk da galeria à sua lista de aplicações geridas do SaaS.

**Para adicionar o Serviço de Apoio Jitbit à galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite o **Helpdesk Jitbit,** selecione **O Helpdesk Jitbit** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Jitbit Helpdesk na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com a Jitbit Helpdesk com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Helpdesk Jitbit.

Para configurar e testar o único sign-on azure ad com o Jitbit Helpdesk, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On único do Helpdesk Jitbit](#configure-jitbit-helpdesk-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador do teste Jitbit Helpdesk](#create-jitbit-helpdesk-test-user)** - para ter uma contrapartida da Britta Simon no Serviço de Ajuda Jitbit que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com o Jitbit Helpdesk, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações da **Jitbit Helpdesk,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Jitbit Helpdesk Domain e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o URL de Início de Sinal real. Contacte a equipa de suporte do [Cliente da Jitbit Helpdesk](https://www.jitbit.com/support/) para obter este valor.

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL como seguinte:`https://www.jitbit.com/web-helpdesk/`

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **De Ajuda Jitbit, copie** os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-jitbit-helpdesk-single-sign-on"></a>Configure Jitbit Helpdesk Single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Jitbit Helpdesk como administrador.

1. Na barra de ferramentas em cima, clique em **Administração**.

    ![Administration](./media/jitbit-helpdesk-tutorial/ic777681.png "Administração")

1. Clique em **configurações gerais**.

    ![Utilizadores, empresas e permissões](./media/jitbit-helpdesk-tutorial/ic777680.png "Utilizadores, empresas e permissões")

1. Na secção de configurações de **autenticação,** execute os seguintes passos:

    ![Definições de autenticação](./media/jitbit-helpdesk-tutorial/ic777683.png "Definições de autenticação")

    a. Selecione Ativar o **sinal único SAML 2.0,** para iniciar sessão utilizando o Single Sign-On (SSO), com **oneLogin**.

    b. Na caixa de texto **endPoint URL,** colá o valor do URL de **Login** que copiou do portal Azure.

    c. Abra o seu certificado codificado **base-64** no bloco de notas, copie o conteúdo do mesmo na sua área de sobrercopiadora e, em seguida, cole-o na caixa de texto **x.509 Certificado**

    d. Clique em **Guardar alterações**.

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

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao Jitbit Helpdesk.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Jitbit Helpdesk**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Jitbit Helpdesk**.

    ![O link de Helpdesk Jitbit na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-jitbit-helpdesk-test-user"></a>Criar o utilizador do teste de helpdesk Jitbit

Para permitir que os utilizadores da AD Azure inscrevam-se no Serviço de Ajuda Jitbit, devem ser aprovisionados no Serviço de Ajuda Jitbit. No caso do Serviço de Ajuda Jitbit, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu inquilino da **Jitbit Helpdesk.**

1. No menu em cima, clique em **Administração**.

    ![Administration](./media/jitbit-helpdesk-tutorial/ic777681.png "Administração")

1. Clique em **Utilizadores, empresas e permissões.**

    ![Utilizadores, empresas e permissões](./media/jitbit-helpdesk-tutorial/ic777682.png "Utilizadores, empresas e permissões")

1. Clique em **Adicionar utilizador**.

    ![Adicionar utilizador](./media/jitbit-helpdesk-tutorial/ic777685.png "Adicionar utilizador")

1. Na secção Criar, digite os dados da conta Azure AD que pretende fornecer da seguinte forma:

    ![Criar](./media/jitbit-helpdesk-tutorial/ic777686.png "Criar")

   a. Na caixa de texto **username,** digite o nome de utilizador do utilizador como **BrittaSimon**.

   b. Na caixa de texto **e-mail,** digite o e-mail do utilizador como **BrittaSimon@contoso.com**.

   c. Na caixa de texto **First Name,** digite o primeiro nome do utilizador como **Britta**.

   d. Na caixa de texto **Last Name,** digite o sobrenome do utilizador como **Simon**.

   e. Clique em **Criar**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador do Jitbit Helpdesk ou APIs fornecidas pela Jitbit Helpdesk para fornecer contas de utilizador da Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Jitbit Helpdesk no Painel de Acesso, deve ser automaticamente inscrito no Helpdesk Jitbit para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
