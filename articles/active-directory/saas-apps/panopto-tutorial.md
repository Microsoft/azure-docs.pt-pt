---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Panopto Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Panopto.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 89c88e23-93ce-4970-9baa-1104c4e8fe4a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/17/2019
ms.author: jeedes
ms.openlocfilehash: a1d4836586bac359c3208147ec7bf59061cf0928
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648586"
---
# <a name="tutorial-azure-active-directory-integration-with-panopto"></a>Tutorial: Integração do Diretório Ativo Azure com o Panopto

Neste tutorial, aprende-se a integrar o Panopto com o Azure Ative Directory (Azure AD).
Integrar o Panopto com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Panopto.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Panopto (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Panopto, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de sinal único panopto

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Panopto apoia **SP** iniciado SSO

* Panopto suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-panopto-from-the-gallery"></a>Adicionando Panopto da galeria

Para configurar a integração do Panopto em Azure AD, precisa adicionar Panopto da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Panopto da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Panopto,** selecione **Panopto** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Panopto na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com panopto com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Panopto.

Para configurar e testar o único sinal de Azure AD com o Panopto, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On único do Panopto](#configure-panopto-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador do teste Panopto](#create-panopto-test-user)** - para ter uma contrapartida de Britta Simon em Panopto que esteja ligada à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sinal de Azure AD com o Panopto, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **panopto,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Panopto Domain e URLs informações únicas de inscrição](common/sp-signonurl.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<tenant-name>.panopto.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte a equipa de suporte do [Cliente Panopto](mailto:support@panopto.com) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na secção **'Configurar Panopto',** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-panopto-single-sign-on"></a>Configure panopto único sign-on

1. Numa janela diferente do navegador web, inicie sessão no site da empresa Panopto como administrador.

2. Na barra de ferramentas à esquerda, clique no **Sistema**, e clique em **Fornecedores de Identidade**.
   
    ![Sistema](./media/panopto-tutorial/ic777670.png "Sistema")

3. Clique em **Adicionar Fornecedor**.
   
    ![Fornecedores de Identidade](./media/panopto-tutorial/ic777671.png "Fornecedores de Identidade")
   
4. Na secção de fornecedores SAML, execute os seguintes passos:
   
    ![Configuração SaaS](./media/panopto-tutorial/ic777672.png "Configuração SaaS")
    
    a. Na lista do **Tipo fornecedor,** selecione **SAML20**.    
    
    b. Na caixa de texto **Nome de exemplo,** digite um nome para a instância.

    c. Na caixa de texto **Descrição Amigável,** escreva uma descrição amigável.
    
    d. Na caixa de texto **Bounce Page Url,** colá o valor do URL de **Login,** que copiou do portal Azure.

    e. Na caixa de texto **emitente,** cola o valor do **Identificador AD Azure,** que copiou do portal Azure.

    f. Abra o seu certificado codificado base-64, que descarregou do portal Azure, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o à caixa de texto **da Chave Pública.**

5. Clique em **Guardar**.

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso a Panopto.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Panopto**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Panopto**.

    ![O link Panopto na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-panopto-test-user"></a>Criar o utilizador do teste panopto

Nesta secção, um utilizador chamado Britta Simon é criado em Panopto. O Panopto suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Panopto, um novo é criado após a autenticação.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador panopto ou APIs fornecidas pela Panopto para fornecer contas de utilizador azure AD.
>

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Panopto no Painel de Acesso, deve ser automaticamente inscrito no Panopto para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

