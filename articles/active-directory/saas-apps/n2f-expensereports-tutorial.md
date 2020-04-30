---
title: 'Tutorial: Integração do Diretório Ativo Azure com a N2F - Relatórios de Despesas Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o N2F - Relatórios de despesas.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 11f5e2f7763008c3af09c5367d90265af6a9653a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73161279"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Tutorial: Integração do Diretório Ativo Azure com a N2F - Relatórios de despesas

Neste tutorial, aprende-se a integrar a N2F - Relatórios de despesas com o Azure Ative Directory (Azure AD).
Integrando n2F - Relatórios de despesas com AD Azure proporcionam-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a N2F - Relatórios de despesas.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na N2F - Relatórios de despesas (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a N2F - Relatórios de despesas, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* N2F - Relatórios de despesas única subscrição ativada por sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* N2F - Relatórios de despesas **suportaS SP** e **IDP** iniciadoS SSO

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Adicionar N2F - Relatórios de despesas da galeria

Para configurar a integração de N2F - Relatórios de despesas em Azure AD, você precisa adicionar Relatórios de Despesas N2F - Despesas da galeria à sua lista de aplicações saaS geridas.

**Para adicionar N2F - Relatórios de despesas da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, tipo **N2F - Relatórios de despesas,** selecione **N2F - Relatórios** de despesas do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![N2F - Relatórios de despesas na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com n2F - Relatórios de despesas baseados num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na N2F - Relatórios de despesas.

Para configurar e testar o único sinal de Azure AD com a N2F - Relatórios de despesas, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure N2F - Relatórios de despesas single sign-on](#configure-n2f---expense-reports-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Criar relatórios N2F - Relatórios](#create-n2f---expense-reports-test-user)** de despesas utilizador - para ter uma contrapartida de Britta Simon na N2F - Relatórios de despesas que estão ligados à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com n2F - Relatórios de despesas, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações de relatórios de despesas **N2F** - Despesas, selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado **idp,** o utilizador não tem de realizar quaisquer passos, uma vez que a aplicação já está pré-integrada com o Azure.

    ![N2F - Relatórios de despesas Informações únicas de assinatura](common/preintegrated.png)

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    ![N2F - Relatórios de despesas Informações únicas de assinatura](common/metadata-upload-additional-signon.png)

    Na caixa de texto **de URL sign-on,** escreva um URL:`https://www.n2f.com/app/`

6. Na configuração do Single Sign-On com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

7. Na secção **Configurar as minhas Políticas,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-n2f---expense-reports-single-sign-on"></a>Configure N2F - Relatórios de despesas single sign-on

1. Numa janela diferente do navegador web, inscreva-se no site da empresa N2F - Relatórios de despesas como administrador.

2. Clique em **Definições** e, em seguida, selecione **Definições de Adiantamento** a partir da queda.

    ![N2F - Configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/configure1.png)

3. Selecione o separador de **definições de conta.**

    ![N2F - Configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/configure2.png)

4. **Selecione Autenticação** e, em seguida, selecione + Adicione um separador de método de **autenticação.**

    ![N2F - Configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/configure3.png)

5. Selecione **SAML Microsoft Office 365** como método de autenticação.

    ![N2F - Configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/configure4.png)

6. Na secção do método de **autenticação,** execute os seguintes passos:

    ![N2F - Configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/configure5.png)

    a. Na caixa de texto **Id da Entidade,** colá o valor do **Identificador AD Azure,** que copiou do portal Azure.

    b. Na caixa de texto URL de **Metadados,** colá o valor url de **metadados da Federação de Aplicações,** que copiou do portal Azure.

    c. Clique em **Guardar**.

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso aos relatórios de despesas da N2F - Despesas.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações,** em seguida, selecione **N2F - Relatórios**de despesas .

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **N2F - Relatórios**de despesas .

    ![N2F - Ligação de relatórios de despesas na lista de Candidaturas](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-n2f---expense-reports-test-user"></a>Criar N2F - Relatórios de despesas testem utilizador

Para permitir que os utilizadores da AD Azure entrem nos relatórios de despesas da N2F - Devem ser aprovisionados nos relatórios de despesas da N2F - Despesas. No caso dos relatórios de despesas da N2F- Despesas, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu N2F - Relatórios de despesas do site da empresa como administrador.

2. Clique em **Definições** e, em seguida, selecione **Definições de Adiantamento** a partir da queda.

    ![N2F - Adicionar despesas ao utilizador](./media/n2f-expensereports-tutorial/configure1.png)

3. Selecione o separador **Utilizadores** do painel de navegação esquerdo.

    ![N2F - Configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/user1.png)

4. Selecione + Novo separador **de utilizador.**

    ![N2F - Configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/user2.png)

5. Na secção **Utilizador,** execute os seguintes passos:

    ![N2F - Configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/user3.png)

    a. Na caixa de texto de **endereço de e-mail,** introduza o endereço de e-mail de um utilizador como **o brittasimon\@contoso.com**.

    b. Na caixa de texto **de primeiro nome,** introduza o primeiro nome de utilizador como **Britta**.

    c. Na caixa de texto **Name,** introduza o nome de utilizador como **BrittaSimon**.

    d. Escolha **Role, Direct manager (N+1)** e **Divisão** de acordo com o seu requisito de organização.

    e. Clique em **Validar e enviar convite**.

    > [!NOTE]
    > Se estiver a enfrentar algum problema ao adicionar o utilizador, contacte a Equipa de Suporte de [Relatórios de Despesas da N2F - Despesas](mailto:support@n2f.com)

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no N2F - Relatórios de despesas no Painel de Acesso, deverá ser automaticamente inscrito no N2F - Relatórios de despesas para os quais configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

