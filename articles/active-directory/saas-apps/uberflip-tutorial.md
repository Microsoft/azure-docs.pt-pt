---
title: 'Tutorial: Integração do Azure Ative Directory com a Uberflip | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Uberflip.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 6bf3eccec70619bf73397e315c6d851f753ec52f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92521837"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>Tutorial: Integração do Azure Ative Directory com a Uberflip

Neste tutorial, aprende-se a integrar a Uberflip com o Azure Ative Directory (Azure AD).

A integração da Uberflip com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Uberflip.
* Pode permitir que os seus utilizadores se inscrevam automaticamente no Uberflip (súpder único) com as suas contas AD Azure.
* Pode gerir as suas contas num local central: o portal Azure.

Para mais detalhes sobre o software como uma integração de aplicações de serviço (SaaS) com a Azure AD, consulte [o que é o acesso à aplicação e um único acesso com o Azure Ative Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com a Uberflip, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma subscrição uberflip com um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

A Uberflip suporta as seguintes funcionalidades:

* SSO iniciado pelo SP e iniciado pelo IDP ( SSO).
* Provisão de utilizadores just-in-time.

## <a name="add-uberflip-from-the-azure-marketplace"></a>Adicione Uberflip do Azure Marketplace

Para configurar a integração da Uberflip no AD Azure, precisa adicionar o Uberflip do Azure Marketplace à sua lista de aplicações geridas pela SaaS:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No painel esquerdo, selecione **Azure Active Directory**.

   ![A opção Azure Ative Directory](common/select-azuread.png)

1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.

   ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

1. Para adicionar uma nova aplicação, selecione **+ Nova aplicação** na parte superior do painel.

   ![A nova opção de aplicação](common/add-new-app.png)

1. Na caixa de pesquisa, insira **Uberflip**. Nos resultados da pesquisa, selecione **Uberflip** e, em seguida, **selecione Adicionar** para adicionar a aplicação.

   ![Uberflip na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com a Uberflip com base num utilizador de teste chamado **B Simon**. Para um único sinal de sação a funcionar, é necessário estabelecer uma ligação entre um utilizador AZure AD e um utilizador relacionado no Uberflip.

Para configurar e testar o Azure AD com o Uberflip, é necessário completar os seguintes blocos de construção:

1. **[Configurar o Azure AD um único sinal para](#configure-azure-ad-single-sign-on)** permitir que os seus utilizadores utilizem esta funcionalidade.
1. **[Configure o único sinal de uberflip](#configure-uberflip-single-sign-on)** para configurar as definições de inscrição única no lado da aplicação.
1. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com B. Simon.
1. **[Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b. Simon utilize um único sinal de Ad AD.
1. Crie um utilizador de **[teste Uberflip](#create-an-uberflip-test-user)** para que haja um utilizador chamado B. Simon em Uberflip que esteja ligado ao utilizador da AZure AD chamado B. Simon.
1. **[Teste um único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com a Uberflip, tome os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Uberflip,** selecione **Single sign-on**.

    ![Configurar a opção de inscrição única](common/select-sso.png)

1. No **painel de método de inscrição,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súm.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

1. Na **configuração single Sign-On com painel SAML,** selecione **Editar** (o ícone do lápis) para abrir o painel **de configuração DE SAML básico.**

   ![A screenshot mostra a Configuração BÁSICA SAML, onde pode introduzir uma Resposta U R L.](common/edit-urls.png)

1. No painel **de configuração básico do SAML,** faça um dos seguintes passos, dependendo do modo SSO que pretende configurar:

   * Para configurar a aplicação no modo SSO iniciado pelo IDP, na caixa **URL de resposta (URL do serviço de apoio ao consumidor de afirmação),** introduza um URL utilizando o seguinte padrão:

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![Uberflip domínio e URLs informações únicas de sinal-on](common/both-replyurl.png)

     > [!NOTE]
     > Este valor não é real. Atualize este valor com o URL de resposta real. Para obter o valor real, contacte a equipa de suporte da [Uberflip.](mailto:support@uberflip.com) Também pode consultar os padrões indicados no painel **de configuração SAML básico** no portal Azure.

   * Para configurar a aplicação no modo SSO iniciado pelo SP, selecione **Definir URLs adicionais**, e na caixa **URL sign-on,** insira este URL:

     `https://app.uberflip.com/users/login`

     ![Screenshot mostra Definir U R Ls adicionais onde pode introduzir um sinal em U R L.](common/both-signonurl.png)

1. Na **configuração single Sign-On com painel SAML,** na secção Certificado de Assinatura **SAML,** selecione **Descarregamento** para descarregar o **Metdata XML** da Federação das opções dadas e guarde-o no seu computador.

   ![A opção de descarregamento de metadados XML da Federação](common/metadataxml.png)

1. No painel **De Configuração uberflip,** copie o URL ou URLs de que necessita:

   * **Login URL**
   * **Identificador de Azure Ad**
   * **Logout URL**

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>Configurar uberflip único sign-on

Para configurar um único sinal no lado da Uberflip, é necessário enviar os Metadados XML da Federação descarregados e os URLs copiados apropriados do portal Azure para a equipa de suporte da [Uberflip.](mailto:support@uberflip.com) A equipa uberflip certificar-se-á de que a ligação SSO SAML está corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, cria-se um utilizador de teste chamado B. Simon no portal Azure.

1. No portal Azure, no painel esquerdo, selecione **Utilizadores do Diretório Ativo Azure**  >    >  **Todos os utilizadores**.

    ![As opções dos Utilizadores e "Todos os utilizadores"](common/users.png)

1. Na parte superior do ecrã, selecione **+ Novo utilizador**.

    ![Nova opção de utilizador](common/new-user.png)

1. No painel de **utilização,** faça os seguintes passos:

    ![O painel de utilizador](common/user-properties.png)

    1. Na caixa **Nome,** **insira BSimon**.
  
    1. Na caixa **de nome do utilizador,** **insira bSimon \@ \<yourcompanydomain> . \<extension>** Por exemplo, **a BSimon \@ contoso.com**.

    1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que B. Simon use a Azure single sign-on, concedendo o seu acesso à Uberflip.

1. No portal Azure, selecione **Aplicações Empresariais**  >  **Todas as aplicações**  >  **Uberflip**.

    ![Painel de aplicações da empresa](common/enterprise-applications.png)

1. Na lista de candidaturas, selecione **Uberflip**.

    ![Uberflip na lista de candidaturas](common/all-applications.png)

1. No painel esquerdo, em **MANAGE,** **selecione Utilizadores e grupos**.

    ![A opção "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione + Adicione o utilizador** e, em seguida, selecione **Utilizadores e grupos** no painel **de atribuição de adicionar.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

1. No painel **de Utilizadores e grupos,** selecione **B Simon** na lista **de Utilizadores** e, em seguida, escolha **Selecione** na parte inferior do painel.

1. Se está à espera de um valor de função na afirmação SAML, então no painel **'Escolha' de Função,** selecione a função adequada para o utilizador da lista. Na parte inferior do painel, escolha **Select**.

1. No painel **de atribuição de adição,** selecione **Atribuir**.

### <a name="create-an-uberflip-test-user"></a>Criar um utilizador de teste Uberflip

Um utilizador chamado B. Simon é agora criado na Uberflip. Não tens de fazer nada para criar este utilizador. A Uberflip suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Se um utilizador chamado B. Simon já não existir na Uberflip, um novo é criado após a autenticação.

> [!NOTE]
> Se precisar de criar um utilizador manualmente, contacte a equipa de suporte da [Uberflip](mailto:support@uberflip.com).

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal de Azure AD utilizando o portal My Apps.

Quando selecionar o **Uberflip** no portal My Apps, deverá ser automaticamente inscrito na subscrição da Uberflip para a qual configura uma única s inscrição. Para obter mais informações sobre o portal My Apps, consulte [o Access e utilize aplicações no portal My Apps.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais para integrar aplicações saaS com diretório ativo Azure](./tutorial-list.md)

* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

* [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)