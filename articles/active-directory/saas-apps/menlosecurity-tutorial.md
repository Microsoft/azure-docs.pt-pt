---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Menlo Security Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Menlo Security.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9e63fe6b-0ad0-405d-9e41-6a1a40a41df8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.openlocfilehash: 172fe6a6b5b8e7c649adc58e69e71c54a0f8bde9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73160720"
---
# <a name="tutorial-azure-active-directory-integration-with-menlo-security"></a>Tutorial: Integração do Diretório Ativo Azure com a Menlo Security

Neste tutorial, aprende-se a integrar a Menlo Security com o Azure Ative Directory (Azure AD).
Integrar a Menlo Security com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso à Menlo Security.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Menlo Security (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Menlo Security, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Menlo Security assinatura única ativada por sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Menlo Security apoia **SP** iniciado SSO

## <a name="adding-menlo-security-from-the-gallery"></a>Adicionando Menlo Security da galeria

Para configurar a integração da Menlo Security no Azure AD, você precisa adicionar Menlo Security da galeria à sua lista de aplicações geridas saaS.

**Para adicionar menlo Security da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Menlo Security**, selecione **Menlo Security** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Menlo Security na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sinal de Azure AD com a Menlo Security com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Menlo Security.

Para configurar e testar o único sinal de Azure AD com a Menlo Security, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Menlo Security Single Sign-On](#configure-menlo-security-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador](#create-menlo-security-test-user)** do teste Menlo Security - para ter uma contrapartida da Britta Simon na Menlo Security que esteja ligada à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure com a Menlo Security, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **menlo Security,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Menlo Security Domain e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<subdomain>.menlosecurity.com/account/login`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<subdomain>.menlosecurity.com/safeview-auth-server/saml/metadata`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de [apoio ao Cliente menlo security](https://www.menlosecurity.com/menlo-contact) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **De Segurança Menlo, copie** os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-menlo-security-single-sign-on"></a>Configure Menlo Security Single Sign-On

1. Para configurar um único login no lado da **Menlo Security,** inicie sessão no site da **Menlo Security** como administrador.

2. Em **Definições,** vá para a **Autenticação** e execute as seguintes ações:
    
    ![Configurar um único sinal](./media/menlosecurity-tutorial/menlo_user_setup.png)

    a. Marque a caixa de verificação **Ativar a autenticação do utilizador utilizando o SAML**.

    b. **Selecione permitir o acesso externo** ao **Sim**.

    c. No âmbito **do Provedor SAML,** selecione **Azure Ative Directory**.

    d. **SAML 2.0 Ponto final** : Colar o URL de **login** que copiou do portal Azure.

    e. **Identificador de serviço (Emitente)** : Colar o **identificador Azure AD** que copiou do portal Azure.

    f. **Certificado X.509** : Abra o **Certificado (Base64)** descarregado do Portal Azure no bloco de notas e colá-lo nesta caixa.

    g. Clique em **Guardar** para guardar as definições.

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso à Menlo Security.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Menlo Security**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Menlo Security**.

    ![O link Menlo Security na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-menlo-security-test-user"></a>Criar o utilizador do teste menlo Security

Nesta secção, cria-se uma utilizadora chamada Britta Simon na Menlo Security. Trabalhe com a equipa de suporte ao [Cliente Menlo Security](https://www.menlosecurity.com/menlo-contact) para adicionar os utilizadores na plataforma Menlo Security. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Menlo Security no Painel de Acesso, deverá ser automaticamente inscrito na Menlo Security para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

