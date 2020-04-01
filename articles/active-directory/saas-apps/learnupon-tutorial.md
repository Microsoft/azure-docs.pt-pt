---
title: 'Tutorial: Integração de Diretório Sonérório Ativo Azure com a LearnUpon [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o LearnUpon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecdcd8c6024b3cacb422b556718bbbdbb5d601c2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67098247"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Tutorial: Integração de Diretório Ativo Azure com a LearnUpon

Neste tutorial, aprende-se a integrar o LearnUpon com o Azure Ative Directory (Azure AD).
Integrar o LearnUpon com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a LearnUpon.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no LearnUpon (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a LearnUpon, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* LearnUpon única subscrição ativada por sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.


* LearnUpon suporta **IDP** iniciado SSO

* LearnUpon suporta o fornecimento de utilizadores **justo no tempo**


## <a name="adding-learnupon-from-the-gallery"></a>Adicionar LearnUpon da galeria

Para configurar a integração do LearnUpon em Azure AD, precisa de adicionar LearnUpon da galeria à sua lista de aplicações saaS geridas.

**Para adicionar LearnUpon a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **LearnUpon**, selecione **LearnUpon** a partir do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

    ![LearnUpon na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o single sign-on azure com o LearnUpon com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no LearnUpon.

Para configurar e testar o único sinal de Azure AD com o LearnUpon, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure LearnUpon Single Sign-On](#configure-learnupon-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create LearnUpon utilizador](#create-learnupon-test-user)** de teste - para ter uma contrapartida de Britta Simon em LearnUpon que esteja ligada à representação azure AD do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com learnUpon, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **LearnUpon,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![LearnUpon Domain e URLs informações únicas de inscrição](common/idp-reply.png)

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Resposta real. Contacte a equipa de suporte do [Cliente LearnUpon](https://www.learnupon.com/features/support/) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sinal com a página **SAML,** localize a **impressão DIGITAL** - Isto será adicionado às definições LearnUpon SAML.

    ![O link de descarregamento do Certificado](common/certificateraw.png)

6. Na secção **Configurar LearnUpon,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-learnupon-single-sign-on"></a>Configure Aprender Após Um Único Sign-On

1. Abra outra instância do navegador e inscreva-se no LearnUpon com uma conta de administrador.

1. Clique no separador **de definições.**

    ![Configurar um único sinal](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. Clique **em Sinal Único - SAML**, e, em seguida, clique em **Definições Gerais** para configurar as definições do SAML.
   
    ![Configurar um único sinal](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. Na secção **Definições Gerais,** execute os seguintes passos:
   
    ![Configurar um único sinal](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Selecione **Ativado**.

    b. Selecione **versão** como **2.0**.

    c. Selecione **condições de skip** como **Nº**.

    d. Na caixa de **texto saml token post,** digite o nome do parâmetro do post de pedido para o URL do consumidor SAML indicado acima que contém a Afirmação SAML a ser verificada e autenticada - por **exemplo, SAMLResponse**.

    e. Na caixa de texto **formato identificador** de nome, digite o valor que indica onde na `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`sua Afirmação SAML o identificador de utilizadores (endereço de e-mail) reside - por exemplo .
  
    f. Na caixa de texto **Identify Provider Location,** escreva o valor que indica para onde os utilizadores são enviados se clicarem no seu ícone carregado a partir do seu ecrã de login do portal Azure.
  
    g. Na caixa de texto **URL Sign out,** colá o valor URL de **Logout,** que copiou do portal Azure.

    h. Clique em **Gerir impressões digitais**dos dedos e, em seguida, faça upload da impressão digital do seu certificado descarregado.

1. Clique em **Definições do Utilizador**e, em seguida, execute os seguintes passos:

     ![Configurar um único sinal](./media/learnupon-tutorial/tutorial_learnupon_11.png)  

    a. Na caixa de texto formato de **identificação de primeiro nome,** digite o valor que nos `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`diz onde reside o primeiro nome dos utilizadores - por exemplo: .
  
    b. Na caixa de texto de formato de identificação de **apelido,** digite o valor que nos `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`diz onde reside no seu SAML O apelido dos utilizadores - por exemplo: .

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **User name** tipo `brittasimon@yourcompanydomain.extension`de campo do nome do utilizador . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao LearnUpon.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **LearnUpon**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **LearnUpon**.

    ![O link LearnUpon na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-learnupon-test-user"></a>Criar utilizador de teste LearnUpon

Nesta secção, um utilizador chamado Britta Simon é criado no LearnUpon. O LearnUpon suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no LearnUpon, um novo é criado após a autenticação. Se precisar de criar um utilizador manualmente, tem de contactar a equipa de [suporte LearnUpon](https://www.learnupon.com/features/support/).

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo LearnUpon no Painel de Acesso, deve ser automaticamente inscrito no LearnUpon para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)