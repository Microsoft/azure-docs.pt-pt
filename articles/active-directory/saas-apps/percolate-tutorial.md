---
title: 'Tutorial: Integração do Diretório Ativo Azure com percolate | Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar um único sign-on entre Azure Ative Directory e Percolate.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 913ffc0670e40e749bd28382d492a16891fdc5c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522277"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Tutorial: Integração do Diretório Ativo Azure com Percolate

Neste tutorial, você vai aprender a integrar Percolate com Azure Ative Diretório (Azure AD).

Esta integração proporciona estes benefícios:

* Você pode usar Azure AD para controlar quem tem acesso a Percolate.
* Pode permitir que os seus utilizadores se inscrevam automaticamente no Percolate (súmido único) com as suas contas AD Azure.
* Pode gerir as suas contas num local central: o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte [o Single sign-on para aplicações no Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o Percolate, você precisa ter:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição Percolate que tem um único sinal de acesso ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar Azure AD um único sinal de acesso em um ambiente de teste.

* Percolate suporta SSO iniciado pelo SP e iniciado pelo IdP.

## <a name="add-percolate-from-the-gallery"></a>Adicione Percolate da galeria

Para configurar a integração do Percolate no Azure AD, é necessário adicionar Percolate da galeria à sua lista de aplicações geridas pelo SaaS.

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Ir para **aplicações da Empresa**  >  **Todas as aplicações**:

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **Nova aplicação** na parte superior da janela:

    ![Selecione nova aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Percolate.** Selecione **Percolate** nos resultados da pesquisa e, em seguida, **selecione Adicionar**.

     ![Resultados da pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, irá configurar e testar o Azure AD com percolate utilizando um utilizador de teste chamado Britta Simon.
Para ativar um único sinal, é necessário estabelecer uma relação entre um utilizador Azure AD e o utilizador correspondente em Percolate.

Para configurar e testar o Azure AD com percolate, é necessário completar estes passos:

1. **[Configurar o Azure AD um único sinal para](#configure-azure-ad-single-sign-on)** ativar a funcionalidade para os seus utilizadores.
2. **[Configurar Percolato único sinal no](#configure-percolate-single-sign-on)** lado da aplicação.
3. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com um único sinal de sessão.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para ativar um único sinal de Azure AD para o utilizador.
5. Crie um utilizador de **[teste percolate](#create-a-percolate-test-user)** que esteja ligado à representação AD AZure do utilizador.
6. **[Teste um único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativará a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com Percolate, tome estes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Percolate,** selecione **Single sign-on**:

    ![Selecione um único sinal de s-on](common/select-sso.png)

2. Na caixa de diálogo **de método de inscrição única,** selecione o modo **SAML/WS-Fed** para permitir um único sinal de entrada:

    ![Selecione um único método de inscrição](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** selecione o ícone **Editar** para abrir a caixa de diálogo **de configuração DE SAML básica:**

    ![Ícone editar](common/edit-urls.png)

4. Na caixa de diálogo **de configuração de Saml Básica,** não é necessário tomar nenhuma medida para configurar a aplicação no modo iniciado pelo IdP. A aplicação já está integrada com o Azure.

    ![Informações de súmis de domínio percolate e URLs únicas](common/preintegrated.png)

5. Se pretender configurar a aplicação no modo iniciado pelo SP, selecione **Definir URLs adicionais** e, na caixa **URL signo,** insira: **https://percolate.com/app/login**

   ![Screenshot que mostra "Definir U R Ls adicionais" selecionado com a caixa de texto "Sign on U R L" realçada.](common/metadata-upload-additional-signon.png)
6. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** selecione o ícone **Copy** para copiar o Url **de Metadados da Federação de Aplicações**. Salve esta URL.

    ![Copiar o URL de metadados da Federação de Aplicações](common/copy-metadataurl.png)

7. Na secção **De Percolate Configurar,** copie os URLs apropriados, com base nos seus requisitos.

    ![Copiar os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de login**.

    1. **Identificador Azure Ad.**

    1. **URL logout**.

### <a name="configure-percolate-single-sign-on"></a>Configurar Percolate único sinal-on

1. Numa nova janela do navegador web, inscreva-se em Percolate como administrador.

2. No lado esquerdo da página inicial, selecione **Definições**:
    
    ![Selecionar Definições](./media/percolate-tutorial/configure01.png)

3. No painel esquerdo, selecione **SSO** sob **Organização:**

    ![Selecione SSO sob Organização](./media/percolate-tutorial/configure02.png)

    1. Na caixa **URL do Login,** cole o valor URL de **login** que copiou do portal Azure.

    1. Na caixa **de ID da Entidade,** cole o valor do **identificador Azure AD** que copiou do portal Azure.

    1. No Bloco de Notas, abra o certificado codificado base-64 que descarregou a partir do portal Azure. Copie o seu conteúdo e cole-o na caixa **de certificados x509.**

    1. Na caixa **de atributos de email,** insira **o emailaddress**.

    1. A **caixa URL de metadados de fornecedor de identidade** é um campo opcional. Se copiou um **Url de Metadados da Federação** de Aplicações do portal Azure, pode colar-o nesta caixa.

    1. Na lista **AuthNRequests deve ser assinada?** 

    1. Na lista **de provisionamento automático do Enable SSO,** selecione **Nº**.

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
  
    1. Na caixa **do nome do utilizador,** **introduza BrittaSimon@ . \<yourcompanydomain> . \<extension>** (Por exemplo, BrittaSimon@contoso.com .)

    1. Selecione **Mostrar Palavra-Passe** e, em seguida, anotar o valor que está na caixa **de palavra-passe.**

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que Britta Simon use Azure AD um único sinal de acesso, concedendo-lhe acesso a Percolate.

1. No portal Azure, selecione **aplicações Enterprise**, selecione **Todas as aplicações** e, em seguida, selecione **Percolate**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Percolate**.

    ![Lista de candidaturas](common/all-applications.png)

3. No painel esquerdo, selecione **Utilizadores e grupos:**

    ![Screenshot que mostra "Utilizadores e grupos" selecionados a partir do painel esquerdo.](common/users-groups-blade.png)

4. **Selecione Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Adicionar Atribuição'.**

    ![Selecionar Utilizadores e grupos](common/add-assign-user.png)

5. Na caixa de diálogo **de Utilizadores e grupos,** selecione **Britta Simon** na lista de utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se espera um valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Clique no botão **Selecione** na parte inferior do ecrã.

7. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

### <a name="create-a-percolate-test-user"></a>Criar um utilizador de teste de Percolate

Para permitir que os utilizadores de Azure AD entrem em Percolate, é necessário adicioná-los ao Percolate. Precisa adicioná-las manualmente.

Para criar uma conta de utilizador, tome estes passos:

1. Inscreva-se em Percolate como administrador.

2. No painel esquerdo, selecione **Utilizadores** sob **Organização.** Selecione **Novos utilizadores:**

    ![Selecione Novos utilizadores](./media/percolate-tutorial/configure03.png)

3. Na página de **utilizadores Create,** tome os seguintes passos.

    ![Criar página de utilizadores](./media/percolate-tutorial/configure04.png)

    1. Na caixa **de e-mail,** insira o endereço de e-mail do utilizador. Por exemplo, brittasimon@contoso.com.

    1. Na caixa **de nome completo,** insira o nome do utilizador. Por exemplo, **Brittasimon.**

    1. Selecione **Criar utilizadores**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Agora tem de testar a sua configuração de inscrição única AD Azure utilizando o Painel de Acesso.

Quando selecionar o azulejo Percolate no Painel de Acesso, deverá ser automaticamente inscrito na instância Percolate para a qual configura sSO. Para mais informações, consulte [o Access e utilize aplicações no portal My Apps.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](./tutorial-list.md) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)