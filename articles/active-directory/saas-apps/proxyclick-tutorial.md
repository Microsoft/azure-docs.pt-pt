---
title: 'Tutorial: Integração do Azure Ative Directory com Proxyclick Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar um único sign-on entre Azure Ative Directory e Proxyclick.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: b3974c5e2a46ede6ef3f92d74759f58ed55b8497
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88553451"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Tutorial: Integração do Azure Ative Directory com Proxyclick

Neste tutorial, você vai aprender a integrar Proxyclick com Azure Ative Directory (Azure AD).
Esta integração proporciona estes benefícios:

* Você pode usar Azure AD para controlar quem tem acesso a Proxyclick.
* Pode permitir que os seus utilizadores se inscrevam automaticamente no Proxyclick (único súmido) com as suas contas AD Azure.
* Pode gerir as suas contas num local central: o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte [o Single sign-on para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com Proxyclick, você precisa ter:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode [inscrever-se](https://azure.microsoft.com/pricing/free-trial/)para um julgamento de um mês.
* Uma subscrição Proxyclick que tem um único sinal de acesso ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar Azure AD um único sinal de acesso em um ambiente de teste.

* Proxyclick suporta SSO iniciado pelo SP e iniciado pelo IdP.

## <a name="add-proxyclick-from-the-gallery"></a>Adicione Proxyclick da galeria

Para configurar a integração do Proxyclick no Azure AD, precisa adicionar Proxyclick da galeria à sua lista de aplicações geridas pelo SaaS.

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Ir para **aplicações da Empresa**  >  **Todas as aplicações**:

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **Nova aplicação** na parte superior da janela:

    ![Selecione nova aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **insira Proxyclick**. Selecione **Proxyclick** nos resultados da pesquisa e, em seguida, **selecione Adicionar**.

     ![Resultados da pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, irá configurar e testar o Azure AD com o Proxyclick utilizando um utilizador de teste chamado Britta Simon.
Para ativar um único sinal, é necessário estabelecer uma relação entre um utilizador Azure AD e o utilizador correspondente no Proxyclick.

Para configurar e testar o Azure AD com um único sinal de sinalização com Proxyclick, é necessário completar estes passos:

1. **[Configurar o Azure AD um único sinal para](#configure-azure-ad-single-sign-on)** ativar a funcionalidade para os seus utilizadores.
2. **[Configurar o sinal único de Proxyclick no](#configure-proxyclick-single-sign-on)** lado da aplicação.
3. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com um único sinal de sessão.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para ativar um único sinal de Azure AD para o utilizador.
5. Crie um utilizador de **[teste Proxyclick](#create-a-proxyclick-test-user)** que esteja ligado à representação AD AZure do utilizador.
6. **[Teste um único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativará a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com Proxyclick, tome estes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação Proxyclick, selecione **Single sign-on**:

    ![Selecione um único sinal de s-on](common/select-sso.png)

2. Na caixa de diálogo **de método de inscrição única,** selecione o modo **SAML/WS-Fed** para permitir um único sinal de entrada:

    ![Selecione um único método de inscrição](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** selecione o ícone **Editar** para abrir a caixa de diálogo **de configuração DE SAML básica:**

    ![Ícone editar](common/edit-urls.png)

4. Na caixa de diálogo **de configuração de configuração básica SAML,** se pretender configurar a aplicação no modo iniciado pelo IdP, tome os seguintes passos.

    ![Caixa básica de diálogo de configuração SAML](common/idp-intiated.png)

    1. Na caixa **identifier,** introduza um URL neste padrão:
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. Na caixa **URL de resposta,** introduza um URL neste padrão:

       `https://saml.proxyclick.com/consume/<companyId>`

5. Se pretender configurar a aplicação no modo iniciado pelo SP, selecione **Definir URLs adicionais**. Na **caixa de URL** sign in, introduza um URL neste padrão:
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![Proxyclick Domain e URLs informações únicas de súmis](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > Estes valores são espaços reservados. Você precisa usar o identificador real, URL de resposta e URL de inscrição. Os passos para obter estes valores são descritos mais tarde neste tutorial.

6. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** selecione o link **de descarregamento** ao lado **do Certificado (Base64)**, de acordo com os seus requisitos, e guarde o certificado no seu computador:

    ![Link de descarregamento de certificado](common/certificatebase64.png)

7. Na secção **Configurar Proxyclick,** copie os URLs apropriados, com base nos seus requisitos:

    ![Copiar os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de login**.

    1. **Identificador Azure Ad.**

    1. **URL logout**.

### <a name="configure-proxyclick-single-sign-on"></a>Configurar Proxyclick single sign-on

1. Numa nova janela do navegador web, inscreva-se no site da empresa Proxyclick como administrador.

2. Selecione **definições de & conta:**

    ![Selecione definições de & de conta](./media/proxyclick-tutorial/configure1.png)

3. Desloque-se até à secção **integrações** e selecione **SAML:**

    ![Selecione SAML](./media/proxyclick-tutorial/configure2.png)

4. Na secção **SAML,** tome os seguintes passos.

    ![Seção SAML](./media/proxyclick-tutorial/configure3.png)

    1. Copie o valor **URL do consumidor SAML** e cole-o na caixa **URL de resposta** na caixa de diálogo **de configuração SAML básica** no portal Azure.

    1. Copie o valor **DE URL de redirecionamento SAML SSO** e cole-o nas caixas **de sinalização em URL** e **identifier** na caixa de diálogo **de configuração SAML básica** no portal Azure.

    1. Na lista do **Método de Pedido SAML,** selecione **HTTP Redirect**.

    1. Na caixa **emitente,** cole o valor do **identificador Azure AD** que copiou do portal Azure.

    1. Na caixa **URL DE ponto final SAML 2.0,** cole o valor URL de **login** que copiou do portal Azure.

    1. No Bloco de Notas, abra o ficheiro de certificado que descarregou a partir do portal Azure. Cole o conteúdo deste ficheiro na caixa **de certificados.**

    1. Selecione **Guardar alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste chamado Britta Simon no portal Azure.

1. No portal Azure, selecione **O Diretório Ativo Azure** no painel esquerdo, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**:

    ![Selecionar Todos os utilizadores](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã:

    ![Selecione Novo utilizador](common/new-user.png)

3. Na caixa de diálogo **do utilizador,** tome os seguintes passos.

    ![Caixa de diálogo do utilizador](common/user-properties.png)

    1. Na caixa **Nome,** **insira BrittaSimon**.
  
    1. Na caixa **do nome do utilizador,** **introduza BrittaSimon@ . \<yourcompanydomain> . \<extension> ** (Por exemplo, BrittaSimon@contoso.com .)

    1. Selecione **Mostrar Palavra-Passe**e, em seguida, anotar o valor que está na caixa **de palavra-passe.**

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que Britta Simon use a Azure single sign-on, concedendo-lhe acesso a Proxyclick.

1. No portal Azure, selecione **aplicações Enterprise**, selecione **Todas as aplicações**e, em seguida, selecione **Proxyclick**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Proxyclick**.

    ![Lista de candidaturas](common/all-applications.png)

3. No painel esquerdo, selecione **Utilizadores e grupos:**

    ![Selecionar Utilizadores e grupos](common/users-groups-blade.png)

4. **Selecione Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Adicionar Atribuição'.**

    ![Selecione Adicionar utilizador](common/add-assign-user.png)

5. Na caixa de diálogo **de Utilizadores e grupos,** selecione **Britta Simon** na lista de utilizadores e, em seguida, clique no botão **Select** na parte inferior da janela.

6. Se espera um valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Clique no botão **Selecione** na parte inferior da janela.

7. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

### <a name="create-a-proxyclick-test-user"></a>Criar um utilizador de teste Proxyclick

Para permitir que os utilizadores de Azure AD entrem no Proxyclick, é necessário adicioná-los ao Proxyclick. Precisa adicioná-las manualmente.

Para criar uma conta de utilizador, tome estes passos:

1. Inscreva-se no site da sua empresa Proxyclick como administrador.

1. Selecione **Colegas** na parte superior da janela:

    ![Selecione Colegas](./media/proxyclick-tutorial/user1.png)

1. Selecione **Adicionar Colega:**

    ![Selecione Adicionar Colega](./media/proxyclick-tutorial/user2.png)

1. Na secção **Adicionar um colega,** tome os seguintes passos.

    ![Adicione uma secção de colegas](./media/proxyclick-tutorial/user3.png)

    1. Na caixa **de e-mail,** insira o endereço de e-mail do utilizador. Neste caso, **brittasimon \@ contoso.com**.

    1. Na caixa **First Name,** insira o primeiro nome do utilizador. Neste caso, **Britta.**

    1. Na caixa **Último Nome,** insira o último nome do utilizador. Neste caso, **Simon.**

    1. Selecione **Adicionar Utilizador**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Agora tem de testar a sua configuração de inscrição única AD Azure utilizando o Painel de Acesso.

Quando selecionar o azulejo Proxyclick no Painel de Acesso, deverá ser automaticamente inscrito na instância Proxyclick para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [o Access e utilize aplicações no portal My Apps.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

