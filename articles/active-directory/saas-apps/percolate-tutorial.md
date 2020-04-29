---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Percolate Microsoft Docs'
description: Neste tutorial, você vai aprender a configurar um único sign-on entre o Azure Ative Directory e o Percolate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 355f9659-b378-44c9-aa88-236e9b529a53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: a6c1f893757baf1e6c85420b31997a5073cff684
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67094605"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Tutorial: Integração do Diretório Ativo Azure com percolate

Neste tutorial, você vai aprender a integrar Percolate com o Azure Ative Directory (Azure AD).

Esta integração proporciona estes benefícios:

* Pode utilizar a AD Azure para controlar quem tem acesso a Percolate.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Percolate (única investida) com as suas contas Azure AD.
* Pode gerir as suas contas num local central: o portal Azure.

Para saber mais sobre a integração de aplicações saaS com a Azure AD, consulte [o single sign-on para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Percolate, é necessário ter:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição Percolate que tem um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar o único sign-on Azure AD em um ambiente de teste.

* Percolate suporta SSO iniciado por SP e iniciado por IDP.

## <a name="add-percolate-from-the-gallery"></a>Adicione Perclate da galeria

Para configurar a integração da Percolate em Azure AD, você precisa adicionar Percolate da galeria à sua lista de aplicações geridas saaS.

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory:**

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Ir a **aplicações** > da Enterprise**Todas as aplicações:**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **Nova aplicação** na parte superior da janela:

    ![Selecione Nova aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Percolate**. Selecione **Percolate** nos resultados da pesquisa e, em seguida, **selecione Adicionar**.

     ![Resultados de pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, irá configurar e testar um único sinal de Azure AD com percolate utilizando uma utilizadora de teste chamada Britta Simon.
Para permitir uma única inscrição, é necessário estabelecer uma relação entre um utilizador da AD Azure e o utilizador correspondente em Percolate.

Para configurar e testar o único sinal de Azure AD com percolate, é necessário completar estes passos:

1. Configure o único sinal de entrada **[da AD Azure](#configure-azure-ad-single-sign-on)** para ativar a funcionalidade para os seus utilizadores.
2. **[Configure o único sinal percolado](#configure-percolate-single-sign-on)** no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de AD Azure.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para ativar o único sinal de entrada da Azure AD para o utilizador.
5. **[Crie um utilizador de teste Percolate](#create-a-percolate-test-user)** ligado à representação da AD Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, irá ativar o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sinal de Azure AD com percolate, tome estes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Perclate,** selecione **Single sign-on:**

    ![Selecione um único sinal](common/select-sso.png)

2. Na selectuma única caixa de diálogo do **método de sinalização,** selecione o modo **SAML/WS-Fed** para ativar um único sinal:

    ![Selecione um único método de sinalização](common/select-saml-option.png)

3. Na configuração de um único sign-on com a página **SAML,** selecione o ícone **Editar** para abrir a caixa de diálogo **de configuração SAML básica:**

    ![Ícone editar](common/edit-urls.png)

4. Na caixa de diálogo **Basic SAML Configuration,** não é necessário tomar qualquer medida para configurar a aplicação no modo iniciado pelo IDP. A aplicação já está integrada com o Azure.

    ![Domínio perclado e URLs informações únicas de inscrição](common/preintegrated.png)

5. Se pretender configurar a aplicação no modo iniciado por SP, selecione **Definir URLs adicionais** e, na caixa **de URL Sign on,** introduza: **https://percolate.com/app/login**

   ![Domínio perclado e URLs informações únicas de inscrição](common/metadata-upload-additional-signon.png)
6. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** selecione o ícone **Copy** para copiar o Url de **Metadados da Federação de Aplicações**. Guarde este URL.

    ![Copiar o URL de Metadados da Federação de Aplicações](common/copy-metadataurl.png)

7. Na secção **Configurar percolado,** copie os URLs apropriados, com base nos seus requisitos.

    ![Copiar os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de login**.

    1. **Identificador Azure AD**.

    1. **URL de logout**.

### <a name="configure-percolate-single-sign-on"></a>Configure o único sinal de Perclate

1. Numa nova janela do navegador web, inscreva-se na Percolate como administrador.

2. No lado esquerdo da página inicial, selecione **Definições:**
    
    ![Selecionar Definições](./media/percolate-tutorial/configure01.png)

3. No painel esquerdo, selecione **SSO** ao abrigo **da Organização:**

    ![Selecione SSO em Organização](./media/percolate-tutorial/configure02.png)

    1. Na caixa **URL login,** colhe o valor URL de **Login** que copiou do portal Azure.

    1. Na caixa **id** entidade, colhe o valor do **Identificador AD Azure** que copiou do portal Azure.

    1. No Notepad, abra o certificado codificado base-64 que descarregou do portal Azure. Copie o seu conteúdo e cole-o na caixa de **certificados x509.**

    1. Na caixa de **atributo saqueado por e-mail,** introduza o **endereço de e-mail**.

    1. A caixa URL de **metadados** do fornecedor de identidade é um campo opcional. Se copiou um Url de **Metadados da Federação** de Aplicações do portal Azure, pode colá-lo nesta caixa.

    1. Na lista **de Pedidos AuthNRequests,** selecione **No**.

    1. Na lista de **fornecimento automático Enable SSO,** selecione **No**.

    1. Selecione **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar uma utilizadora de teste chamada Britta Simon no portal Azure.

1. No portal Azure, selecione **Azure Ative Directory** no painel esquerdo, selecione **Utilizadores,** e, em seguida, selecione **Todos os utilizadores:**

    ![Selecionar Todos os utilizadores](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã:

    ![Selecione Novo utilizador](common/new-user.png)

3. Na caixa de diálogo **do Utilizador,** tome os seguintes passos.

    ![Caixa de diálogo do utilizador](common/user-properties.png)

    1. Na caixa **de nomes,** entre **brittaSimon.**
  
    1. Na caixa de **nomes do Utilizador,** introduza **\<BrittaSimon@> de domínio da\< sua empresa.>de extensão. ** (Por exemplo, BrittaSimon@contoso.com.)

    1. Selecione **Mostrar palavra-passe**e, em seguida, anote o valor que está na caixa **password.**

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que Britta Simon use um único sign-on azure ad, concedendo-lhe acesso a Percolate.

1. No portal Azure, selecione **aplicações Enterprise,** selecione **Todas as aplicações,** e, em seguida, selecione **Percolate**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Percolate**.

    ![Lista de candidaturas](common/all-applications.png)

3. No painel esquerdo, selecione **Utilizadores e grupos:**

    ![Selecionar Utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Atribuição adicionar'.**

    ![Selecionar Utilizadores e grupos](common/add-assign-user.png)

5. Na caixa de diálogo **De utilizadores e grupos,** selecione **Britta Simon** na lista de utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se esperar um valor de papel na afirmação Do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Clique no botão **Select** na parte inferior do ecrã.

7. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

### <a name="create-a-percolate-test-user"></a>Criar um utilizador de teste percolado

Para permitir que os utilizadores de Anúncios Azure assinem a Percolate, é necessário adicioná-los à Percolate. Precisa adicioná-las manualmente.

Para criar uma conta de utilizador, tome estas medidas:

1. Inscreva-se na Percolate como administrador.

2. No painel esquerdo, selecione **Utilizadores** no âmbito **da Organização**. Selecione **novos utilizadores:**

    ![Selecione Novos utilizadores](./media/percolate-tutorial/configure03.png)

3. Na página de **utilizadores Create,** tome os seguintes passos.

    ![Criar página de utilizadores](./media/percolate-tutorial/configure04.png)

    1. Na caixa de **e-mail,** insira o endereço de e-mail do utilizador. Por exemplo, brittasimon@contoso.com.

    1. Na caixa de **nomes Completos,** introduza o nome do utilizador. Por exemplo, **Brittasimon.**

    1. Selecione **Criar utilizadores**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Agora precisa de testar a configuração de um único sinal de acesso do Azure AD utilizando o Painel de Acesso.

Quando selecionar o azulejo Percolado no Painel de Acesso, deve ser automaticamente inscrito na instância Percolate para a qual configura o SSO. Para mais informações, consulte [O Acesso e utilize aplicações no portal My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)