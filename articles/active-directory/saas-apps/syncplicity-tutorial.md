---
title: 'Tutorial: Integração do Azure Ative Directory com Sincronização Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Syncplicity.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: da532adfa2d4ab97edb44de9ae49c646ccdff381
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88544898"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Tutorial: Integrar a Sincronização com o Diretório Ativo Azure

Neste tutorial, você aprenderá a integrar a Syncplicity com o Azure Ative Directory (Azure AD). Quando integrar a Syncplicity com AZure AD, pode:

* Controlo em Azure AD que tem acesso à Syncplicity.
* Permitir que os seus utilizadores sejam automaticamente inscritos na Syncplicity com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode ter um mês de teste gratuito [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Sincronização única assinatura ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. A Sincronização suporta o SSO iniciado pela **SP.**

## <a name="adding-syncplicity-from-the-gallery"></a>Adicionar Sincronização da galeria

Para configurar a integração da Syncplicity no AD Azure, é necessário adicionar Syncplicity da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **escreva Syncplicity** na caixa de pesquisa.
1. Selecione **Syncplicity** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar Azure AD SSO

Configure e teste Azure AD SSO com Syncplicity usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Syncplicity.

Para configurar e testar o Azure AD SSO com Syncplicity, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Syncplicity SSO](#configure-syncplicity-sso)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
5. **[Create Syncplicity test user](#create-syncplicity-test-user)** - para ter uma contraparte de B.Simon em Syncplicity que está ligada à representação AD Ad Azure do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Syncplicity,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na página **de Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<companyname>.syncplicity.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente Syncplicity](https://www.syncplicity.com/contact-us) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Syncplicity,** copie os URL(s) apropriados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso"></a>Configure sincronização SSO

1. Inscreva-se no seu inquilino **de Syncplicity.**

1. No menu em cima, clique em **administração,** selecione **definições**e, em seguida, clique **em domínio personalizado e sinte-on único**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Na página de diálogo **single Sign-On (SSO),** execute os seguintes passos:

    ![SSO Sign-On Único \(\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. Na caixa de texto **de domínio personalizado,** digite o nome do seu domínio.
  
    b. Selecione **Ativado** como **Estado de Sign-On único**.

    c. Na caixa de texto **Id da Entidade,** cole o valor **identificador (Entity ID),** que utilizou na **Configuração BÁSICA SAML** no portal Azure.

    d. Na caixa de texto **URL de página de início de sessão,** cole o URL de **login** que copiou do portal Azure.

    e. Na caixa de texto URL da **página logout,** cole o **URL logout** que copiou do portal Azure.

    f. No **Certificado de Fornecedor de Identidade,** clique em Escolher o **ficheiro**e, em seguida, faça o upload do certificado que descarregou a partir do portal Azure.

    exemplo, CLIQUE **EM GUARDAR ALTERAÇÕES.**

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Syncplicity.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Syncplicity**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-syncplicity-test-user"></a>Criar utilizador de teste de Syncplicity

Para que os utilizadores da AZure AD possam fazer o seu slicição, devem ser abastecidos à aplicação Syncplicity. Esta secção descreve como criar contas de utilizador Azure AD em Syncplicity.

**Para doar uma conta de utilizador à Syncplicity, execute os seguintes passos:**

1. Inscreva-se no seu inquilino **Syncplicity** (por exemplo: `https://company.Syncplicity.com` ).

1. Clique **em administração** e selecione **contas de utilizador** e, em seguida, clique em ADD A **USER**.

    ![Gerir utilizadores](./media/syncplicity-tutorial/ic769764.png "Gerir Utilizadores")

1. Digite os endereços de **E-mail** de uma conta AD Azure que pretende obter, selecione **Utilizador** como **Role**e, em seguida, clique em **SEGUINTE**.

    ![Informação da Conta](./media/syncplicity-tutorial/ic769765.png "Informações da Conta")

    > [!NOTE]
    > O titular da conta AZure AD recebe um e-mail incluindo um link para confirmar e ativar a conta.

1. Selecione um grupo na sua empresa para que o seu novo utilizador se torne membro e, em seguida, clique em **SEGUINTE**.

    ![Adesão ao Grupo](./media/syncplicity-tutorial/ic769772.png "Adesão ao Grupo")

    > [!NOTE]
    > Se não houver grupos listados, clique em **SEGUINTE**.

1. Selecione as pastas que pretende colocar sob o controlo da Syncplicity no computador do utilizador e, em seguida, clique em **SEGUINTE**.

    ![Pastas de sincronização](./media/syncplicity-tutorial/ic769773.png "Pastas de sincronização")

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador Syncplicity ou APIs fornecidas pela Syncplicity para fornecer contas de utilizador Azure AD.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo syncplicity no Painel de Acesso, deverá ser automaticamente inscrito na Sincronização para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)