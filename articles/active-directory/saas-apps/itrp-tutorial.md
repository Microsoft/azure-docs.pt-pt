---
title: 'Tutorial: Integração do Azure Ative Directory com a ITRP Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar um único sign-on entre Azure Ative Directory e ITRP.
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
ms.openlocfilehash: 2dff68f98e2922d5fc7a4fca1e6de8740bc2ae68
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92459684"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Tutorial: Integração do Azure Ative Directory com a ITRP

Neste tutorial, você vai aprender a integrar ITRP com Azure Ative Directory (Azure AD).
Esta integração proporciona estes benefícios:

* Pode utilizar a Azure AD para controlar quem tem acesso ao ITRP.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no ITRP (súpido único) com as suas contas AD Azure.
* Pode gerir as suas contas num local central: o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte [o Single sign-on para aplicações no Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o ITRP, você precisa ter:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição ITRP que tem um único sinal de acesso ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar Azure AD um único sinal de acesso em um ambiente de teste.

* A ITRP suporta sSO iniciado pelo SP.

## <a name="add-itrp-from-the-gallery"></a>Adicione ITRP da galeria

Para configurar a integração do ITRP no Azure AD, é necessário adicionar ITRP da galeria à sua lista de aplicações geridas pelo SaaS.

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Ir para **aplicações da Empresa**  >  **Todas as aplicações**:

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **Nova aplicação** na parte superior da janela:

    ![Selecione nova aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **o ITRP**. Selecione **ITRP** nos resultados da pesquisa e, em seguida, **selecione Adicionar**.

     ![Resultados da pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, irá configurar e testar o Azure AD com o ITRP utilizando um utilizador de teste chamado Britta Simon.
Para ativar um único sinal, é necessário estabelecer uma relação entre um utilizador Azure AD e o utilizador correspondente no ITRP.

Para configurar e testar o Azure AD com o ITRP, é necessário completar estes passos:

1. **[Configurar o Azure AD um único sinal para](#configure-azure-ad-single-sign-on)** ativar a funcionalidade para os seus utilizadores.
2. **[Configurar o auto-sinal de ITRP no](#configure-itrp-single-sign-on)** lado da aplicação.
3. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com um único sinal de sessão.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para ativar um único sinal de Azure AD para o utilizador.
5. Crie um utilizador de **[teste ITRP](#create-an-itrp-test-user)** que esteja ligado à representação AD AZure do utilizador.
6. **[Teste um único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativará a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o ITRP, tome estes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações ITRP, selecione **Single sign-on**:

    ![Selecione um único sinal de s-on](common/select-sso.png)

2. Na caixa de diálogo **de método de inscrição única,** selecione o modo **SAML/WS-Fed** para permitir um único sinal de entrada:

    ![Selecione um único método de inscrição](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** selecione o ícone **Editar** para abrir a caixa de diálogo **de configuração DE SAML básica:**

    ![A screenshot mostra o Conjunto Single Sign-On com a página SAML com o ícone de edição selecionado.](common/edit-urls.png)

4. Na caixa de diálogo **de configuração DE SAML Básico,** tome os seguintes passos.

    ![Caixa básica de diálogo de configuração SAML](common/sp-identifier.png)

    1. Na **caixa de URL** sign in, introduza um URL neste padrão:
    
       `https://<tenant-name>.itrp.com`

    1. Na caixa **identifier (Entity ID),** introduza um URL neste padrão:

       `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > Estes valores são espaços reservados. Tens de usar o URL e o identificador de inscrição real. Contacte a equipa de apoio da [ITRP](https://www.4me.com/support/) para obter os valores. Também pode consultar os padrões mostrados na caixa de diálogo **de configuração SAML básica** no portal Azure.

5. Na secção **Certificado de Assinatura SAML,** selecione o ícone **Editar** para abrir a caixa de diálogo do Certificado de **Assinatura SAML:**

    ![A screenshot mostra a página do Certificado de Assinatura SAML com o ícone de edição selecionado.](common/edit-certificate.png)

6. Na caixa de diálogo do Certificado de **Assinatura SAML,** copie o valor **da impressão digital** e guarde-o:

    ![Copiar o valor da impressão digital](common/copy-thumbprint.png)

7. Na secção Configurar o **ITRP,** copie os URLs apropriados, com base nos seus requisitos:

    ![Copiar os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de login**.

    1. **Identificador Azure Ad.**

    1. **URL logout**.

### <a name="configure-itrp-single-sign-on"></a>Configurar o sign-on único do ITRP

1. Numa nova janela do navegador web, inscreva-se no site da empresa ITRP como administrador.

1. Na parte superior da janela, selecione o ícone **Definições:**

    ![Ícone Definições](./media/itrp-tutorial/ic775570.png "Ícone Definições")

1. No painel esquerdo, selecione **Single Sign-On**:

    ![Selecione um único sinal de inscrição](./media/itrp-tutorial/ic775571.png "Selecione Sign-On única")

1. Na secção de configuração **'Sign-On' único,** tome os seguintes passos.

    ![A screenshot mostra a secção single Sign-On com o selecionado Ativado.](./media/itrp-tutorial/ic775572.png "Seção de Sign-On única")

    ![A screenshot mostra a secção single Sign-On onde pode adicionar as informações descritas neste passo.](./media/itrp-tutorial/ic775573.png "Seção de Sign-On única")

    1. Selecione **Ativado**.

    1. Na caixa **URL de logout remoto,** cole o valor **URL logout** que copiou do portal Azure.

    1. Na caixa **DE URL SAML SSO,** cole o valor URL de **login** que copiou do portal Azure.

    1. Na caixa **de impressões digitais do Certificado,** cole o valor de **impressão digital** do certificado, que copiou do portal Azure.

    1. Selecione **Guardar**.

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

Nesta secção, você permitirá que Britta Simon use a Azure single sign-on, concedendo-lhe acesso à ITRP.

1. No portal Azure, selecione **aplicações Enterprise**, selecione **Todas as aplicações**e, em seguida, selecione **ITRP**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **ITRP**.

    ![Lista de candidaturas](common/all-applications.png)

3. No painel esquerdo, selecione **Utilizadores e grupos:**

    ![Selecionar Utilizadores e grupos](common/users-groups-blade.png)

4. **Selecione Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Adicionar Atribuição'.**

    ![Selecione Adicionar utilizador](common/add-assign-user.png)

5. Na caixa de diálogo **de Utilizadores e grupos,** selecione **Britta Simon** na lista de utilizadores e, em seguida, clique no botão **Select** na parte inferior da janela.

6. Se espera um valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Clique no botão **Selecione** na parte inferior da janela.

7. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

### <a name="create-an-itrp-test-user"></a>Criar um utilizador de teste ITRP

Para permitir que os utilizadores de Azure AD entrem no ITRP, é necessário adicioná-los ao ITRP. Precisa adicioná-las manualmente.

Para criar uma conta de utilizador, tome estes passos:

1. Inscreva-se no seu inquilino da ITRP.

1. Na parte superior da janela, selecione o ícone **Registos:**

    ![Ícone de registos](./media/itrp-tutorial/ic775575.png "Ícone de registos")

1. No menu, selecione **Pessoas:**

    ![Selecione Pessoas](./media/itrp-tutorial/ic775587.png "Selecione Pessoas")

1. Selecione o sinal de mais **+** () para adicionar uma nova pessoa:

    ![Selecione o sinal mais](./media/itrp-tutorial/ic775576.png "Selecione o sinal mais")

1. Na caixa de diálogo **Add New Person,** tome os seguintes passos.

    ![Adicionar caixa de diálogo de nova pessoa](./media/itrp-tutorial/ic775577.png "Adicionar caixa de diálogo de nova pessoa")

    1. Insira o nome e o endereço de e-mail de uma conta Azure AD válida que pretende adicionar.

    1. Selecione **Guardar**.

> [!NOTE]
> Pode utilizar qualquer ferramenta de criação de conta de utilizador ou API fornecida pelo ITRP para fornecer contas de utilizador Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Agora tem de testar a sua configuração de inscrição única AD Azure utilizando o Painel de Acesso.

Quando selecionar o azulejo ITRP no Painel de Acesso, deverá ser automaticamente inscrito na instância ITRP para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [o Access e utilize aplicações no portal My Apps.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](./tutorial-list.md) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)