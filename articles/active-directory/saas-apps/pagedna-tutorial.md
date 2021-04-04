---
title: 'Tutorial: Integração do Diretório Ativo Azure com o PageDNA | Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o PageDNA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: e1ccd337851f8242526362675a11280e33584082
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95993929"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>Tutorial: Integração do Diretório Ativo Azure com o PageDNA

Neste tutorial, aprende-se a integrar o PageDNA com o Azure Ative Directory (Azure AD).

A integração do PageDNA com a AD Azure proporciona-lhe os seguintes benefícios:

* No Azure AD, pode controlar quem tem acesso ao PageDNA.
* Pode permitir que os seus utilizadores se inscrevam automaticamente no PageDNA (súmido único) com as suas contas AD Azure.
* Pode gerir as suas contas num local central: o portal Azure.

Para mais detalhes sobre o software como uma integração de aplicações de serviço (SaaS) com a Azure AD, consulte [o que é o acesso à aplicação e um único acesso com o Azure Ative Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o PageDNA, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma subscrição pageDNA com um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD um único sinal num ambiente de teste e integra o PageDNA com a Azure AD.

O PageDNA suporta as seguintes funcionalidades:

* SSO iniciado pelo SP.

* Provisão de utilizadores just-in-time.

## <a name="add-pagedna-from-the-azure-marketplace"></a>Adicionar PageDNA do Mercado Azure

Para configurar a integração do PageDNA no AD AZure, é necessário adicionar o PageDNA do Mercado Azure à sua lista de aplicações geridas para o SaaS:

1. Inicie sessão no [portal do Azure](https://portal.azure.com?azure-portal=true).
1. No painel esquerdo, selecione **Azure Active Directory**.

    ![A opção Azure Ative Directory](common/select-azuread.png)

1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

1. Para adicionar uma nova aplicação, selecione **+ Nova aplicação** na parte superior do painel.

    ![A nova opção de aplicação](common/add-new-app.png)

1. Na caixa de pesquisa, insira **o PageDNA**. Nos resultados da pesquisa, selecione **PageDNA** e, em seguida, **selecione Adicionar** para adicionar a aplicação.

    ![PageDNA na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o PageDNA com base num utilizador de teste chamado **Britta Simon**. Para um único pré-in funcionar, deve estabelecer uma ligação entre um utilizador Azure AD e o utilizador relacionado no PageDNA.

Para configurar e testar o Azure AD com o PageDNA, é necessário completar os seguintes blocos de construção:

1. **[Configurar o Azure AD um único sinal para](#configure-azure-ad-single-sign-on)** permitir que os seus utilizadores utilizem esta funcionalidade.
1. **[Configurar o sign-on único pageDNA](#configure-pagedna-single-sign-on)** para configurar as definições de inscrição única no lado da aplicação.
1. **[Crie um utilizador de teste AZure AD](#create-an-azure-ad-test-user)** para testar o Azure AD com Britta Simon.
1. **[Atribua ao utilizador de teste AZure AD](#assign-the-azure-ad-test-user)** para permitir que a Britta Simon utilize um único sinal de Ad AD.
1. Crie um utilizador de **[teste PageDNA](#create-a-pagedna-test-user)** para que haja um utilizador chamado Britta Simon no PageDNA que está ligado ao utilizador da AD Azure chamado Britta Simon.
1. **[Teste um único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD um único sinal de inscrição com o PageDNA, tome os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **PageDNA,** selecione **'Único s-on'.**

    ![Configurar a opção de inscrição única](common/select-sso.png)

1. No **painel de método de inscrição,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súm.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

1. Na **configuração single Sign-On com painel SAML,** selecione **Editar** (o ícone do lápis) para abrir o painel **de configuração DE SAML básico.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. No painel **de configuração básico do SAML,** tome os seguintes passos:

    ![PageDNA domínio e URLs informações únicas de súmato](common/sp-identifier.png)

    1. Na caixa url do **Signo,** introduza um URL utilizando um dos seguintes padrões:

        ```https
        https://stores.pagedna.com/<your site>
        https://<your domain>
        https://<your domain>/<your site>
        https://www.nationsprint.com/<your site>
        ```

    1. Na caixa **identifier (Entity ID),** introduza um URL utilizando um dos seguintes padrões:

        ```https
        https://stores.pagedna.com/<your site>/saml2ep.cgi
        https://www.nationsprint.com/<your site>/saml2ep.cgi
        ```

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL e o identificador de inscrição real. Para obter estes valores, contacte a equipa de suporte do [PageDNA](mailto:success@pagedna.com). Também pode consultar os padrões indicados no painel **de configuração SAML básico** no portal Azure.

1. Na **configuração single Sign-On com painel SAML,** na secção Certificado de Assinatura **SAML,** selecione **Descarregamento** para descarregar **Certificado (Raw)** das opções dadas e guarde-o no seu computador.

    ![A opção de download do Certificado (Raw)](common/certificateraw.png)

1. Na secção **Configurar PageDNA,** copie o URL ou URLs de que necessita:

   * **Login URL**
   * **Identificador de Azure Ad**
   * **Logout URL**

    ![Copiar os URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>Configurar o sign-on único pageDNA

Para configurar um único sinal no lado do PageDNA, envie o Certificado descarregado (Raw) e os URLs copiados apropriados do portal Azure para a equipa de [suporte do PageDNA](mailto:success@pagedna.com). A equipa PageDNA certificar-se-á de que a ligação SSO SAML está corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, cria-se um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Utilizadores do Diretório Ativo Azure**    >    >  **Todos os utilizadores**.

    ![As opções dos Utilizadores e "Todos os utilizadores"](common/users.png)

1. Na parte superior do ecrã, selecione **+ Novo utilizador**.

    ![Nova opção de utilizador](common/new-user.png)

1. No painel de **utilização,** faça os seguintes passos:

    ![O painel de utilizador](common/user-properties.png)

    1. Na caixa **Nome,** **insira BrittaSimon**.
  
    1. Na caixa **de nome do utilizador,** insira **BrittaSimon \@ \<yourcompanydomain> . . \<extension>** Por exemplo, **brittaSimon \@ contoso.com**.

    1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, permite ao utilizador Britta Simon utilizar o Azure como único sinal, permitindo ao utilizador aceder ao PageDNA.

1. No portal Azure, selecione **aplicações Enterprise**  >  **Todas as aplicações**  >  **PageDNA**.

    ![Painel de aplicações da empresa](common/enterprise-applications.png)

1. Na lista de candidaturas, selecione **PageDNA**.

    ![PageDNA na lista de aplicações](common/all-applications.png)

1. No painel esquerdo, em **MANAGE,** **selecione Utilizadores e grupos**.

    ![A opção "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione + Adicione o utilizador** e, em seguida, selecione **Utilizadores e grupos** no painel **de atribuição de adicionar.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

1. No painel **de Utilizadores e grupos,** selecione **Britta Simon** na lista **de Utilizadores** e, em seguida, escolha **Selecione** na parte inferior do painel.

1. Se está à espera de um valor de função na afirmação SAML, então no painel **'Escolha' de Função,** selecione a função adequada para o utilizador da lista. Na parte inferior do painel, escolha **Select**.

1. No painel **de atribuição de adição,** selecione **Atribuir**.

### <a name="create-a-pagedna-test-user"></a>Criar um utilizador de teste PageDNA

Um utilizador chamado Britta Simon é agora criado no PageDNA. Não tens de fazer nada para criar este utilizador. O PageDNA suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Se um utilizador chamado Britta Simon já não existir no PageDNA, um novo é criado após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal de Azure AD utilizando o portal My Apps.

Quando selecionar **o PageDNA** no portal My Apps, deverá ser automaticamente inscrito na subscrição pageDNA para a qual configurar um único s-on. Para obter mais informações sobre o portal My Apps, consulte [o Access e utilize aplicações no portal My Apps.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais para integrar aplicações saaS com diretório ativo Azure](./tutorial-list.md)

* [Inscrição única para aplicações no Azure Ative Directory](../manage-apps/what-is-single-sign-on.md)

* [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)