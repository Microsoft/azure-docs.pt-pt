---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Uberflip Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Uberflip.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 754b1f5b-6694-4fd6-9e1e-9fad769c64db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: d20c05e6ec5a413b81ede9cb4906de2595967115
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80048463"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>Tutorial: Integração do Diretório Ativo Azure com a Uberflip

Neste tutorial, aprende-se a integrar o Uberflip com o Azure Ative Directory (Azure AD).

Integrar o Uberflip com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Uberflip.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Uberflip (único sinal) com as suas contas Azure AD.
* Pode gerir as suas contas num local central: o portal Azure.

Para mais detalhes sobre o software como uma integração de aplicações de serviço (SaaS) com a Azure AD, veja [o que é o acesso à aplicação e um único sign-on com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Uberflip, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma subscrição Uberflip com um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

A Uberflip suporta as seguintes funcionalidades:

* SP iniciado e iniciado por IDP único sign-on (SSO).
* Provisões de utilizadores just-in-time.

## <a name="add-uberflip-from-the-azure-marketplace"></a>Adicione uberflip do Mercado Azure

Para configurar a integração do Uberflip no Azure AD, precisa de adicionar o Uberflip do Azure Marketplace à sua lista de aplicações geridas pelo SaaS:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No painel esquerdo, selecione **Azure Active Directory**.

   ![A opção Azure Ative Diretório](common/select-azuread.png)

1. Vá a **Aplicações Empresariais**e, em seguida, selecione **Todas as Aplicações**.

   ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

1. Para adicionar uma nova aplicação, selecione **+ Nova aplicação** na parte superior do painel.

   ![A nova opção de candidatura](common/add-new-app.png)

1. Na caixa de pesquisa, entre no **Uberflip.** Nos resultados da pesquisa, selecione **Uberflip**, e, em seguida, selecione **Adicionar** para adicionar a aplicação.

   ![Uberflip na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o single sign-on azure com a Uberflip com base num utilizador de teste chamado **B Simon**. Para um único início de sessão funcionar, é necessário estabelecer uma ligação entre um utilizador da AD Azure e um utilizador relacionado no Uberflip.

Para configurar e testar o único sinal de Azure AD com a Uberflip, é necessário completar os seguintes blocos de construção:

1. **[Configure](#configure-azure-ad-single-sign-on)** o único sinal de ad do Azure para permitir que os seus utilizadores utilizem esta funcionalidade.
1. **[Configure](#configure-uberflip-single-sign-on)** o início de inscrição único do Uberflip para configurar as definições de inscrição únicas no lado da aplicação.
1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de Azure AD com B. Simon.
1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b. Simon utilize um único sinal de AD Azure.
1. **[Crie um utilizador](#create-an-uberflip-test-user)** de teste Uberflip para que haja um utilizador chamado B. Simon na Uberflip que esteja ligado ao utilizador da AD Azure chamado B. Simon.
1. **[Teste um único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on da Azure AD com a Uberflip, tome os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **uberflip,** selecione **Single sign-on**.

    ![Configure uma única opção de inscrição](common/select-sso.png)

1. No select um único painel de método de **início de sinal,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

1. No **set up Single Sign-On com painel SAML,** selecione **Editar** (o ícone do lápis) para abrir o painel **de configuração SAML básico.**

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. No painel **de configuração Basic SAML,** faça um dos seguintes passos, dependendo do modo SSO que pretende configurar:

   * Para configurar a aplicação no modo SSO iniciado pelo IDP, na caixa **DEURL de Resposta (URL do Serviço de Consumidor de Afirmação),** introduza um URL utilizando o seguinte padrão:

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![Domínio Uberflip e URLs informações únicas de inscrição](common/both-replyurl.png)

     > [!NOTE]
     > Este valor não é real. Atualize este valor com o URL de resposta real. Para obter o valor real, contacte a equipa de suporte da [Uberflip.](mailto:support@uberflip.com) Também pode consultar os padrões mostrados no painel de **configuração SAML Básico** no portal Azure.

   * Para configurar a aplicação no modo SSO iniciado por SP, selecione **Definir URLs adicionais,** e na caixa **URL sign-on,** introduza este URL:

     `https://app.uberflip.com/users/login`

     ![Domínio Uberflip e URLs informações únicas de inscrição](common/both-signonurl.png)

1. No **set set Single Sign-On com painel SAML,** na secção Certificado de **Assinatura SAML,** selecione **Download** para descarregar o **Federation Metadata XML** das opções dadas e guarde-o no seu computador.

   ![A opção de descarregamento de Metadados Da Federação XML](common/metadataxml.png)

1. No **painel De Configurar Uberflip,** copie os URL ou URLs de que necessita:

   * **Login URL**
   * **Identificador Azure AD**
   * **Logout URL**

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>Configure uberflip único sign-on

Para configurar um único sinal no lado uberflip, você precisa enviar o descarregamento da Federação Metadados XML e os URLs copiados apropriados do portal Azure para a equipa de [suporte Uberflip](mailto:support@uberflip.com). A equipa Uberflip certificar-se-á de que a ligação SAML SSO está corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, cria-se um utilizador de teste chamado B. Simon no portal Azure.

1. No portal Azure, no painel esquerdo,**Users** > selecione Utilizadores de **Diretório** > Ativo Azure**Todos os utilizadores**.

    ![As opções de Utilizadores e "Todos os Utilizadores"](common/users.png)

1. Na parte superior do ecrã, selecione **+ Novo utilizador**.

    ![Nova opção de utilizador](common/new-user.png)

1. No painel **do Utilizador,** faça os seguintes passos:

    ![O painel do Utilizador](common/user-properties.png)

    1. Na caixa **nome,** insira **BSimon**.
  
    1. Na caixa de **nomes do Utilizador,** **introduza o domínio\@\<bSimon da sua empresa>.\<>de extensão. ** Por exemplo, **\@BSimon contoso.com.**

    1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que B. Simon utilize um único sign-on Azure, concedendo o seu acesso ao Uberflip.

1. No portal Azure, selecione **Aplicações Empresariais** > **Todas as aplicações** > **Uberflip**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

1. Na lista de aplicações, selecione **Uberflip**.

    ![Uberflip na lista de aplicações](common/all-applications.png)

1. No painel esquerdo, em **MANAGE**, selecione **Utilizadores e grupos**.

    ![A opção "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **+ Adicionar utilizador**, e, em seguida, selecione Utilizadores e **grupos** no painel **de atribuição de adicionar.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

1. No painel **de utilizadores e grupos,** selecione **B Simon** na lista **de Utilizadores** e, em seguida, escolha **Selecionar** na parte inferior do painel.

1. Se estiver à espera de um valor de papel na afirmação do SAML, então no painel **Select Role,** selecione a função adequada para o utilizador da lista. Na parte inferior do painel, escolha **Select**.

1. No painel **adicionar atribuição,** selecione **Atribuir**.

### <a name="create-an-uberflip-test-user"></a>Criar um utilizador de teste Uberflip

Um utilizador chamado B. Simon é agora criado na Uberflip. Não tens de fazer nada para criar este utilizador. O Uberflip suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Se um utilizador chamado B. Simon já não existir no Uberflip, um novo é criado após a autenticação.

> [!NOTE]
> Se precisar de criar um utilizador manualmente, contacte a equipa de suporte do [Uberflip](mailto:support@uberflip.com).

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal de Acesso AD Azure utilizando o portal My Apps.

Ao selecionar o **Uberflip** no portal My Apps, deverá ser automaticamente inscrito na subscrição uberflip para a qual configura um único início de sessão. Para mais informações sobre o portal My Apps, consulte [o Access e utilize aplicações no portal My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais para integração de aplicações saaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

* [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
