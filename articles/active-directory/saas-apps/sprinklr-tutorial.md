---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Sprinklr Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Sprinklr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 9e4025d040783bff1cd85fb46d571e3a89967892
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67089648"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Tutorial: Integração do Diretório Ativo Azure com a Sprinklr

Neste tutorial, aprende-se a integrar o Sprinklr com o Azure Ative Directory (Azure AD).
Integrar o Sprinklr com a AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso a Sprinklr.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Sprinklr (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Sprinklr, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de assinatura ativada por sprinklr

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Sprinklr apoia **SP** iniciado SSO

## <a name="adding-sprinklr-from-the-gallery"></a>Adicionando Sprinklr da galeria

Para configurar a integração do Sprinklr em Azure AD, você precisa adicionar Sprinklr da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Sprinklr da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Sprinklr,** selecione **Sprinklr** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Sprinklr na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com sprinklr com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Sprinklr.

Para configurar e testar o único sinal de Azure AD com o Sprinklr, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sprinklr Single Sign-On](#configure-sprinklr-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie um utilizador de teste Sprinklr](#create-sprinklr-test-user)** - para ter uma contrapartida de Britta Simon em Sprinklr que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure com o Sprinklr, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Sprinklr,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Sprinklr Domain e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<subdomain>.sprinklr.com`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<subdomain>.sprinklr.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de suporte ao [Cliente Sprinklr](https://www.sprinklr.com/contact-us/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **Configurar Sprinklr,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-sprinklr-single-sign-on"></a>Configure Sprinklr Single Sign-On

1. Numa janela de navegador web diferente, inicie sessão no site da sua empresa Sprinklr como administrador.

1. Ir para **as Definições \> **de Administração .

    ![Administration](./media/sprinklr-tutorial/ic782907.png "Administração")

1. Vá para gerir o **single \> sign** on do painel esquerdo.

    ![Gerir parceiro](./media/sprinklr-tutorial/ic782908.png "Gerir parceiro")

1. Clique em **+Adicionar sinais individuais**.

    ![Inscrições individuais](./media/sprinklr-tutorial/ic782909.png "Inscrições individuais")

1. No **Sinal Único na** página, execute os seguintes passos:

    ![Inscrições individuais](./media/sprinklr-tutorial/ic782910.png "Inscrições individuais")

    a. Na caixa de texto **Name,** escreva um nome para a sua configuração (por exemplo: *WAADSSOTest*).

    b. Selecione **Ativado**.

    c. Selecione **Utilize o novo Certificado SSO**.

    d. Abra o seu certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo na sua área de receita e, em seguida, cole-o na caixa de texto do Certificado de Fornecedor de **Identidade.**

    e. Colar o valor do **Identificador AD Azure** que copiou do Portal Azure para a caixa de texto **Id entidade.**

    f. Colar o valor URL de **Login** que copiou do Portal Azure para a caixa de texto URL do Fornecedor de **Identidade.**

    g. Colhe o valor URL de **Logout** que copiou do Portal Azure para a caixa de texto **url de logout** do fornecedor de identidade.

    h. Como Tipo de ID do **utilizador SAML,** selecione **Afirmação contém o nome de utilizador sprinklr.com do utilizador**.

    i. Como localização de ID do **utilizador SAML,** selecione id do **utilizador está no elemento identificador de nome da declaração de Assunto**.

    j. Clique em **Guardar**.

    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao Sprinklr.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Sprinklr**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Sprinklr**.

    ![A ligação Sprinklr na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-sprinklr-test-user"></a>Criar o utilizador de teste Sprinklr

1. Faça login no site da sua empresa Sprinklr como administrador.

1. Ir para **as Definições \> **de Administração .

    ![Administration](./media/sprinklr-tutorial/ic782907.png "Administração")

1. Vá para **gerir os utilizadores \> ** do cliente a partir do painel esquerdo.

    ![Definições](./media/sprinklr-tutorial/ic782914.png "Definições")

1. Clique em **Adicionar Utilizador**.

    ![Definições](./media/sprinklr-tutorial/ic782915.png "Definições")

1. No diálogo do **utilizador Editar,** execute os seguintes passos:

    ![Editar utilizador](./media/sprinklr-tutorial/ic782916.png "Editar utilizador")

    a. Nas caixas de texto **e-mail**, **Primeiro Nome** e **Apelido,** escreva a informação de uma conta de utilizador DaD Azure que pretende fornecer.

    b. Selecione **Password Disabled**.

    c. Selecione **Idioma**.

    d. Selecione **Type de utilizador**.

    e. Clique em **Atualizar**.

    > [!IMPORTANT]
    > **A Palavra-Passe Desativada** deve ser selecionada para permitir que um utilizador faça login através de um fornecedor de identidade. 

1. Ir para **o Papel**, e, em seguida, executar os seguintes passos:

    ![Funções de Parceiro](./media/sprinklr-tutorial/ic782917.png "Funções de Parceiro")

    a. A partir da lista **Global,** selecione **ALL_Permissions**.  

    b. Clique em **Atualizar**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador Sprinklr ou APIs fornecidas pela Sprinklr para fornecer contas de utilizador da Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Sprinklr no Painel de Acesso, deve ser automaticamente inscrito no Sprinklr para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
