---
title: 'Tutorial: Integração do Azure Ative Directory com o InsideView Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar um único sign-on entre Azure Ative Directory e InsideView.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: 17c9672663cae4df7eacb72779e6caf6476e3d24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88550491"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Tutorial: Integração do Azure Ative Directory com o InsideView

Neste tutorial, você vai aprender a integrar o InsideView com o Azure Ative Directory (Azure AD).
Esta integração proporciona estes benefícios:

* Pode utilizar a Azure AD para controlar quem tem acesso ao InsideView.
* Pode permitir que os seus utilizadores se inscrevam automaticamente no InsideView (súmido único) com as suas contas AD Azure.
* Pode gerir as suas contas num local central: o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte [o Single sign-on para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o InsideView, precisa de ter:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição insideView que tem um único sinal de entrada ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar Azure AD um único sinal de acesso em um ambiente de teste.

* O InsideView suporta sSO iniciado pelo IdP.

## <a name="add-insideview-from-the-gallery"></a>Adicionar InsideView da galeria

Para configurar a integração do InsideView no Azure AD, é necessário adicionar o InsideView da galeria à sua lista de aplicações geridas pelo SaaS.

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Ir para **aplicações da Empresa**  >  **Todas as aplicações**:

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **Nova aplicação** na parte superior da janela:

    ![Selecione nova aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **InsideView**. Selecione **InsideView** nos resultados da pesquisa e, em seguida, **selecione Adicionar**.

    ![Resultados da pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, irá configurar e testar o Azure AD com o InsideView utilizando um utilizador de teste chamado Britta Simon.
Para ativar um único sinal de entrada, é necessário estabelecer uma relação entre um utilizador Azure AD e o utilizador correspondente no InsideView.

Para configurar e testar o Azure AD com o InsideView, é necessário completar estes passos:

1. **[Configurar o Azure AD um único sinal para](#configure-azure-ad-single-sign-on)** ativar a funcionalidade para os seus utilizadores.
2. **[Configurar o InsideView um único sinal de entrada no](#configure-insideview-single-sign-on)** lado da aplicação.
3. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com um único sinal de sessão.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para ativar um único sinal de Azure AD para o utilizador.
5. Crie um utilizador de **[teste InsideView](#create-an-insideview-test-user)** que esteja ligado à representação AD AZure do utilizador.
6. **[Teste um único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativará a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com o InsideView, tome estes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação InsideView, selecione **Single sign-on**:

    ![Selecione um único sinal de s-on](common/select-sso.png)

2. Na caixa de diálogo **de método de inscrição única,** selecione o modo **SAML/WS-Fed** para permitir um único sinal de entrada:

    ![Selecione um único método de inscrição](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** selecione o ícone **Editar** para abrir a caixa de diálogo **de configuração DE SAML básica:**

    ![Ícone editar](common/edit-urls.png)

4. Na caixa de diálogo **de configuração DE SAML Básico,** tome os seguintes passos.

    ![Caixa básica de diálogo de configuração SAML](common/idp-reply.png)

    Na caixa **URL de resposta,** introduza um URL neste padrão:

    `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > Este valor é um espaço reservado. Tem de utilizar a URL de resposta real. Contacte a [equipa de suporte insideView](mailto:support@insideview.com) para obter o valor. Também pode consultar os padrões mostrados na caixa de diálogo **de configuração SAML básica** no portal Azure.

5. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** selecione o link **de descarregamento** ao lado **do Certificado (Raw)**, de acordo com os seus requisitos, e guarde o certificado no seu computador:

    ![Link de descarregamento de certificado](common/certificateraw.png)

6. Na secção Configurar o **InsideView,** copie os URLs apropriados, com base nos seus requisitos:

    ![Copiar os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de login**.

    1. **Identificador Azure Ad.**

    1. **URL logout**.

### <a name="configure-insideview-single-sign-on"></a>Configurar o InsideView um único sinal de entrada

1. Numa nova janela do navegador web, inscreva-se no site da empresa InsideView como administrador.

1. Na parte superior da janela, selecione **Admin**, **SingleSignOn Settings**e, em seguida, **Adicione SAML**.
   
   ![Configurações de inscrição únicas do SAML](./media/insideview-tutorial/ic794135.png "Configurações de inscrição únicas do SAML")

1. Na secção **Adicionar um Novo SAML,** tome os seguintes passos.

    ![Adicione uma nova secção SAML](./media/insideview-tutorial/ic794136.png "Adicione uma nova secção SAML")

    1. Na caixa **STS Name,** insira um nome para a sua configuração.

    1. Na caixa **EndPoint Não solicitada SamlP/WS-Fed,** cole o valor URL de **login** que copiou do portal Azure.

    1. Abra o certificado Raw que descarregou a partir do portal Azure. Copie o conteúdo do certificado para a área de transferência e, em seguida, cole o conteúdo na caixa **de Certificado STS.**

    1. Na caixa de mapeamento do **ID do utilizador crm,** insira **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** .

    1. Na caixa **de mapeamento de e-mail crm,** insira **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** .

    1. Na caixa **de mapeamento de primeiro nome crm,** insira **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`** .

    1. Na caixa **de Mapeamento de Creme de Última Nome,** insira **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`** .  

    1. Selecione **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste chamado Britta Simon no portal Azure.

1. No portal Azure, selecione **O Diretório Ativo Azure** no painel esquerdo, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**:

    ![Selecionar Todos os utilizadores](common/users.png)

2. Selecione **Novo utilizador** na parte superior da janela:

    ![Selecione Novo utilizador](common/new-user.png)

3. Na caixa de diálogo **do utilizador,** tome os seguintes passos.

    ![Caixa de diálogo do utilizador](common/user-properties.png)

    1. Na caixa **Nome,** **insira BrittaSimon**.
  
    1. Na caixa **do nome do utilizador,** **introduza BrittaSimon@ . \<yourcompanydomain> . \<extension> ** (Por exemplo, BrittaSimon@contoso.com .)

    1. Selecione **Mostrar Palavra-Passe**e, em seguida, anotar o valor que está na caixa **de palavra-passe.**

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que Britta Simon use a Azure single sign-on, concedendo-lhe acesso ao InsideView.

1. No portal Azure, selecione **aplicações Enterprise**, selecione **Todas as aplicações**e, em seguida, selecione **InsideView**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **InsideView**.

    ![Lista de candidaturas](common/all-applications.png)

3. No painel esquerdo, selecione **Utilizadores e grupos:**

    ![Selecionar Utilizadores e grupos](common/users-groups-blade.png)

4. **Selecione Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Adicionar Atribuição'.**

    ![Selecione Adicionar utilizador](common/add-assign-user.png)

5. Na caixa de diálogo **de Utilizadores e grupos,** selecione **Britta Simon** na lista de utilizadores e, em seguida, clique no botão **Select** na parte inferior da janela.

6. Se espera um valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Clique no botão **Selecione** na parte inferior da janela.

7. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

### <a name="create-an-insideview-test-user"></a>Criar um utilizador de teste InsideView

Para que os utilizadores de Azure AD entrem no InsideView, é necessário adicioná-los ao InsideView. Precisa adicioná-las manualmente.

Para criar utilizadores ou contactos no InsideView, contacte a equipa de suporte do [InsideView.](mailto:support@insideview.com)

> [!NOTE]
> Pode utilizar qualquer ferramenta de criação de conta de utilizador ou API fornecida pelo InsideView para fornecer contas de utilizador Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Agora tem de testar a sua configuração de inscrição única AD Azure utilizando o Painel de Acesso.

Quando selecionar o azulejo InsideView no Painel de Acesso, deverá ser automaticamente inscrito no exemplo InsideView para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [o Access e utilize aplicações no portal My Apps.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
