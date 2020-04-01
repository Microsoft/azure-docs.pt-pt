---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Condeco Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Condeco.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4601c17d-ad93-4865-8885-b378c4bbe82b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07e29c3c9c9c5512283a62a50ca69c7736464104
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159350"
---
# <a name="tutorial-azure-active-directory-integration-with-condeco"></a>Tutorial: Integração de Diretório Sonárte Azure com a Condeco

Neste tutorial, aprende-se a integrar o Condeco com o Azure Ative Directory (Azure AD).
Integrar o Condeco com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso a Condeco.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Condeco (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Condeco, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de inscrição única da Condeco

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Condeco apoia **SP** iniciado SSO

* Condeco suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-condeco-from-the-gallery"></a>Adicionando Condeco da galeria

Para configurar a integração da Condeco em Azure AD, você precisa adicionar Condeco da galeria à sua lista de aplicações saaS geridas.

**Para adicionar condeco da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Condeco,** selecione **Condeco** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Condeco na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com condeco com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Condeco.

Para configurar e testar o único sinal de Azure AD com a Condeco, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sinal Único do Condeco](#configure-condeco-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador](#create-condeco-test-user)** de teste Condeco - para ter uma contrapartida da Britta Simon em Condeco que esteja ligada à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com condeco, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **da Condeco,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Conéco Domínio e URLs informações únicas de inscrição](common/sp-signonurl.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<companyname>.condecosoftware.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte a equipa de apoio ao [Cliente Condeco](mailto:supportna@condecosoftware.com) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na secção **Configurar condeco,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-condeco-single-sign-on"></a>Configure O Único Sign-On do Condeco

Para configurar um único sign-on no lado do **Condeco,** você precisa enviar o descarregamento **da Federação Metadados XML** e URLs copiados apropriados do portal Azure para a equipa de [suporte condeco](mailto:supportna@condecosoftware.com). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso à Condeco.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Condeco**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Condeco**.

    ![O link Condeco na lista de candidaturas](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-condeco-test-user"></a>Criar o utilizador de teste Condeco

O objetivo desta secção é criar uma utilizadora chamada Britta Simon em Condeco. A Condeco suporta **o fornecimento just-in-time**, que está por defeito habilitado.

Não há nenhum item de ação para si nesta secção. Um novo utilizador é criado durante uma tentativa de acesso ao Condeco se ainda não existir.

>[!NOTE]
>Se precisar de criar um utilizador manualmente, tem de contactar a equipa de suporte da [Condeco.](mailTo:supportna@condecosoftware.com)

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Condeco no Painel de Acesso, deverá ser automaticamente inscrito no Condeco para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

