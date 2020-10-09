---
title: 'Tutorial: Integração do Azure Ative Directory com Ficheiros Lisonjeiros Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e os Ficheiros Flatter.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 596a6657018658d2d00abe7f221ffae9a6730a7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826460"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Tutorial: Integração do Azure Ative Directory com Ficheiros Lisos

Neste tutorial, aprende-se a integrar os Ficheiros Lisos com o Azure Ative Directory (Azure AD).
A integração de Ficheiros Lisos com AZure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso a Ficheiros Lisonjeiros.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos em Ficheiros Flatter (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD do Azure com os Ficheiros Lisos, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por Ficheiros Lisos

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Flatter Files suporta SSO iniciado pelo **IDP**

## <a name="adding-flatter-files-from-the-gallery"></a>Adicionando ficheiros flatter da galeria

Para configurar a integração de Ficheiros Lisos em Azure AD, é necessário adicionar Ficheiros Flatter da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Ficheiros Flatter da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva Ficheiros Flatter**, selecione **Ficheiros Lisos** do painel de resultados e, em seguida, clique no botão **Adicionar** o botão Adicionar a aplicação.

     ![Ficheiros lisos na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com ficheiros flatter baseados num utilizador de teste chamado **Britta Simon**.
Para um único s-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Ficheiros Lisos.

Para configurar e testar o Azure AD com ficheiros flatter, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure os ficheiros lisos único sinal -,](#configure-flatter-files-single-sign-on)** para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Flatter Files test user](#create-flatter-files-test-user)** - para ter uma contraparte de Britta Simon em Ficheiros Lisos que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com ficheiros flatter, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Flatter Files,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure.

    ![Informações de súmula de ficheiros e URLs](common/preintegrated.png)

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar Ficheiros Lisos,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-flatter-files-single-sign-on"></a>Configurar ficheiros lisos single Sign-On

1. Iniciar s presenciação na sua aplicação Flatter Files como administrador.

2. Clique **no DASHBOARD**. 
   
    ![Screenshot que mostra "DASHBOARD" selecionado na aplicação "Flatter Files".](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

3. Clique **em Definições**e, em seguida, execute os seguintes passos no separador **Empresa:** 
   
    ![Screenshot que mostra o separador "Empresa" com "Use S A M L 2.0 for Authentication" verificado e o botão "Configure S A M L" selecionado.](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. Selecione **Utilizar SAML 2.0 para autenticação**.
    
    b. Clique **em Configurar SAML**.

4. No diálogo **de configuração SAML,** execute os seguintes passos: 
   
    ![Configurar Sign-On Individuais](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. Na caixa de texto **do Domínio,** digite o seu domínio registado.
   
   > [!NOTE]
   > Se ainda não tiver um domínio registado, contacte a sua equipa de suporte do Flatter Files através [support@flatterfiles.com](mailto:support@flatterfiles.com) de . 
    
    b. Na caixa de texto **URL do Fornecedor de Identidade,** cole o valor do URL de **login** que copiou do portal Azure.
   
    c.  Abra o certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **do Certificado de Fornecedor de Identidade.**

    d. Clique em **Atualizar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador** **brittasimon \@ yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon utilize o Azure single sign-on, concedendo acesso a Ficheiros Lisos.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Ficheiros Lisos**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Ficheiros Lisos.**

    ![O link Ficheiros Lisos na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-flatter-files-test-user"></a>Criar utilizador de teste de Ficheiros Lisos

O objetivo desta secção é criar um utilizador chamado Britta Simon in Flatter Files.

**Para criar um utilizador chamado Britta Simon in Flatter Files, execute os seguintes passos:**

1. Inscreva-se no site da empresa **Flatter Files** como administrador.

2. No painel de navegação à esquerda, clique em **Definições**e, em seguida, clique no **separador Utilizadores.**
   
    ![Screenshot que mostra a página "Definições" com o separador "Utilizadores" selecionado.](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Clique em **Adicionar Utilizador**. 

4. No diálogo **do Utilizador Adicionar,** execute os seguintes passos:
   
    ![Criar um utilizador de ficheiros lisos](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. Na caixa de texto **Name Name,** **escreva Britta.**
   
    b. Na caixa de texto **do último nome,** escreva **Simon.** 
   
    c. Na caixa de texto **do Endereço de E-mail,** escreva o endereço de e-mail da Britta no portal Azure.
   
    d. Clique **em Submeter.**   


### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo dos Ficheiros Lisos no Painel de Acesso, deverá ser automaticamente inscrito nos Ficheiros Lisos para os quais configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

