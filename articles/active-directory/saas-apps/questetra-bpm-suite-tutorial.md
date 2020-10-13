---
title: 'Tutorial: Integração do Azure Ative Directory com a Suite Questetra BPM Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Questetra BPM Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 11a6df348ae623ccf75e61bc1abbb5e28c832237
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88548976"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Tutorial: Integração do Azure Ative Directory com a Suite Questetra BPM

Neste tutorial, aprende-se a integrar a Suite Questetra BPM com o Azure Ative Directory (Azure AD).
A integração da Suite Questetra BPM com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso à Suite Questetra BPM.
* Pode permitir que os seus utilizadores se inscrevam automaticamente na Suite Questetra BPM (Sign-on Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a Suite Questetra BPM, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por Questetra BPM Suite

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Questetra BPM Suite suporta **SSO** iniciado SP

## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Adicionar questetra BPM Suite da galeria

Para configurar a integração da Suite Questetra BPM em Azure AD, é necessário adicionar a Suite Questetra BPM da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar a Suite Questetra BPM da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Questetra BPM Suite**, selecione **Questetra BPM Suite** a partir do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![Questetra BPM Suite na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com a Questetra BPM Suite com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Suite Questetra BPM.

Para configurar e testar o único sinal de Azure AD com a Suite Questetra BPM, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure questetra BPM Suite Single Sign-On](#configure-questetra-bpm-suite-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Questetra BPM Suite test user](#create-questetra-bpm-suite-test-user)** - para ter uma contraparte de Britta Simon em Questetra BPM Suite que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com questetra BPM Suite, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Questetra BPM Suite,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Questetra BPM Suite Domain e URLs informações únicas de súbdula](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<subdomain>.questetra.net/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL e o identificador de Sign-On reais. Pode obter estes valores a partir da secção **de Informação SP** no seu site da empresa **Questetra BPM Suite,** que é explicado mais tarde no tutorial ou contacte a [equipa de suporte do Cliente Questetra BPM Suite](https://www.questetra.com/contact/). Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configuração Questetra BPM Suite,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-questetra-bpm-suite-single-sign-on"></a>Configurar questetra BPM Suite Single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no site da empresa **Questetra BPM Suite** como administrador.

2. No menu em cima, clique em **Definições de Sistema**. 
   
    ![Azure AD Single Sign-On][10]

3. Para abrir a página **SingleSignOnSAML,** clique em **SSO (SAML)**. 
   
    ![Azure AD Single Sign-On][11]

4. No seu site da empresa **Questetra BPM Suite,** na secção **de Informação SP,** execute os seguintes passos:

    a. Copie o **URL ACS**e, em seguida, cole-o na caixa de texto **Sign On URL** na secção de **Configuração Básica SAML** do portal Azure.
    
    b. Copie o **ID da Entidade**e, em seguida, cole-o na caixa de texto **identifier** na secção **de Configuração BÁSICA SAML** do portal Azure.

5. No seu site da empresa **Questetra BPM Suite,** execute os seguintes passos: 
   
    ![Configurar Sign-On Individuais][15]
   
    a. Selecione **Ative o único sinal de inscrição**.
   
    b. Na caixa de texto **ID da Entidade,** cole o valor do **Identificador AD Azure** que copiou do portal Azure.
    
    c. Na caixa de texto **URL de página de entrada de sessão,** cole o valor do URL de **login** que copiou do portal Azure.
    
    d. Na caixa de texto **URL da página de inscrição,** cole o valor do URL **logout** que copiou do portal Azure.
    
    e. Na caixa de texto do **formato NameID,** escreva `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` .

    f. Abra o certificado codificado **Base-64** no bloco de notas descarregado do portal Azure, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto do **certificado de validação.** 

    exemplo, Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **do nome do utilizador** brittasimon@yourcompanydomain.extension . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon utilize o Azure single sign-on, concedendo acesso à Suite Questetra BPM.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Questetra BPM Suite**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Questetra BPM Suite**.

    ![O link Questetra BPM Suite na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-questetra-bpm-suite-test-user"></a>Criar utilizador de testes Questetra BPM Suite

O objetivo desta secção é criar um utilizador chamado Britta Simon na Questetra BPM Suite.

**Para criar um utilizador chamado Britta Simon em Questetra BPM Suite, execute os seguintes passos:**

1. Inscreva-se no site da empresa Questetra BPM Suite como administrador.

2. Aceda às **definições do sistema > lista de utilizadores > novo utilizador**.
 
3. No diálogo do Novo Utilizador, execute os seguintes passos: 
   
    ![Criar utilizador de teste][300] 
   
    a. Na caixa de texto **'Nome',** tipo de **nome** do utilizador britta.simon@contoso.com .
   
    b. Na caixa de texto **por e-mail,** escreva **e-mail** do britta.simon@contoso.com utilizador.
   
    c. Na caixa de texto **da palavra-passe,** digite uma **palavra-passe** do utilizador.
    
    d. Clique **em Adicionar novo utilizador.**

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Questetra BPM Suite no Painel de Acesso, deverá iniciar sedu automaticamente na Suite Questetra BPM para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png