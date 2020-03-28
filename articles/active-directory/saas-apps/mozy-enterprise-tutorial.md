---
title: 'Tutorial: Integração de Diretório Sonérrio Sonárgico Azure com a Mozy Enterprise Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Mozy Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 7d3b7f8982a24856818cbb6ce86060beac969fc2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233502"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Tutorial: Integração de Diretório Ativo Azure com a Mozy Enterprise

Neste tutorial, aprende-se a integrar a Mozy Enterprise com o Azure Ative Directory (Azure AD).
Integrar a Mozy Enterprise com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso à Mozy Enterprise.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Mozy Enterprise (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da Azure AD com a Mozy Enterprise, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de inscrição única da Mozy Enterprise

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Mozy Enterprise apoia **SP** iniciado SSO

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Adicionando Mozy Enterprise da galeria

Para configurar a integração da Mozy Enterprise em Azure AD, você precisa adicionar Mozy Enterprise da galeria à sua lista de aplicações geridas saaS.

**Para adicionar a Mozy Enterprise a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite a Mozy Enterprise,** selecione **Mozy Enterprise** a partir do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Mozy Enterprise na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on da Azure AD com a Mozy Enterprise com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Mozy Enterprise.

Para configurar e testar o único sign-on da Azure AD com a Mozy Enterprise, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Mozy Enterprise Single Sign-On](#configure-mozy-enterprise-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador](#create-mozy-enterprise-test-user)** de teste da Mozy Enterprise - para ter uma contrapartida da Britta Simon na Mozy Enterprise que esteja ligada à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on da Azure AD com a Mozy Enterprise, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **da Mozy Enterprise,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Mozy Enterprise Domain e URLs informações únicas de inscrição](common/sp-signonurl.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte a equipa de [apoio ao Cliente da Mozy Enterprise](https://support.mozy.com/) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção Configurar a **Mozy Enterprise,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-mozy-enterprise-single-sign-on"></a>Configure Mozy Enterprise Single Sign-On

1. Numa janela diferente do navegador web, inicie sessão no site da empresa Mozy Enterprise como administrador.

2. Na secção **Configuração,** clique na **Política de Autenticação**.
   
    ![Política de autenticação](./media/mozy-enterprise-tutorial/ic777314.png "Política de autenticação")

3. Na secção **Política de Autenticação,** execute os seguintes passos:
   
    ![Política de autenticação](./media/mozy-enterprise-tutorial/ic777315.png "Política de autenticação")
   
    a. Selecione **Serviço de Diretório** como **Fornecedor**.
   
    b. Selecione **Utilizar o impulso LDAP**.
   
    c. Clique no **separador de autenticação SAML.**
   
    d. Colhe o URL de **Login,** que copiou do portal Azure para a caixa de texto URL de **Autenticação.**
   
    e. Identificador **AD Paste Azure**, que copiou do portal Azure para a caixa de texto **SAML Endpoint.**
   
    f. Abra o certificado codificado base-64 descarregado no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole todo o Certificado na caixa de **texto do Certificado SAML.**
   
    g. Selecione **Enable SSO para os Administradores iniciarem sessão com as suas credenciais**de rede .
   
    h. Clique em **Guardar Alterações**.

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

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso à Mozy Enterprise.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Mozy Enterprise**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, **selecione Mozy Enterprise**.

    ![O link Mozy Enterprise na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-mozy-enterprise-test-user"></a>Criar o utilizador de teste Mozy Enterprise

Para permitir que os utilizadores da AD Azure entrem na Mozy Enterprise, devem ser aprovisionados na Mozy Enterprise. No caso da Mozy Enterprise, o provisionamento é uma tarefa manual.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador da Mozy Enterprise ou APIs fornecidas pela Mozy Enterprise para fornecer contas de utilizador da Azure AD.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Faça login no seu inquilino **da Mozy Enterprise.**

2. Clique em **Utilizadores**e, em seguida, clique em **Adicionar Novo Utilizador**.
   
    ![Utilizadores](./media/mozy-enterprise-tutorial/ic777317.png "Utilizadores")
   
    >[!NOTE]
    >A opção **Adicionar Novo Utilizador** só é apresentada se a **Mozy** for selecionada como fornecedora no âmbito da política de **autenticação**. Se a autenticação SAML estiver configurada, os utilizadores são adicionados automaticamente no primeiro login através do sinal único.
    
3. No novo diálogo do utilizador, execute os seguintes passos:
   
    ![Add Users](./media/mozy-enterprise-tutorial/ic777318.png "Adicionar Utilizadores")
   
    a. A partir da lista **Escolha um Grupo,** selecione um grupo.
   
    b. A partir do tipo de lista **de utilizadores,** selecione um tipo.
   
    c. Na caixa de texto **username,** digite o nome do utilizador DaD Azure.
   
    d. Na caixa de texto **por e-mail,** digite o endereço de e-mail do utilizador da AD Azure.
   
    e. Selecione **Enviar e-mail**de instrução do utilizador .
   
    f. Clique em **Adicionar Utilizador(s)**.

     >[!NOTE]
     > Após a criação do utilizador, será enviado um e-mail ao utilizador da AD Azure que inclui um link para confirmar a conta antes de se tornar ativa.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Mozy Enterprise no Painel de Acesso, deverá ser automaticamente inscrito na Mozy Enterprise para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

