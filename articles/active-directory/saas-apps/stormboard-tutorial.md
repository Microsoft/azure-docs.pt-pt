---
title: 'Tutorial: Integração do Diretório Ativo Azure com stormboard [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Stormboard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 46780ebd-bbfe-4ddc-bf50-22052321026c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d26bf8e7a00944eae2d2252ad2dfcb22796ffb4a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159956"
---
# <a name="tutorial-azure-active-directory-integration-with-stormboard"></a>Tutorial: Integração do Diretório Ativo Azure com stormboard

Neste tutorial, aprende-se a integrar o Stormboard com o Azure Ative Directory (Azure AD).
Integrar o Stormboard com o Azure AD proporciona-lhe os seguintes benefícios:

* Podes controlar em Azure AD quem tem acesso a Stormboard.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Stormboard (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Stormboard, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de sinal único de stormboard ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Stormboard apoia **SP e IDP** iniciadoS SSO

* Stormboard suporta o fornecimento de utilizadores **justo no tempo**


## <a name="adding-stormboard-from-the-gallery"></a>Adicionando Stormboard da galeria

Para configurar a integração do Stormboard em Azure AD, você precisa adicionar Stormboard da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Stormboard da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Stormboard**, selecione **Stormboard** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Prancha de tempestade na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sinal de Azure AD com Stormboard com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Stormboard.

Para configurar e testar o único sinal de Azure AD com stormboard, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sinal Único](#configure-stormboard-single-sign-on)** do Stormboard - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie um utilizador](#create-stormboard-test-user)** de teste stormboard - para ter uma contrapartida de Britta Simon em Stormboard que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sinal de Azure AD com stormboard, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **stormboard,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** Se desejar configurar a aplicação no modo iniciado **idp,** execute o seguinte passo:

    ![Domínio de prancha de tempestade e URLs informações únicas de inscrição](common/both-replyurl.png)

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.stormboard.com/saml2/ad/acs/<TEAMID>`

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    ![Domínio de prancha de tempestade e URLs informações únicas de inscrição](common/both-signonurl.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.stormboard.com/saml2/ad/login/<TEAMID>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Resposta real e URL de Sinal. Contacte a equipa de apoio ao [Cliente Stormboard](mailto:support@stormboard.com) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

6. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

7. Na secção De Configuração de **Stormboard,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-stormboard-single-sign-on"></a>Configure Stormboard Single Sign-On

Para configurar um único sinal no lado **stormboard,** você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de [suporte stormboard](mailto:support@stormboard.com). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome,** entre **brittaSimon.**
  
    b. No campo **de nome do Utilizador,** **escreva brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sinal de Azure, concedendo acesso ao Stormboard.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Stormboard**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Stormboard**.

    ![O link Stormboard na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-stormboard-test-user"></a>Criar o utilizador de teste stormboard

Nesta secção, um utilizador chamado Britta Simon é criado em Stormboard. O Stormboard suporta o fornecimento de **utilizadores just-in-time**, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Stormboard, um novo é criado após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Stormboard no Painel de Acesso, deve ser automaticamente inscrito no Stormboard para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

