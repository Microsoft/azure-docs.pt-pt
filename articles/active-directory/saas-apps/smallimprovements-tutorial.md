---
title: 'Tutorial: Integração do Diretório Ativo Azure com Pequenas Melhorias [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e as Pequenas Melhorias.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: d2d0bbc7a6e1c680434041d1b9d55e39a96b6f44
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67090367"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Tutorial: Integração de Diretório Ativo Azure com Pequenas Melhorias

Neste tutorial, aprende-se a integrar pequenas melhorias com o Azure Ative Directory (Azure AD).
Integrar pequenas melhorias com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Pequenas Melhorias.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos em Pequenas Melhorias (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com Pequenas Melhorias, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Pequenas melhorias únicas subscrição ativada por sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Pequenas melhorias suporta **SP** iniciado SSO

## <a name="adding-small-improvements-from-the-gallery"></a>Adicionar pequenas melhorias da galeria

Para configurar a integração de Pequenas Melhorias em Anúncio saque a Azure, é necessário adicionar Pequenas Melhorias da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Pequenas Melhorias da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Pequenas Melhorias,** selecione **Pequenas Melhorias** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Pequenas melhorias na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com pequenas melhorias com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Pequenas Melhorias.

Para configurar e testar o único sinal de Azure AD com Pequenas Melhorias, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure pequenas melhorias de entrada única -](#configure-small-improvements-single-sign-on)** para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Criar pequenos utilizadores](#create-small-improvements-test-user)** de testes de melhorias - para ter uma contrapartida de Britta Simon em Pequenas Melhorias que está ligada à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da AD Azure com Pequenas Melhorias, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Small Improvements,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Pequenas melhorias Domain e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<subdomain>.small-improvements.com`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<subdomain>.small-improvements.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de suporte do [Cliente de Pequenas Melhorias](mailto:support@small-improvements.com) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **Configurar Pequenas Melhorias,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-small-improvements-single-sign-on"></a>Configurar pequenas melhorias únicas de inscrição

1. Noutra janela do navegador, inscreva-se no site da sua empresa Small Improvements como administrador.

1. A partir da página principal do painel, clique no botão **administração** à esquerda.

    ![Configurar um único sinal](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. Clique no botão **SAML SSO** da secção **Integrações.**

    ![Configurar um único sinal](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. Na página de Configuração SSO, execute os seguintes passos:

    ![Configurar um único sinal](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. Na caixa de texto **HTTP Endpoint,** cola o valor do URL de **Login,** que copiou do portal Azure.

    b. Abra o seu certificado descarregado no Bloco de Notas, copie o conteúdo e, em seguida, cole-o na caixa de texto **x509 Certificate.** 

    c. Se desejar ter a opção de autenticação do formulário SSO e Login disponível para os utilizadores, verifique também o **acesso enable via opção de login/senha.**  

    d. Introduza o valor adequado para nomear o botão sso login na caixa de texto **SAML Prompt.**  

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
  
    b. No tipo de campo de **nome do utilizador****brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso a Pequenas Melhorias.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Pequenas Melhorias**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Small Improvements**.

    ![O link de pequenas melhorias na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-small-improvements-test-user"></a>Criar pequenos utilizadores de teste de melhorias

Para permitir que os utilizadores de Anúncios Azure entrem em Pequenas Melhorias, devem ser aprovisionados em Pequenas Melhorias. No caso de Pequenas Melhorias, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da sua empresa small improvements como administrador.

1. A partir da página Inicial, vá ao menu à esquerda, clique em **Administração**.

1. Clique no botão **de Diretório** do Utilizador na secção de Gestão de Utilizadores.

    ![Criação de um utilizador de teste azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. Clique em **Adicionar utilizadores**.

    ![Criação de um utilizador de teste azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. No diálogo **Adicionar Utilizadores,** execute os seguintes passos: 

    ![Criação de um utilizador de teste azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)

    a. Introduza o **primeiro nome** de utilizador como **Britta.**

    b. Introduza o **último nome** de utilizador como **Simon.**

    c. Introduza o **e-mail** do utilizador como **brittasimon@contoso.com**.

    d. Pode também optar por introduzir a mensagem pessoal na caixa de **correio eletrónico enviar.** Se não quiser enviar a notificação, desverifique esta caixa de verificação.

    e. Clique em **criar utilizadores**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Small Improvements no Painel de Acesso, deve ser automaticamente inscrito nas Pequenas Melhorias para as quais configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)