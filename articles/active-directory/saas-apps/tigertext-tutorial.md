---
title: 'Tutorial: Integração do Azure Ative Directory com TigerText Secure Messenger Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o TigerText Secure Messenger.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 9dff60767e923bad1322b689acd98e69eb9c2ac6
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516998"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Tutorial: Integração do Diretório Ativo Azure com TigerText Secure Messenger

Neste tutorial, você aprende a integrar TigerText Secure Messenger com Azure Ative Directory (Azure AD).

A integração do TigerText Secure Messenger com a Azure AD proporciona-lhe os seguintes benefícios:

* Podes controlar em Azure AD que tem acesso ao TigerText Secure Messenger.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no TigerText Secure Messenger (único súmido) com as suas contas AD Azure.
* Pode gerir as suas contas num local central: o portal Azure.

Para mais detalhes sobre o software como uma integração de aplicações de serviço (SaaS) com a Azure AD, consulte [o que é o acesso à aplicação e um único acesso com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o TigerText Secure Messenger, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma subscrição tigerText Secure Messenger com um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa a Azure AD um único sign-on em um ambiente de teste e integrar TigerText Secure Messenger com Azure AD.

TigerText Secure Messenger suporta um único sinal iniciado pelo SP (SSO).

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>Adicione TigerText Secure Messenger do Azure Marketplace

Para configurar a integração do TigerText Secure Messenger em AD Azure, precisa adicionar TigerText Secure Messenger do Azure Marketplace à sua lista de aplicações geridas para o SaaS:

1. Inicie sessão no [portal do Azure](https://portal.azure.com?azure-portal=true).
1. No painel esquerdo, selecione **Azure Active Directory**.

    ![A opção Azure Ative Directory](common/select-azuread.png)

1. Vá a **Aplicações Empresariais**e, em seguida, selecione **Todas as Aplicações**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

1. Para adicionar uma nova aplicação, selecione **+ Nova aplicação** na parte superior do painel.

    ![A nova opção de aplicação](common/add-new-app.png)

1. Na caixa de pesquisa, insira **o TigerText Secure Messenger**. Nos resultados da pesquisa, selecione **TigerText Secure Messenger**e, em seguida, selecione **Adicionar** para adicionar a aplicação.

    ![TigerText Secure Messenger na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com TigerText Secure Messenger com base em um utilizador de teste chamado **Britta Simon**. Para um único sinal de sação a funcionar, deve estabelecer uma ligação entre um utilizador Azure AD e o utilizador relacionado no TigerText Secure Messenger.

Para configurar e testar o Azure AD com o TigerText Secure Messenger, é necessário completar os seguintes blocos de construção:

1. **[Configurar o Azure AD um único sinal para](#configure-azure-ad-single-sign-on)** permitir que os seus utilizadores utilizem esta funcionalidade.
1. **[Configure o TigerText Secure Messenger para](#configure-tigertext-secure-messenger-single-sign-on)** configurar as definições de inscrição única no lado da aplicação.
1. **[Crie um utilizador de teste AZure AD](#create-an-azure-ad-test-user)** para testar o Azure AD com Britta Simon.
1. **[Atribua ao utilizador de teste AZure AD](#assign-the-azure-ad-test-user)** para permitir que a Britta Simon utilize um único sinal de Ad AD.
1. **[Crie um utilizador de teste TigerText Secure Messenger](#create-a-tigertext-secure-messenger-test-user)** para que haja um utilizador chamado Britta Simon em TigerText Secure Messenger que está ligado ao utilizador Azure AD chamado Britta Simon.
1. **[Teste um único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com TigerText Secure Messenger, tome os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **TigerText Secure Messenger,** selecione **Single sign-on**.

    ![Configurar a opção de inscrição única](common/select-sso.png)

1. No painel **de método de inscrição,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súm.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

1. No **set up Single Sign-On com painel SAML,** selecione **Editar** (o ícone do lápis) para abrir o painel **de configuração DE SAML básico.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. No painel **de configuração básico do SAML,** tome os seguintes passos:

    ![TigerText Secure Messenger e informações de assinatura única](common/sp-identifier.png)

    1. Na **caixa de sinalização na** url, introduza um URL:

       `https://home.tigertext.com`

    1. Na caixa **identifier (Entity ID),** digite um URL utilizando o seguinte padrão:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > O valor **do Identificador (ID da Entidade)** não é real. Atualize este valor com o identificador real. Para obter o valor, contacte a [equipa de apoio TigerText Secure Messenger](mailto:prosupport@tigertext.com). Também pode consultar os padrões indicados no painel **de configuração SAML básico** no portal Azure.

1. Na **configuração de um único sign-on com painel SAML,** na secção **certificado de assinatura SAML,** selecione **Descarregue** para descarregar o **Metdata XML** da Federação das opções dadas e guarde-o no seu computador.

    ![A opção de descarregamento de metadados XML da Federação](common/metadataxml.png)

1. Na secção **Configurar TigerText Secure Messenger,** copie o URL ou URLs de que necessita:

   * **Login URL**
   * **Identificador de Azure Ad**
   * **Logout URL**

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>Configurar tigerText Secure Messenger single sign-on

Para configurar um único sinal no lado TigerText Secure Messenger, é necessário enviar os Metadados XML da Federação descarregados e os URLs copiados apropriados do portal Azure para a equipa de [suporte TigerText Secure Messenger](mailto:prosupport@tigertext.com). A equipa TigerText Secure Messenger certificar-se-á de que a ligação SSO SAML está corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, cria-se um utilizador de teste chamado Britta Simon no portal Azure.

1. No portal Azure, no painel esquerdo, selecione **Utilizadores do Diretório Ativo Azure**    >  **Users**  >  **Todos os utilizadores**.

    ![As opções dos Utilizadores e "Todos os utilizadores"](common/users.png)

1. Na parte superior do ecrã, selecione **+ Novo utilizador**.

    ![Nova opção de utilizador](common/new-user.png)

1. No painel de **utilização,** faça os seguintes passos:

    ![O painel de utilizador](common/user-properties.png)

    1. Na caixa **Nome,** **insira BrittaSimon**.
  
    1. Na caixa **de nome do utilizador,** insira **BrittaSimon \@ \<yourcompanydomain> . . \<extension> ** Por exemplo, **brittaSimon \@ contoso.com**.

    1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo-lhes acesso ao TigerText Secure Messenger.

1. No portal Azure, selecione **aplicações Enterprise**  >  **Todas as aplicações**  >  **TigerText Secure Messenger**.

    ![Painel de aplicações da empresa](common/enterprise-applications.png)

1. Na lista de aplicações, selecione **TigerText Secure Messenger**.

    ![TigerText Secure Messenger na lista de aplicações](common/all-applications.png)

1. No painel esquerdo, em **MANAGE,** **selecione Utilizadores e grupos**.

    ![A opção "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione + Adicione o utilizador**e, em seguida, selecione **Utilizadores e grupos** no painel **de atribuição de adicionar.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

1. No painel **de Utilizadores e grupos,** selecione **Britta Simon** na lista **de Utilizadores** e, em seguida, escolha **Selecione** na parte inferior do painel.

1. Se está à espera de um valor de função na afirmação SAML, então no painel **'Escolha' de Função,** selecione a função adequada para o utilizador da lista. Na parte inferior do painel, escolha **Select**.

1. No painel **de atribuição de adição,** selecione **Atribuir**.

### <a name="create-a-tigertext-secure-messenger-test-user"></a>Criar um utilizador de teste TigerText Secure Messenger

Nesta secção, cria-se um utilizador chamado Britta Simon em TigerText Secure Messenger. Trabalhe com a [equipa de suporte TigerText Secure Messenger](mailto:prosupport@tigertext.com) para adicionar Britta Simon como utilizador no TigerText Secure Messenger. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal de Azure AD utilizando o portal My Apps.

Quando selecionar **TigerText Secure Messenger** no portal My Apps, deverá ser automaticamente inscrito na subscrição TigerText Secure Messenger para a qual configura uma única s inscrição. Para obter mais informações sobre o portal My Apps, consulte [o Access e utilize aplicações no portal My Apps.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais para integrar apps saaS com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

* [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
