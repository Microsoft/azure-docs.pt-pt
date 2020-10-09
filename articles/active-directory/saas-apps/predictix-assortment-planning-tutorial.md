---
title: 'Tutorial: Integração do Azure Ative Directory com o Planeamento do Sortido Da Predictix Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar um único sign-on entre O Diretório Ativo Azure e o Planeamento de Sortundo Prediix.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 5b7ec1843d279e8c0477ed1c7754746d4f3044ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88553667"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-assortment-planning"></a>Tutorial: Integração do Diretório Ativo Azure com o Planeamento do Sortido De Predictix

Neste tutorial, você aprenderá a integrar o Planeamento de Sortument Predictix com O Diretório Ativo Azure (Azure AD).
Esta integração proporciona estes benefícios:

* Você pode usar Azure AD para controlar quem tem acesso ao Previsix Sortsortment Planning.
* Pode permitir que os seus utilizadores se inscrevam automaticamente no Previsix Sortsortment Planning (único súlter) com as suas contas AD Azure.
* Pode gerir as suas contas num local central: o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte [o Single sign-on para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração Azure AD com o Planeamento do Sortido De Previsãoix, você precisa ter:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/pricing/free-trial/)
* Uma subscrição de Planeamento de Sortidos Predictix que tem um único sinal de acesso ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar Azure AD um único sinal de acesso em um ambiente de teste.

* O Planeamento de Sortudos Da Predictix suporta sso iniciado pelo SP.

## <a name="add-predictix-assortment-planning-from-the-gallery"></a>Adicione o Planeamento do Sortido De Previsão da galeria

Para configurar a integração do Previsix Sortsortment Planning em Azure AD, é necessário adicionar o Planeamento de Sortudos Predictix da galeria à sua lista de aplicações geridas pelo SaaS.

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Ir para **aplicações da Empresa**  >  **Todas as aplicações**:

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **Nova aplicação** na parte superior da janela:

    ![Selecione nova aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **insira o Planeamento do Sortido De Previsão.** Selecione **Predictix Sortsortment Planning** nos resultados da pesquisa e, em seguida, selecione **Adicionar**.

     ![Resultados da pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, irá configurar e testar o Azure AD com o Predictix Sortsortment Planning utilizando um utilizador de teste chamado Britta Simon.
Para ativar um único sinal, é necessário estabelecer uma relação entre um utilizador Azure AD e o utilizador correspondente no Planeamento do Sortido Predictix.

Para configurar e testar o Azure AD com um único sinal de compensação com o Planeamento do Sortido de Previsix, é necessário completar estes passos:

1. **[Configurar o Azure AD um único sinal para](#configure-azure-ad-single-sign-on)** ativar a funcionalidade para os seus utilizadores.
2. **[Configurar o Sortido de Previsão de Variação planejando um único sinal no](#configure-predictix-assortment-planning-single-sign-on)** lado da aplicação.
3. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com um único sinal de sessão.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para ativar um único sinal de Azure AD para o utilizador.
5. Crie um utilizador de **[teste de planeamento de sortidos de Previsão](#create-a-predictix-assortment-planning-test-user)** que esteja ligado à representação AD Azure do utilizador.
6. **[Teste um único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativará a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o Planeamento do Sortido De Previsãoix, tome estes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **do Planeamento do Sortido De Predictix,** selecione **Single sign-on**:

    ![Selecione um único sinal de s-on](common/select-sso.png)

2. Na caixa de diálogo **de método de inscrição única,** selecione o modo **SAML/WS-Fed** para permitir um único sinal de entrada:

    ![Selecione um único método de inscrição](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** selecione o ícone **Editar** para abrir a caixa de diálogo **de configuração DE SAML básica:**

    ![Ícone editar](common/edit-urls.png)

4. Na caixa de diálogo **de configuração DE SAML Básica,** complete os seguintes passos.

    ![Caixa básica de diálogo de configuração SAML](common/sp-identifier.png)

    1. Na **caixa de URL** sign in, introduza um URL neste padrão:

        ```https
        https://<sub-domain>.ap.predictix.com/sso/request
        https://<sub-domain>.dev.ap.predictix.com/
        ```

    1. Na caixa **identifier (Entity ID),** introduza um URL neste padrão:

        ```https
        https://<sub-domain>.ap.predictix.com
        https://<sub-domain>.dev.ap.predictix.com
        ```

    > [!NOTE]
    > Estes valores são espaços reservados. Tens de usar o URL e o identificador de inscrição real. Contacte a [equipa de apoio ao Planeamento do Sortido de Previsão](https://www.infor.com/support) para obter os valores. Também pode consultar os padrões mostrados na caixa de diálogo **de configuração SAML básica** no portal Azure.

5. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** selecione o link **de descarregamento** ao lado **do Certificado (Base64)**, de acordo com os seus requisitos, e guarde o certificado no seu computador:

    ![Link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **De Planeamento de Sortidos De Configuração,** copie os URLs apropriados, com base nos seus requisitos:

    ![Copiar os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de login**.

    1. **Identificador Azure Ad.**

    1. **URL logout**.

### <a name="configure-predictix-assortment-planning-single-sign-on"></a>Configurar Previsão De Sortumento Planeamento único

Para configurar um único sinal no lado do Planeamento do Sortido de Previsão, precisa enviar o certificado que descarregou e os URLs que copiou do portal Azure para a equipa de apoio ao Planeamento de [Sortuments Predictix.](https://www.infor.com/support) Esta equipa garante que a ligação SSO SAML está corretamente definida em ambos os lados.

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

    1. Selecione **Mostrar a palavra-passe**e, em seguida, anotar o valor que está na caixa **de palavra-passe.**

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que Britta Simon use Azure AD um único sinal de acesso, concedendo-lhe acesso ao Planeamento de Sortudos Predictix.

1. No portal Azure, selecione **aplicações Enterprise**, selecione **Todas as aplicações**e, em seguida, selecione **Predictix Sortsortment Planning**.

    ![Aplicações Empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Predictix Sortsortment Planning**.

    ![Lista de candidaturas](common/all-applications.png)

3. No painel esquerdo, selecione **Utilizadores e grupos:**

    ![Selecionar Utilizadores e grupos](common/users-groups-blade.png)

4. **Selecione Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Adicionar Atribuição'.**

    ![Selecione Adicionar utilizador](common/add-assign-user.png)

5. Na caixa de diálogo **de Utilizadores e grupos,** selecione **Britta Simon** na lista de utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se espera um valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Clique no botão **Selecione** na parte inferior do ecrã.

7. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

### <a name="create-a-predictix-assortment-planning-test-user"></a>Criar um utilizador de teste de planeamento de sortido de previsão

Em seguida, você precisa criar um utilizador chamado Britta Simon em Previsix Sortsortment Planning. Trabalhe com a [equipa de apoio ao Planeamento do Sortido de Previsix](https://www.infor.com/support) para adicionar utilizadores. Os utilizadores precisam de ser criados e ativados antes de utilizarem uma única s ativação.

> [!NOTE]
> O titular da conta Azure AD recebe um e-mail e seleciona um link para confirmar a conta antes de ficar ativa.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Agora tem de testar a sua configuração de inscrição única AD Azure utilizando o Painel de Acesso.

Quando selecionar o azulejo de Planeamento do Sortido De Previsão no Painel de Acesso, deverá ser automaticamente inscrito no caso de Planeamento do Sortido de Previsix para o qual configura sSO. Para mais informações, consulte [o Access e utilize aplicações no portal My Apps.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)