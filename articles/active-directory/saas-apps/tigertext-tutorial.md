---
title: 'Tutorial: Integração de Diretório Ativo Azure com TigerText Secure Messenger [ Messenger Seguro] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o TigerText Secure Messenger.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03f1e128-5bcb-4e49-b6a3-fe22eedc6d5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: ea3bda1dd51a7c3a2e5e3f8b669d7138898f1595
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67088662"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Tutorial: Integração de Diretório Ativo Azure com TigerText Secure Messenger

Neste tutorial, aprende-se a integrar o TigerText Secure Messenger com o Azure Ative Directory (Azure AD).

Integrar o TigerText Secure Messenger com o Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a TigerText Secure Messenger.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no TigerText Secure Messenger (único sinal) com as suas contas Azure AD.
* Pode gerir as suas contas num local central: o portal Azure.

Para mais detalhes sobre o software como uma integração de aplicações de serviço (SaaS) com a Azure AD, veja [o que é o acesso à aplicação e um único sign-on com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o TigerText Secure Messenger, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma subscrição TigerText Secure Messenger com um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o single sign-on Azure AD num ambiente de teste e integra o TigerText Secure Messenger com o Azure AD.

TigerText Secure Messenger suporta um único sign-on iniciado por SP (SSO).

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>Adicione o TigerText Secure Messenger do Mercado Azure

Para configurar a integração do TigerText Secure Messenger em Azure AD, precisa adicionar tigerText Secure Messenger do Azure Marketplace à sua lista de aplicações geridas saaS:

1. Inicie sessão no [portal do Azure](https://portal.azure.com?azure-portal=true).
1. No painel esquerdo, selecione **Azure Active Directory**.

    ![A opção Azure Ative Diretório](common/select-azuread.png)

1. Vá a **Aplicações Empresariais**e, em seguida, selecione **Todas as Aplicações**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

1. Para adicionar uma nova aplicação, selecione **+ Nova aplicação** na parte superior do painel.

    ![A nova opção de candidatura](common/add-new-app.png)

1. Na caixa de pesquisa, introduza **o TigerText Secure Messenger**. Nos resultados da pesquisa, selecione **TigerText Secure Messenger**, e, em seguida, selecione **Adicionar** para adicionar a aplicação.

    ![TigerText Secure Messenger na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com tigerText Secure Messenger com base num utilizador de teste chamado **Britta Simon**. Para que o início de sessão seja introduzido, deve estabelecer uma ligação entre um utilizador da AD Azure e o utilizador relacionado no TigerText Secure Messenger.

Para configurar e testar o único sinal de Azure AD com o TigerText Secure Messenger, é necessário completar os seguintes blocos de construção:

1. **[Configure](#configure-azure-ad-single-sign-on)** o único sinal de ad do Azure para permitir que os seus utilizadores utilizem esta funcionalidade.
1. **[Configure](#configure-tigertext-secure-messenger-single-sign-on)** o tigerText Secure Messenger único para configurar as definições de início de sessão individuais no lado da aplicação.
1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sign-on da Azure AD com britta Simon.
1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que Britta Simon utilize um único sinal de AD Azure.
1. **[Crie um utilizador](#create-a-tigertext-secure-messenger-test-user)** de teste TigerText Secure Messenger para que haja um utilizador chamado Britta Simon no TigerText Secure Messenger que esteja ligado ao utilizador da AD Azure chamado Britta Simon.
1. **[Teste um único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on da Azure AD com o TigerText Secure Messenger, tome os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **TigerText Secure Messenger,** selecione **single sign-on**.

    ![Configure uma única opção de inscrição](common/select-sso.png)

1. No select um único painel de método de **início de sinal,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

1. No **set up Single Sign-On com painel SAML,** selecione **Editar** (o ícone do lápis) para abrir o painel **de configuração SAML básico.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. No painel **de configuração SAML básico,** tome os seguintes passos:

    ![TigerText Secure Messenger domínio e URLs informações únicas de inscrição](common/sp-identifier.png)

    1. No **signo na** caixa URL, introduza um URL:

       `https://home.tigertext.com`

    1. Na caixa **identifiedor (Id** da entidade), digite um URL utilizando o seguinte padrão:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > O valor do **Identificador (ID da Entidade)** não é real. Atualize este valor com o identificador real. Para obter o valor, contacte a equipa de [suporte tigerText Secure Messenger](mailto:prosupport@tigertext.com). Também pode consultar os padrões mostrados no painel de **configuração SAML Básico** no portal Azure.

1. No **set set Single Sign-On com painel SAML,** na secção Certificado de **Assinatura SAML,** selecione **Download** para descarregar o **Federation Metadata XML** das opções dadas e guarde-o no seu computador.

    ![A opção de descarregamento de Metadados Da Federação XML](common/metadataxml.png)

1. Na secção **'TigerText Secure Messenger',** copie o URL ou URLs de que necessita:

   * **Login URL**
   * **Identificador Azure AD**
   * **Logout URL**

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>Configure TigerText Secure Messenger single sign-on

Para configurar um único sinal no lado tigerText Secure Messenger, você precisa enviar os metadados da Federação XML descarregados e os URLs copiados apropriados do portal Azure para a equipa de [suporte TigerText Secure Messenger](mailto:prosupport@tigertext.com). A equipa TigerText Secure Messenger certificar-se-á de que a ligação SAML SSO está corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, cria-se uma utilizadora de teste chamada Britta Simon no portal Azure.

1. No portal Azure, no painel esquerdo,**Users** > selecione Utilizadores de **Diretório**   > Ativo Azure**Todos os utilizadores**.

    ![As opções de Utilizadores e "Todos os Utilizadores"](common/users.png)

1. Na parte superior do ecrã, selecione **+ Novo utilizador**.

    ![Nova opção de utilizador](common/new-user.png)

1. No painel **do Utilizador,** faça os seguintes passos:

    ![O painel do Utilizador](common/user-properties.png)

    1. Na caixa **de nomes,** entre **brittaSimon.**
  
    1. Na caixa de **nome suster,** **introduza a BrittaSimon\@\<\< yourcompanydomain>.>de extensão. ** Por exemplo, **brittaSimon\@contoso.com.**

    1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo-lhes acesso ao TigerText Secure Messenger.

1. No portal Azure, selecione **aplicações** > Enterprise**Todas as aplicações** > **TigerText Secure Messenger**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

1. Na lista de aplicações, selecione **TigerText Secure Messenger**.

    ![TigerText Secure Messenger na lista de aplicações](common/all-applications.png)

1. No painel esquerdo, em **MANAGE**, selecione **Utilizadores e grupos**.

    ![A opção "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **+ Adicionar utilizador**, e, em seguida, selecione Utilizadores e **grupos** no painel **de atribuição de adicionar.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

1. No painel **de utilizadores e grupos,** selecione **Britta Simon** na lista de **Utilizadores** e, em seguida, escolha **Selecionar** na parte inferior do painel.

1. Se estiver à espera de um valor de papel na afirmação do SAML, então no painel **Select Role,** selecione a função adequada para o utilizador da lista. Na parte inferior do painel, escolha **Select**.

1. No painel **adicionar atribuição,** selecione **Atribuir**.

### <a name="create-a-tigertext-secure-messenger-test-user"></a>Criar um utilizador de teste TigerText Secure Messenger

Nesta secção, cria-se um utilizador chamado Britta Simon no TigerText Secure Messenger. Trabalhe com a equipa de [suporte tigerText Secure Messenger](mailto:prosupport@tigertext.com) para adicionar Britta Simon como utilizadora no TigerText Secure Messenger. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal de Acesso AD Azure utilizando o portal My Apps.

Ao selecionar **o TigerText Secure Messenger** no portal My Apps, deve ser automaticamente inscrito na subscrição TigerText Secure Messenger para a qual configura um único sinal. Para mais informações sobre o portal My Apps, consulte [o Access e utilize aplicações no portal My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais para integração de apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

* [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
