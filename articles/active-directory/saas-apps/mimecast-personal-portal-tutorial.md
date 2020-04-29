---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Portal Pessoal mimecast [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Portal Pessoal mimecast.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 259635613855e4d7687cf569c94bbd3dd04027fe
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73160623"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Tutorial: Integração do Diretório Ativo Azure com portal pessoal mimecast

Neste tutorial, aprende-se a integrar o Portal Pessoal mimecast com o Azure Ative Directory (Azure AD).
Integrar o Portal Pessoal mimecast com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Mimecast Personal Portal.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Portal Pessoal mimecast (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Portal Pessoal mimecast, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de assinatura ativada pelo Portal Pessoal Mimecast

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Portal Pessoal Mimecast suporta **SP** iniciado SSO

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Adicionando o Portal Pessoal mimecast da galeria

Para configurar a integração do Portal Pessoal Mimecast em Azure AD, você precisa adicionar O Portal Pessoal Mimecast da galeria à sua lista de aplicações geridas saaS.

**Para adicionar o Portal Pessoal Mimecast da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **o Portal Pessoal mimecast,** selecione **Portal Pessoal Mimecast** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Portal Pessoal Mimecast na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com o Portal Pessoal mimecast com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Portal Pessoal mimecast.

Para configurar e testar o único sign-on azure ad com o Portal Pessoal mimecast, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Portal Único do Portal Mimecast Single Sign-On](#configure-mimecast-personal-portal-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador de teste do Portal Pessoal Mimecast](#create-mimecast-personal-portal-test-user)** - para ter uma contrapartida de Britta Simon no Portal Pessoal mimecast que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com o Portal Pessoal mimecast, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações do **Portal Pessoal Mimecast,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Mimecast Personal Portal Domain e URLs informações únicas de inscrição](common/sp-identifier-reply.png)

    a. Na caixa de texto **salmôl Sign-on URL,** escreva um URL: 

    | Região  |  Valor | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Estados Unidos   | `https://us-api.mimecast.com/login/saml`|
    | África do Sul    | `https://za-api.mimecast.com/login/saml`|
    | Austrália       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

    b. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:

    | Região  |  Valor | 
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Estados Unidos   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | África do Sul    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Austrália       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Offshore        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. Na caixa de texto url de **resposta,** escreva um URL: 

    | Região  |  Valor | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Estados Unidos   | `https://us-api.mimecast.com/login/saml`|
    | África do Sul    | `https://za-api.mimecast.com/login/saml`|
    | Austrália       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

    > [!NOTE]
    > O valor do identificador não é real. Atualize o valor com o identificador real. Contacte a equipa de suporte do [Cliente do Portal Pessoal mimecast](https://www.mimecast.com/customer-success/technical-support/) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

4. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **"Portal Pessoal Mimecast",** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-mimecast-personal-portal-single-sign-on"></a>Configure Mimecast Portal Pessoal Único Sign-On

1. Numa janela diferente do navegador web, inicie sessão no portal pessoal mimecast como administrador.

2. Ir a Aplicações de **Serviços \> **.
   
    ![Aplicações](./media/mimecast-personal-portal-tutorial/ic794998.png "Aplicações")

3. Clique em **Perfis de Autenticação**.
   
    ![Perfis de Autenticação](./media/mimecast-personal-portal-tutorial/ic794999.png "Perfis de Autenticação")

4. Clique em **Novo Perfil de Autenticação**.
   
    ![Novo perfil de autenticação](./media/mimecast-personal-portal-tutorial/ic795000.png "Novo perfil de autenticação")

5. Na secção Perfil de **Autenticação,** execute os seguintes passos:
   
    ![Perfil de autenticação](./media/mimecast-personal-portal-tutorial/ic795001.png "Perfil de autenticação")
   
    a. Na caixa de texto **Descrição,** digite um nome para a sua configuração.
   
    b. Selecione **impor a autenticação SAML para mimecast Portal Pessoal**.
   
    c. Como **Fornecedor,** **selecione Azure Ative Directory**.
   
    d. Na caixa de texto **URL emitentes,** colhe o valor do Identificador De **Ad Azure,** que copiou do portal Azure.
   
    e. Na caixa de texto **LOGIN URL,** colá o valor do URL de **Login,** que copiou do portal Azure.
   
    f. Na caixa de texto **logout URL,** cola o valor do URL de **Logout,** que copiou do portal Azure.

    g. Abra o seu certificado codificado **base-64** no bloco de notas descarregado do portal Azure, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto do Fornecedor de **Identidade (Metadados).**

    h. **Selecione permitir um único sinal .**
   
    i. Clique em **Guardar**.

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao Portal Pessoal Mimecast.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Portal Pessoal Mimecast**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, escreva e selecione **Portal Pessoal Mimecast**.

    ![O link Do Portal Pessoal Mimecast na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-mimecast-personal-portal-test-user"></a>Criar o utilizador de teste do Portal Pessoal mimecast

Para permitir que os utilizadores de Anúncios Azure entrem no Portal Pessoal mimecast, devem ser aprovisionados no Portal Pessoal mimecast. No caso do Portal Pessoal Mimecast, o provisionamento é uma tarefa manual.

Tem de registar um domínio antes de poder criar utilizadores.

**Para configurar o fornecimento do utilizador, execute os seguintes passos:**

1. Inscreva-se no seu **Portal Pessoal Mimecast** como administrador.

2. Ir ao **Diretórios \> Interno.**
   
    ![Diretórios](./media/mimecast-personal-portal-tutorial/ic795003.png "Diretórios")

3. Clique **em registar novo domínio**.
   
    ![Registar Novo Domínio](./media/mimecast-personal-portal-tutorial/ic795004.png "Registar Novo Domínio")

4. Depois de criado o seu novo domínio, clique em **Novo Endereço**.
   
    ![Novo Endereço](./media/mimecast-personal-portal-tutorial/ic795005.png "Novo Endereço")

5. No novo diálogo de endereço, execute os seguintes passos de uma conta Azure AD válida que pretende fornecer:
   
    ![Guardar](./media/mimecast-personal-portal-tutorial/ic795006.png "Guardar")
   
    a. Na caixa de texto endereço de **e-mail,** digite endereço de **e-mail** do utilizador como **BrittaSimon\@contoso.com**.
    
    b. Na caixa de texto **Nome Global,** digite o nome de **utilizador** como **BrittaSimon**.

    c. Na **Palavra-passe**e confirmar caixas de **texto** password, digite a **Palavra-passe** do utilizador.
   
    b. Clique em **Guardar**.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador do Portal Pessoal mimecast ou APIs fornecidas pelo Portal Pessoal mimecast para fornecer contas de utilizador da AD Azure.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Mimecast Personal Portal no Painel de Acesso, deverá ser automaticamente inscrito no Portal Pessoal mimecast para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

