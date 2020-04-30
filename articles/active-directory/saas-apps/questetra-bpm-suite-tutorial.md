---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Suite Questetra BPM [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Questetra BPM Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: f58d6cbc6ec04e51e105662dff31c60ff502584c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67093364"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Tutorial: Integração de Diretório Ativo Azure com a Suite Questetra BPM

Neste tutorial, você aprende a integrar a Suite Questetra BPM com o Azure Ative Directory (Azure AD).
Integrar a Suite Questetra BPM com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso à Suite Questetra BPM.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Suite Questetra BPM (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Suite Questetra BPM, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Questetra BPM Suite única subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Questetra BPM Suite suporta **SP** iniciado SSO

## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Adicionar Suíte Questetra BPM da galeria

Para configurar a integração da Suite Questetra BPM em Azure AD, você precisa adicionar Questetra BPM Suite da galeria à sua lista de aplicações saaS geridas.

**Para adicionar a Suite Questetra BPM da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite a Suite Questetra BPM,** selecione **Questetra BPM Suite** do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![Suíte Questetra BPM na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com a Suite Questetra BPM com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Suite Questetra BPM.

Para configurar e testar o único sinal de Azure AD com a Suite Questetra BPM, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On único da Suite Questetra BPM](#configure-questetra-bpm-suite-single-sign-on)** - para configurar as definições de entrada única no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador de teste Questetra BPM Suite](#create-questetra-bpm-suite-test-user)** - para ter uma contrapartida da Britta Simon na Suite Questetra BPM que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com a Suite Questetra BPM, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **da Suite Questetra BPM,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Questetra BPM Suite Domain e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<subdomain>.questetra.net/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL e Identificador de Sinal real. Você pode obter estes valores a partir da secção **de Informação SP** no seu site da empresa **Questetra BPM Suite,** o que é explicado mais tarde no tutorial ou contatar a equipa de suporte ao [Cliente Da Suite Questetra BPM.](https://www.questetra.com/contact/) Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na **secção Deconfiguração da Suite Questetra BPM,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-questetra-bpm-suite-single-sign-on"></a>Configure Questetra BPM Suite Single Sign-On

1. Numa janela de navegador web diferente, inscreva-se no site da empresa **Questetra BPM Suite** como administrador.

2. No menu em cima, clique em **Definições do Sistema**. 
   
    ![Sinal único azure AD][10]

3. Para abrir a página **SingleSignOnSAML,** clique em **SSO (SAML)**. 
   
    ![Sinal único azure AD][11]

4. No site da empresa **Questetra BPM Suite,** na secção **Informação SP,** execute os seguintes passos:

    a. Copie o **URL ACS**e cole-o na caixa de texto **Sign On URL** na secção de **configuração SAML Básica** do portal Azure.
    
    b. Copie o ID da **Entidade**e cole-o na caixa de texto **do Identificador** na secção **de Configuração SAML Básica** do portal Azure.

5. No site da empresa **Questetra BPM Suite,** execute os seguintes passos: 
   
    ![Configurar um único sinal][15]
   
    a. **Selecione ativar um único sinal .**
   
    b. Na caixa de texto **Id da Entidade,** colhe o valor do **Identificador Azure AD** que copiou do portal Azure.
    
    c. Na caixa de texto URL da **página de entrada,** colá o valor do URL de **Login** que copiou do portal Azure.
    
    d. Na caixa de texto URL da **página de sign-out,** colhe o valor do URL de **Logout** que copiou do portal Azure.
    
    e. Na caixa de texto em `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` **formato NameID,** escreva .

    f. Abra o seu certificado codificado **Base-64** no bloco de notas descarregado do portal Azure, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto do certificado de **validação.** 

    g. Clique em **Guardar**.

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso à Suite Questetra BPM.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Questetra BPM Suite**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Questetra BPM Suite**.

    ![O link Questetra BPM Suite na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-questetra-bpm-suite-test-user"></a>Criar o utilizador de teste Questetra BPM Suite

O objetivo desta secção é criar um utilizador chamado Britta Simon na Suite Questetra BPM.

**Para criar um utilizador chamado Britta Simon na Suite Questetra BPM, execute os seguintes passos:**

1. Inscreva-se no site da empresa Questetra BPM Suite como administrador.

2. Vá às **Definições do sistema > Lista de Utilizadores > novo utilizador**.
 
3. No diálogo new user, execute os seguintes passos: 
   
    ![Criar o utilizador do teste][300] 
   
    a. Na caixa de texto **Name,** britta.simon@contoso.comdigite o **nome** do utilizador .
   
    b. Na caixa de texto **por e-mail,** digite o **e-mail** do utilizador. britta.simon@contoso.com
   
    c. Na caixa de texto **Password,** digite uma **palavra-passe** do utilizador.
    
    d. Clique **em Adicionar novo utilizador**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Da Suite Questetra BPM no Painel de Acesso, deverá ser automaticamente inscrito na Suite Questetra BPM para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png