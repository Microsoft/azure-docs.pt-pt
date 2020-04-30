---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Fluxx Labs [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Fluxx Labs.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: e624520a9d1f39bc8115ac72e9df0398065928f1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67102380"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Tutorial: Integração de Diretório Sonárte Azure com a Fluxx Labs

Neste tutorial, aprende-se a integrar o Fluxx Labs com o Azure Ative Directory (Azure AD).
Integrar o Fluxx Labs com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso aos Laboratórios Fluxx.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos nos Laboratórios Fluxx (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Fluxx Labs, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Fluxx Labs única subscrição ativada por sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Fluxx Labs suporta **IDP** iniciado SSO

## <a name="adding-fluxx-labs-from-the-gallery"></a>Adicionar Fluxx Labs da galeria

Para configurar a integração dos Fluxx Labs em Azure AD, você precisa adicionar Fluxx Labs da galeria à sua lista de aplicações geridas saaS.

**Para adicionar Fluxx Labs da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Fluxx Labs,** selecione **Fluxx Labs** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Fluxx Labs na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com a Fluxx Labs com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado nos Laboratórios Fluxx.

Para configurar e testar o único sign-on azure ad com o Fluxx Labs, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure O Sign-On Único dos Laboratórios Fluxx](#configure-fluxx-labs-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador de teste Fluxx Labs](#create-fluxx-labs-test-user)** - para ter uma contrapartida de Britta Simon em Fluxx Labs que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com o Fluxx Labs, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **fluxx Labs,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na configuração de um único sign-on com a página **SAML,** execute os seguintes passos:

    ![Fluxox Labs Domínio e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:

    | Ambiente | Padrão URL|
    |-------------|------------|
    | Produção | `https://<subdomain>.fluxx.io` |
    | Pré-produção | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    | Ambiente | Padrão URL|
    |-------------|------------|
    | Produção | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Pré-produção | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte a equipa de suporte ao cliente da [Fluxx Labs](mailto:travis@fluxxlabs.com) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **set Up Fluxx Labs,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-fluxx-labs-single-sign-on"></a>Configure Fluxx Labs Single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Fluxx Labs como administrador.

2. Selecione **Administrador** abaixo da secção **Definições.**

    ![Configuração de Laboratórios Fluxx](./media/fluxxlabs-tutorial/config1.png)

3. No Painel de Administração, selecione**integrações** de **plug-ins** > e, em seguida, selecione **SAML SSO-(Deficiente)**

    ![Configuração de Laboratórios Fluxx](./media/fluxxlabs-tutorial/config2.png)

4. Na secção de atributos, execute os seguintes passos:

    ![Configuração de Laboratórios Fluxx](./media/fluxxlabs-tutorial/config3.png)

    a. Selecione a caixa de verificação **SAML SSO.**

    b. Na caixa de texto **Rota de Pedido,** tipo **/auth/saml**.

    c. Na caixa de texto **Callback Path,** tipo **/auth/saml/callback**.

    d. Na caixa de texto **"Single Sign-On URL",** introduza o valor URL de **resposta,** que inseriu no portal Azure.

    e. Na caixa de texto **SP Entity ID,** introduza o valor **identificador,** que introduziu no portal Azure.

    f. Na caixa de texto URL target do fornecedor de **identidade SSO** Target, colá o valor URL de **Login,** que copiou do portal Azure.

    g. Abra o seu certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo na sua área de receita e, em seguida, cole-o na caixa de texto do Certificado de Fornecedor de **Identidade.**

    h. Em **Nome identificador Formato** de `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`texto, introduza o valor .

    i. Clique em **Guardar**.

    > [!NOTE]
    > Uma vez guardado o conteúdo, o campo aparecerá em branco para a segurança, mas o valor foi guardado na configuração.

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso aos Laboratórios Fluxx.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Fluxx Labs**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Fluxx Labs**.

    ![A ligação Fluxx Labs na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-fluxx-labs-test-user"></a>Criar o utilizador de teste fluxx Labs

Para permitir que os utilizadores de Anúncios Azure inscrevam-se nos Laboratórios Fluxx, devem ser aprovisionados nos Laboratórios Fluxx. No caso da Fluxx Labs, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa Fluxx Labs como administrador.

2. Clique no **ícone**abaixo apresentado .

    ![Configuração de Laboratórios Fluxx](./media/fluxxlabs-tutorial/config6.png)

3. No painel de instrumentos, clique no ícone abaixo exibido para abrir o cartão **New PEOPLE.**

    ![Configuração de Laboratórios Fluxx](./media/fluxxlabs-tutorial/config4.png)

4. Na secção **NEW PEOPLE,** execute os seguintes passos:

    ![Configuração de Laboratórios Fluxx](./media/fluxxlabs-tutorial/config5.png)

    a. Os Laboratórios Fluxx usam o e-mail como o identificador único para logins SSO. Povoe o campo **SSO UID** com o endereço de e-mail do utilizador, que corresponda ao endereço de e-mail, que estão a usar como login com SSO.

    b. Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Fluxx Labs no Painel de Acesso, deve ser automaticamente inscrito nos Laboratórios Fluxx para os quais configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

