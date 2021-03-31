---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Signagelive | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Signagelive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 1/11/2019
ms.author: jeedes
ms.openlocfilehash: 78324cfa58a8ac015b085052bdec7e3793befc1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96348454"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>Tutorial: Integração do Diretório Ativo Azure com Signagelive

Neste tutorial, aprende-se a integrar a Signagelive com o Azure Ative Directory (Azure AD).
A integração da Signagelive com a AZure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Signagelive.
* Pode permitir que os seus utilizadores se inscrevam automaticamente no Signagelive (único sind on) com as suas contas AD Azure.
* Pode gerir as suas contas num local central: o portal Azure.

Para obter mais informações sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único acesso com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md). Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com a Signagelive, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um [julgamento de um mês.](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura signagelive única ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Signagelive suporta SSO iniciado pelo SP.

## <a name="add-signagelive-from-the-gallery"></a>Adicione Signagelive da galeria

Para configurar a integração da Signagelive no AZure AD, adicione primeiro o Signagelive da galeria à sua lista de aplicações geridas pelo SaaS.

Para adicionar Signagelive da galeria, tome os seguintes passos:

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione o ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá a **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de  **aplicação** na parte superior da caixa de diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Signagelive**. 

     ![Signagelive na lista de resultados](common/search-new-app.png)

5. Selecione **Signagelive** a partir do painel de resultados e, em seguida, selecione o botão **Adicionar** para adicionar a aplicação.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com signagelive com base num utilizador de teste chamado **Britta Simon**.
Para um único s-on para o trabalho, deve estabelecer uma ligação entre um utilizador Azure AD e o utilizador relacionado em Signagelive.

Para configurar e testar a Azure AD com a Signagelive, complete primeiro os seguintes blocos de construção:

1. [Configurar o Azure AD um único sinal para](#configure-azure-ad-single-sign-on) permitir que os seus utilizadores utilizem esta funcionalidade.
2. [Configurar Signagelive único sinal para](#configure-signagelive-single-sign-on) configurar as definições de inscrição única no lado da aplicação.
3. [Crie um utilizador de teste AZure AD](#create-an-azure-ad-test-user) para testar o Azure AD com Britta Simon.
4. [Atribua ao utilizador de teste AZure AD](#assign-the-azure-ad-test-user) para permitir que a Britta Simon utilize um único sinal de Ad AD.
5. Crie um utilizador de [teste Signagelive](#create-a-signagelive-test-user) para ter uma contraparte de Britta Simon em Signagelive que está ligada à representação AD AZure do utilizador.
6. [Teste um único sinal para](#test-single-sign-on) verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com a Signagelive, tome os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Signagelive,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. Na caixa de diálogo **de método de inscrição única,** selecione **SAML** para ativar um único sinal de entrada.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. No **set-on único com** a página SAML, selecione **Editar** para abrir a caixa de diálogo **de configuração DE SAML Básica.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** tome os seguintes passos:

    ![Signagelive Domain e URLs informações únicas de súmis](common/sp-signonurl.png)

    Na caixa URL de entrada de **inscrição,** introduza um URL que utiliza o seguinte padrão:  `https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de inscrição real. Para obter o valor, contacte a equipa de suporte do [Cliente Signagelive.](mailto:support@signagelive.com) Também pode consultar os padrões que são mostrados na secção **Configuração Básica SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção Certificado de Assinatura **SAML,** selecione **Descarregamento** para descarregar o **Certificado (Raw)** das opções dadas de acordo com o seu requisito. Então guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/certificateraw.png)

6. Na secção **Set-up Signagelive,** copie os URL(s) de que necessita.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-signagelive-single-sign-on"></a>Configurar Signagelive Single sign-on

Para configurar um único sinal no lado signagelive, envie o Certificado descarregado **(Raw)** e copie URLs do portal Azure para a equipa de [suporte Signagelive](mailto:support@signagelive.com). Asseguram-se de que a ligação SSO SAML está corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Na caixa de diálogo **do utilizador,** tome os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome,** **insira BrittaSimon**.
  
    b. No campo **nome do utilizador,** brittasimon@yourcompanydomain.extension introduza " Por exemplo, neste caso, pode entrar BrittaSimon@contoso.com "

    c. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, note o valor que é apresentado na caixa palavra-passe.

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon utilize a Azure single sign-on, concedendo acesso a Signagelive.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **Signagelive**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Signagelive**.

    ![O link Signagelive na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Selecione o botão **de utilizador Adicionar.** Em seguida, na caixa de diálogo **de atribuição de adicionar,** selecione **Utilizadores e grupos**.

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. Na caixa de diálogo **de Utilizadores e grupos,** na lista **de Utilizadores,** selecione **Britta Simon**. Em seguida, clique no botão **Selecione** na parte inferior do ecrã.

6. Se está à espera de um valor de função na afirmação SAML, então, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. Na caixa de diálogo **'Adicionar Atribuição',** selecione o botão **'Atribuir'.**

### <a name="create-a-signagelive-test-user"></a>Criar um utilizador de teste Signagelive

Nesta secção, cria-se um utilizador chamado Britta Simon em Signagelive. Trabalhe com a [equipa de apoio Signagelive](mailto:support@signagelive.com) para adicionar os utilizadores na plataforma Signagelive. Tem de criar e ativar os utilizadores antes de utilizar uma única sessão de sessão.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, você testa a sua configuração de pré-acesso Azure AD através do portal MyApps.

Quando selecionar o azulejo **Signagelive** no portal MyApps, deverá ser automaticamente inscrito. Para mais informações sobre o portal MyApps, veja [o que é o portal MyApps?](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar apps saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)