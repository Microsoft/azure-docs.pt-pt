---
title: 'Tutorial: Integração do Diretório Ativo Azure com pagerDuty [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o PagerDuty.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: b7a522aaf35303bbd87e7aafe65b1302f1b98bc0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095337"
---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Tutorial: Integração do Diretório Ativo Azure com pagerDuty

Neste tutorial, aprende-se a integrar o PagerDuty com o Azure Ative Directory (Azure AD).
Integrar o PagerDuty com a AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao PagerDuty.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no PagerDuty (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o PagerDuty, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por sinal único PagerDuty

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* PagerDuty suporta **SP** iniciado SSO

## <a name="adding-pagerduty-from-the-gallery"></a>Adicionando PagerDuty da galeria

Para configurar a integração do PagerDuty em Azure AD, precisa adicionar pagerDuty da galeria à sua lista de aplicações SaaS geridas.

**Para adicionar PagerDuty da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **PagerDuty**, selecione **PagerDuty** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![PagerDuty na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com pagerDuty com base num utilizador de teste chamado **Britta Simon**.
Para que o início de sessão simples funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no PagerDuty.

Para configurar e testar o único sinal de Azure AD com pagerDuty, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On único do PagerDuty](#configure-pagerduty-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Crie o utilizador do **[teste PagerDuty](#create-pagerduty-test-user)** - para ter uma contrapartida de Britta Simon no PagerDuty que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure com pagerDuty, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **PagerDuty,** selecione **single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![PagerDuty Domain e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<tenant-name>.pagerduty.com`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de [suporte ao Cliente PagerDuty](https://www.pagerduty.com/support/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **Configurar PagerDuty,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-pagerduty-single-sign-on"></a>Configure PagerDuty Single Sign-On

1. Numa janela diferente do navegador web, inicie sessão no site da sua empresa Pagerduty como administrador.

2. No menu em cima, clique em **Definições de Conta**.

    ![Definições de conta](./media/pagerduty-tutorial/ic778535.png "Definições de conta")

3. Clique **em Iniciar Um Único Sinal**.

    ![Início de sessão único](./media/pagerduty-tutorial/ic778536.png "Início de sessão único")

4. Na página **Enable Single Sign-on (SSO),** execute os seguintes passos:

    ![Ativar um único sinal](./media/pagerduty-tutorial/ic778537.png "Ativar um único sinal")

    a. Abra o seu certificado codificado base-64 descarregado do portal Azure no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **x.509 Certificate**
  
    b. Na caixa de texto **login URL,** cola **o URL de Login** que copiou do portal Azure.
  
    c. Na caixa de texto **logout URL,** colhe o **URL de Logout** que copiou do portal Azure.

    d. **Selecione Permitir o nome de utilizador/início da palavra-passe**.

    e. Selecione Exigir caixa de verificação de comparação de contexto de **autenticação EXACT.**

    f. Clique em **Guardar Alterações**.

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao PagerDuty.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **PagerDuty**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **PagerDuty**.

    ![O link PagerDuty na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-pagerduty-test-user"></a>Criar o utilizador do teste PagerDuty

Para permitir que os utilizadores de Anúncios Azure entrem no PagerDuty, devem ser aprovisionados no PagerDuty.  
No caso do PagerDuty, o provisionamento é uma tarefa manual.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador pagerduty ou APIs fornecidas pela Pagerduty para fornecer contas de utilizador do Diretório Ativo Azure.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Faça login no seu inquilino **pagerduty.**

2. No menu em cima, clique em **Utilizadores.**

3. Clique em **Adicionar Utilizadores**.
   
    ![Add Users](./media/pagerduty-tutorial/ic778539.png "Adicionar Utilizadores")

4.  No diálogo **Convidar a sua equipa,** execute os seguintes passos:
   
    ![Convide a sua equipa](./media/pagerduty-tutorial/ic778540.png "Convide a sua equipa")

    a. Digite o **Primeiro e Último Nome** de utilizador como **Britta Simon.** 
   
    b. Introduza o endereço de **e-mail** de utilizadores como **\@o brittasimon contoso.com**.
   
    c. Clique em **Adicionar,** e depois clique em **Enviar Convites**.
   
    >[!NOTE]
    >Todos os utilizadores adicionados receberão um convite para criar uma conta PagerDuty.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo PagerDuty no Painel de Acesso, deverá ser automaticamente inscrito no PagerDuty para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

