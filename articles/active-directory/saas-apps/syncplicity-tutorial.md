---
title: 'Tutorial: Integração do Diretório Ativo Azure com Sincronização [) Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: 663958ae367162eaeb336c819d1d219dc74a2cbe
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74233288"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Tutorial: Integrar a Sincronização com o Diretório Ativo Azure

Neste tutorial, aprenderá a integrar a Sincronização com o Diretório Ativo Azure (Azure AD). Quando integrar a Sincronização com a AD Azure, pode:

* Controlo em Azure AD que tem acesso à Sincronização.
* Ative que os seus utilizadores sejam automaticamente inscritos na Syncplicity com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode ter um mês de experiência gratuita [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Sincronização única de inscrição (SSO) ativada por subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. A sincronização suporta **o SP** iniciado SSO.

## <a name="adding-syncplicity-from-the-gallery"></a>Adicionar Sincronização da galeria

Para configurar a integração da Syncplicity em Azure AD, precisa adicionar A Sincronização da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Sincronização** na caixa de pesquisa.
1. **Selecione Syncplicity** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso"></a>Configure e teste Azure AD SSO

Configure e teste Azure AD SSO com Syncplicity utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Syncplicity.

Para configurar e testar o Azure AD SSO com sincronização, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o SSO](#configure-syncplicity-sso)** de Sincronização - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
5. **[Create Syncplicity test user](#create-syncplicity-test-user)** - para ter uma contraparte de B.Simon em Syncplicity que está ligada à representação azure AD do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Syncplicity,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na página **basic SAML Configuração,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<companyname>.syncplicity.com`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<companyname>.syncplicity.com/sp`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de suporte do [Cliente da Syncplicity](https://www.syncplicity.com/contact-us) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sinal com página **SAML,** na secção certificado de **assinatura SAML,** encontre **certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção Configurar a **Sincronização,** copie os URL(s) adequados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso"></a>Configurar SSO de Sincronização

1. Inscreva-se no seu inquilino **da Syncplicity.**

1. No menu em cima, clique em **administrar,** selecione **definições**e, em seguida, clique no **domínio Personalizado e no início de sessão individual**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Na página de diálogo **Single Sign-On (SSO),** execute os seguintes passos:

    ![SSO \(único sign-on\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. Na caixa de texto **custom Domain,** escreva o nome do seu domínio.
  
    b. Selecione **ativado** como **estado de inscrição único**.

    c. Na caixa de texto **Id entidade,** colar o valor do **identificador (ID da entidade),** que utilizou na **Configuração Básica SAML** no portal Azure.

    d. Na caixa de texto URL da **página sign-in,** colá o URL de **login** que copiou do portal Azure.

    e. Na caixa de texto URL da **página de Logout,** colá o URL de **logout** que copiou do portal Azure.

    f. No Certificado de **Fornecedor de Identidade,** clique em **Escolher ficheiro**, e depois faça upload do certificado que descarregou do portal Azure.

    g. Clique em **GUARDAR ALTERAÇÕES**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso à Syncplicity.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Syncplicity**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-syncplicity-test-user"></a>Criar utilizador de teste de sincronização

Para que os utilizadores da AD Azure possam iniciar o seu início, devem ser aprovisionados na aplicação Syncplicity. Esta secção descreve como criar contas de utilizadores da AD Azure em Syncplicity.

**Para fornecer uma conta de utilizador à Syncplicity, execute os seguintes passos:**

1. Inscreva-se no seu inquilino de `https://company.Syncplicity.com` **Sincronização** (por exemplo: ).

1. Clique em **administrar** e selecione **contas de utilizador** e, em seguida, clique em ADICIONAR Um **UTILIZADOR**.

    ![Gerir utilizadores](./media/syncplicity-tutorial/ic769764.png "Gerir Utilizadores")

1. Digite os endereços de **e-mail** de uma conta Azure AD que pretende fornecer, selecione **User** as **Role**, e, em seguida, clique em **NEXT**.

    ![Informação sobre Contas](./media/syncplicity-tutorial/ic769765.png "Informações da Conta")

    > [!NOTE]
    > O titular da conta Azure AD recebe um e-mail incluindo um link para confirmar e ativar a conta.

1. Selecione um grupo na sua empresa que o seu novo utilizador deve tornar-se membro e, em seguida, clique em **NEXT**.

    ![Associação de Grupos](./media/syncplicity-tutorial/ic769772.png "Associação de Grupos")

    > [!NOTE]
    > Se não houver grupos listados, clique em **NEXT**.

1. Selecione as pastas que pretende colocar sob o controlo da Syncplicity no computador do utilizador e, em seguida, clique em **NEXT**.

    ![Pastas de sincronização](./media/syncplicity-tutorial/ic769773.png "Pastas de sincronização")

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador da Syncplicity ou APIs fornecidas pela Syncplicity para fornecer contas de utilizador Azure AD.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo Syncplicity no Painel de Acesso, deve ser automaticamente inscrito na Syncplicity para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)