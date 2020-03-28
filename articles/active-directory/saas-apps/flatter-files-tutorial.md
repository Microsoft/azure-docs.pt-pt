---
title: 'Tutorial: Integração do Diretório Ativo Azure com Ficheiros Flatter [ Arquivos) Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Flatter Files.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f86fe5e3-0e91-40d6-869c-3df6912d27ea
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 13012474e34af80c84b034703a3b34f0208036bc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73156264"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Tutorial: Integração de Diretório Ativo Azure com Ficheiros Flatter

Neste tutorial, aprende-se a integrar ficheiros Flatter com o Azure Ative Directory (Azure AD).
Integrar ficheiros Flatter com AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso a Ficheiros Flatter.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos em Ficheiros Flatter (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com ficheiros Flatter, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de assinatura ativada por registo único de Ficheiros Flatter

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Ficheiros Flatter suporta **IDP** iniciado SSO

## <a name="adding-flatter-files-from-the-gallery"></a>Adicionar ficheiros mais lisonjeiros da galeria

Para configurar a integração de Ficheiros Flatter em AD Azure, precisa adicionar Ficheiros Flatter da galeria à sua lista de aplicações SaaS geridas.

**Para adicionar Ficheiros Flatter da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Ficheiros Flatter,** selecione **Ficheiros Flatter** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Ficheiros Mais lisonjeiros na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com Ficheiros Flatter baseados num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Ficheiros Flatter.

Para configurar e testar o único sinal de Azure AD com ficheiros Flatter, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure ficheiros flatter single sign-on](#configure-flatter-files-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Flatter Files test user](#create-flatter-files-test-user)** - para ter uma contrapartida de Britta Simon em Ficheiros Flatter que está ligado à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure com ficheiros Flatter, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Flatter Files,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure.

    ![Flatter Files Domain e URLs informações únicas de inscrição](common/preintegrated.png)

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **Configurar Ficheiros Flatter,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-flatter-files-single-sign-on"></a>Configure ficheiros flatter único sign-on

1. Inscreva-se no seu pedido de Ficheiros Flatter como administrador.

2. Clique no **DASHBOARD**. 
   
    ![Configurar um único sinal](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

3. Clique em **Definições**e, em seguida, execute os seguintes passos no separador **Empresa:** 
   
    ![Configurar um único sinal](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. Selecione **Use O SAML 2.0 para autenticação**.
    
    b. Clique **em Configurar SAML**.

4. No diálogo **de configuração SAML,** execute os seguintes passos: 
   
    ![Configurar um único sinal](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. Na caixa de texto **domínio,** digite o seu domínio registado.
   
   > [!NOTE]
   > Se ainda não tiver um domínio registado, contacte [support@flatterfiles.com](mailto:support@flatterfiles.com)a sua equipa de suporte do Flatter Files via . 
    
    b. Na caixa de texto URL do Fornecedor de **Identidade,** colá o valor do URL de **Login** que copiou o portal Azure.
   
    c.  Abra o seu certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo na sua área de receita e, em seguida, cole-o na caixa de texto do Certificado de Fornecedor de **Identidade.**

    d. Clique em **Atualizar**.

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso a Ficheiros Flatter.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Ficheiros Flatter**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Ficheiros Flatter**.

    ![O link Ficheiros Flatter na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-flatter-files-test-user"></a>Criar o utilizador de teste de Ficheiros Mais Lisonjeiros

O objetivo desta secção é criar uma utilizadora chamada Britta Simon em Ficheiros Flatter.

**Para criar um utilizador chamado Britta Simon em Ficheiros Flatter, execute os seguintes passos:**

1. Inscreva-se no site da empresa **Flatter Files** como administrador.

2. No painel de navegação à esquerda, clique em **Definições**e, em seguida, clique no separador **Utilizadores.**
   
    ![Criar um utilizador de ficheiros mais lisonjeiro](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Clique em **Adicionar Utilizador**. 

4. No diálogo **Adicionar utilizador,** execute os seguintes passos:
   
    ![Criar um utilizador de ficheiros mais lisonjeiro](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. Na caixa de texto **De Primeiro Nome,** digite **Britta**.
   
    b. Na caixa de texto **De Apelido,** escreva **Simon**. 
   
    c. Na caixa de texto endereço de **e-mail,** digite o endereço de e-mail da Britta no portal Azure.
   
    d. Clique em **Submeter**.   


### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Flatter Files no Painel de Acesso, deve ser automaticamente inscrito nos Ficheiros Flatter para os quais configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

