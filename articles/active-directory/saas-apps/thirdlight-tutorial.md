---
title: 'Tutorial: Integração do Diretório Ativo Azure com | ThirdLight Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar um único sign-on entre Azure Ative Directory e ThirdLight.
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
ms.openlocfilehash: f15d00f1050177c6255fb5528f03314153c3ed9a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92514668"
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Tutorial: Integração do Diretório Ativo Azure com o ThirdLight

Neste tutorial, você vai aprender a integrar ThirdLight com Azure Ative Directory (Azure AD). Esta integração proporciona estes benefícios:

* Pode utilizar o Azure AD para controlar quem tem acesso ao ThirdLight.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no ThirdLight (único sinte) com as suas contas AD Azure.
* Pode gerir as suas contas num local central: o portal Azure.

Se quiser saber mais sobre a integração de aplicações saaS com a Azure AD, consulte [o Single sign-on para aplicações no Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração Azure AD com a ThirdLight, você precisa ter:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição thirdLight que tem um único sinal de insusitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar Azure AD um único sinal de acesso em um ambiente de teste.

* A ThirdLight suporta sSO iniciado pelo SP.

## <a name="add-thirdlight-from-the-gallery"></a>Adicione ThirdLight da galeria

Para configurar a integração do ThirdLight em Azure AD, precisa de adicionar ThirdLight da galeria à sua lista de aplicações geridas pelo SaaS.

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Ir para **aplicações da Empresa**  >  **Todas as aplicações**:

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **Nova aplicação** na parte superior da janela:

    ![Selecione nova aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **a ThirdLight**. Selecione **ThirdLight** nos resultados da pesquisa e, em seguida, **selecione Adicionar**.

     ![Resultados da pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, irá configurar e testar o Azure AD com o ThirdLight utilizando um utilizador de teste chamado Britta Simon.
Para ativar um único sinal, é necessário estabelecer uma relação entre um utilizador Azure AD e o utilizador correspondente no ThirdLight.

Para configurar e testar o Azure AD com o ThirdLight, é necessário completar estes passos:

1. **[Configurar o Azure AD um único sinal para](#configure-azure-ad-single-sign-on)** ativar a funcionalidade para os seus utilizadores.
2. **[Configurar o terceiro sinal único no](#configure-thirdlight-single-sign-on)** lado da aplicação.
3. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com um único sinal de sessão.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para ativar um único sinal de Azure AD para o utilizador.
5. Crie um utilizador de **[teste ThirdLight](#create-a-thirdlight-test-user)** que esteja ligado à representação AD AZure do utilizador.
6. **[Teste um único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativará a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com ThirdLight, tome estes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação ThirdLight, selecione **Single sign-on**:

    ![Selecione um único sinal de s-on](common/select-sso.png)

2. Na caixa de diálogo **de método de inscrição única,** selecione o modo **SAML/WS-Fed** para permitir um único sinal de entrada:

    ![Selecione um único método de inscrição](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** selecione o ícone **Editar** para abrir a caixa de diálogo **de configuração DE SAML básica:**

    ![Ícone editar](common/edit-urls.png)

4. Na caixa de diálogo **de configuração DE SAML Básica,** complete os seguintes passos.

    ![Caixa básica de diálogo de configuração SAML](common/sp-identifier.png)

    1. Na **caixa de URL** sign in, introduza um URL neste padrão:
    
          `https://<subdomain>.thirdlight.com/`

    1. Na caixa **identifier (Entity ID),** introduza um URL neste padrão:

       `https://<subdomain>.thirdlight.com/saml/sp`

       > [!NOTE]
       > Estes valores são espaços reservados. Tens de usar o URL e o identificador de inscrição real. Contacte a [equipa de apoio da ThirdLight](https://www.thirdlight.com/support) para obter os valores. Também pode consultar os padrões mostrados na caixa de diálogo **de configuração SAML básica** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** selecione o link **de descarregamento** ao lado **do Metadados XML da Federação,** de acordo com os seus requisitos, e guarde o ficheiro no seu computador:

    ![Link de descarregamento de certificado](common/metadataxml.png)

6. Na secção Configurar a **Terceira Luz,** copie os URLs apropriados, com base nos seus requisitos:

    ![Copiar os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de login**.

    1. **Identificador Azure Ad.**

    1. **URL logout**.

### <a name="configure-thirdlight-single-sign-on"></a>Configurar o sign-on único da ThirdLight

1. Numa nova janela do navegador web, inscreva-se no site da empresa ThirdLight como administrador.

1. Ir para a Administração do Sistema **de Configuração**  >    >  **SAML2:**

    ![Administração de Sistema](./media/thirdlight-tutorial/ic805843.png "Administração de Sistema")

1. Na secção de configuração SAML2, tome os seguintes passos.
  
    ![Secção de configuração SAML2](./media/thirdlight-tutorial/ic805844.png "Secção de configuração SAML2")

    1. Selecione **Ativar O único sinal de inscrição do SAML2**.

    1. Em **Fonte para Metadados IdP,** selecione **Metadados de IdP de carga de XML**.

    1. Abra o ficheiro de metadados que descarregou a partir do portal Azure na secção anterior. Copie o conteúdo do ficheiro e cole-o na caixa **IdP Metadata XML.**

    1. **Selecione Guardar as definições SAML2**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste chamado Britta Simon no portal Azure.

1. No portal Azure, selecione **O Diretório Ativo Azure** no painel esquerdo, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**:

    ![Selecionar Todos os utilizadores](common/users.png)

2. Selecione **Novo utilizador** na parte superior da janela:

    ![Selecione Novo utilizador](common/new-user.png)

3. Na caixa de diálogo **do utilizador,** tome os seguintes passos.

    ![Caixa de diálogo do utilizador](common/user-properties.png)

    1. Na caixa **Nome,** **insira BrittaSimon**.
  
    1. Na caixa **do nome do utilizador,** **introduza BrittaSimon@ . \<yourcompanydomain> . \<extension>** (Por exemplo, BrittaSimon@contoso.com .)

    1. Selecione **Mostrar Palavra-Passe** e, em seguida, anotar o valor que está na caixa **de palavra-passe.**

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que Britta Simon use a Azure single sign-on, concedendo-lhe acesso à ThirdLight.

1. No portal Azure, selecione **aplicações Enterprise**, selecione **Todas as aplicações** e, em seguida, selecione **ThirdLight**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **ThirdLight**.

    ![Lista de candidaturas](common/all-applications.png)

3. No painel esquerdo, selecione **Utilizadores e grupos:**

    ![Selecionar Utilizadores e grupos](common/users-groups-blade.png)

4. **Selecione Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Adicionar Atribuição'.**

    ![Selecione Adicionar utilizador](common/add-assign-user.png)

5. Na caixa de diálogo **de Utilizadores e grupos,** selecione **Britta Simon** na lista de utilizadores e, em seguida, clique no botão **Select** na parte inferior da janela.

6. Se espera um valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Clique no botão **Selecione** na parte inferior da janela.

7. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

### <a name="create-a-thirdlight-test-user"></a>Criar um utilizador de teste ThirdLight

Para permitir que os utilizadores de Azure AD entrem no ThirdLight, é necessário adicioná-los ao ThirdLight. Precisa adicioná-las manualmente.

Para criar uma conta de utilizador, tome estes passos:

1. Inscreva-se no site da empresa ThirdLight como administrador.

1. Aceda ao **separador Utilizadores.**

1. Selecione **Utilizadores e Grupos**.

1. **Selecione Adicionar novo utilizador**.

1. Insira o nome de utilizador, um nome ou descrição e o endereço de e-mail de uma conta AD Azure válida que pretende prever. Escolha uma Pré-sintonia ou grupo de novos membros.

1. Selecione **Criar**.

> [!NOTE]
> Pode utilizar qualquer ferramenta de criação de conta de utilizador ou API fornecida pela ThirdLight para fornecer contas de utilizador Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Agora tem de testar a sua configuração de inscrição única AD Azure utilizando o Painel de Acesso.

Quando selecionar o azulejo ThirdLight no Painel de Acesso, deverá ser automaticamente inscrito na instância ThirdLight para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [o Access e utilize aplicações no portal My Apps.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](./tutorial-list.md) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)