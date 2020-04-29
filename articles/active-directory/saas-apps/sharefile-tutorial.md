---
title: 'Tutorial: Integração de Diretório Ativo Azure com citrix ShareFile [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Citrix ShareFile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71d2fe97a4af07f48038ffd88de648247b56bb80
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73160026"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Tutorial: Integração de Diretório Ativo Azure com citrix ShareFile

Neste tutorial, aprende-se a integrar o Citrix ShareFile com o Azure Ative Directory (Azure AD).
Integrar o Citrix ShareFile com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao Citrix ShareFile.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Citrix ShareFile (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Citrix ShareFile, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Citrix ShareFile única subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Citrix ShareFile suporta **SP** iniciado SSO

## <a name="adding-citrix-sharefile-from-the-gallery"></a>Adicionar Citrix ShareFile da galeria

Para configurar a integração do Citrix ShareFile em Azure AD, precisa adicionar citrix ShareFile da galeria à sua lista de aplicações geridas do SaaS.

**Para adicionar Citrix ShareFile na galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Citrix ShareFile**, selecione **Citrix ShareFile** do painel de resultados e, em seguida, clique em adicionar o botão **Adicionar** a aplicação.

     ![Citrix ShareFile na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on do Azure AD com o Citrix ShareFile com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Citrix ShareFile.

Para configurar e testar o único sign-on azure ad com o Citrix ShareFile, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure citrix ShareFile single sign-On](#configure-citrix-sharefile-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador de teste Citrix ShareFile](#create-citrix-sharefile-test-user)** - para ter uma contrapartida da Britta Simon no Citrix ShareFile que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com citrix ShareFile, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **citrix ShareFile,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Citrix ShareFile Domain e URLs informações únicas de inscrição](common/sp-identifier-reply.png)

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<tenant-name>.sharefile.com/saml/login`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:

    | |
    |---|
    | `https://<tenant-name>.sharefile.com`|
    | `https://<tenant-name>.sharefile.com/saml/info`|
    | `https://<tenant-name>.sharefile1.com/saml/info`|
    | `https://<tenant-name>.sharefile1.eu/saml/info`|
    | `https://<tenant-name>.sharefile.eu/saml/info`|
    | |
    
    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:
    
    | |
    |---|
    | `https://<tenant-name>.sharefile.com/saml/acs`|
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>`|
    | `https://<tenant-name>.sharefile.com/saml/<URL path>`|

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL, Identificador e Resposta real. Contacte a equipa de [suporte ao Cliente Citrix ShareFile](https://www.citrix.co.in/products/citrix-content-collaboration/support.html) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

4. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **Configurar citrix ShareFile,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-citrix-sharefile-single-sign-on"></a>Configure Citrix ShareFile Single Sign-On

1. Numa janela diferente do navegador web, inicie sessão no site da empresa **Citrix ShareFile** como administrador.

2. Na barra de ferramentas por cima, clique **em Admin**.

3. No painel de navegação à esquerda, **selecione Configurar um único sinal**.
   
    ![Administração de Contas](./media/sharefile-tutorial/ic773627.png "Administração de Contas")

4. Na página de diálogo de **configuração De configuração De Início Único/ SAML 2.0,** execute os seguintes passos: **Basic Settings**
   
    ![Início de sessão único](./media/sharefile-tutorial/ic773628.png "Início de sessão único")
   
    a. Clique em **ativar saml**.
    
    b. Na caixa de texto **ID/Entity ID,** colhe o valor do **Identificador Azure Ad** que copiou do portal Azure.

    c. Clique em **Alterar** ao lado do campo **de certificadoS X.509** e, em seguida, faça upload do certificado que descarregou do portal Azure.
    
    d. Na caixa de texto **LOGIN URL,** colá o valor do URL de **Login** que copiou do portal Azure.
    
    e. Na caixa de texto **logout URL,** colá o valor do URL de **Logout** que copiou do portal Azure.

5. Clique em **Guardar** no portal de gestão citrix ShareFile.

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

Nesta secção, permite que a Britta Simon utilize um único sign-on Azure, concedendo acesso ao Citrix ShareFile.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Citrix ShareFile**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, digite e selecione **Citrix ShareFile**.

    ![O link Citrix ShareFile na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-citrix-sharefile-test-user"></a>Criar o utilizador de teste Citrix ShareFile

Para permitir que os utilizadores da AD Azure entrem no Citrix ShareFile, devem ser aprovisionados no Citrix ShareFile. No caso do Citrix ShareFile, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Faça login no seu inquilino **Citrix ShareFile.**

2. Clique em **gerir os utilizadores gerir os \> utilizadores em casa \> + criar o Empregado**.
   
    ![Criar Colaborador](./media/sharefile-tutorial/IC781050.png "Criar Colaborador")

3. Na secção **Informação Básica,** execute abaixo os passos:
   
    ![Informação Básica](./media/sharefile-tutorial/IC799951.png "Informação Básica")
   
    a. Na caixa de texto endereço de **e-mail,** digite o endereço de e-mail de Britta Simon como **\@brittasimon contoso.com**.
   
    b. Na caixa de texto **First Name,** digite **o primeiro nome** do utilizador como **Britta**.
   
    c. Na caixa de texto **Sobre Nome,** digite **o apelido** do utilizador como **Simon**.

4. Clique em **Adicionar Utilizador**.
  
    >[!NOTE]
    >O titular da conta Azure AD receberá um e-mail e seguirá um link para confirmar a sua conta antes de se tornar ativo. Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador Citrix ShareFile ou APIs fornecidas pela Citrix ShareFile para fornecer contas de utilizador da Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Citrix ShareFile no Painel de Acesso, deve ser automaticamente inscrito no Citrix ShareFile para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

