---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Página DNA [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o PageDNA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c8765864-45f4-48c2-9d86-986a4aa431e4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7f8c8efcad0a07a3d3a56925866b10d94f82ed
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "68227467"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>Tutorial: Integração do Diretório Ativo Azure com o PageDNA

Neste tutorial, aprende-se a integrar o PageDNA com o Azure Ative Directory (Azure AD).

Integrar o PageDNA com a AD Azur e proporcionar-lhe os seguintes benefícios:

* Em Azure AD, você pode controlar quem tem acesso ao PageDNA.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no PageDNA (único sinal) com as suas contas Azure AD.
* Pode gerir as suas contas num local central: o portal Azure.

Para mais detalhes sobre o software como uma integração de aplicações de serviço (SaaS) com a Azure AD, veja [o que é o acesso à aplicação e um único sign-on com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o PageDNA, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma subscrição de PageDNA com um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sign-on azure num ambiente de teste e integra o PageDNA com a AD Azure.

O PageDNA suporta as seguintes funcionalidades:

* SP-iniciado único sign-on (SSO).

* Provisões de utilizadores just-in-time.

## <a name="add-pagedna-from-the-azure-marketplace"></a>Adicionar PageDNA do Azure Marketplace

Para configurar a integração do PageDNA em Azure AD, precisa adicionar o PageDNA do Azure Marketplace à sua lista de aplicações geridas do SaaS:

1. Inicie sessão no [portal do Azure](https://portal.azure.com?azure-portal=true).
1. No painel esquerdo, selecione **Azure Active Directory**.

    ![A opção Azure Ative Diretório](common/select-azuread.png)

1. Vá a **Aplicações Empresariais**e, em seguida, selecione **Todas as Aplicações**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

1. Para adicionar uma nova aplicação, selecione **+ Nova aplicação** na parte superior do painel.

    ![A nova opção de candidatura](common/add-new-app.png)

1. Na caixa de pesquisa, introduza o **PageDNA**. Nos resultados da pesquisa, selecione **PageDNA**, e, em seguida, selecione **Adicionar** para adicionar a aplicação.

    ![PageDNA na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sinal de Azure AD com o PageDNA com base num utilizador de teste chamado **Britta Simon**. Para que o início de sessão seja introduzido, deve estabelecer uma ligação entre um utilizador da AD Azure e o utilizador relacionado no PageDNA.

Para configurar e testar o único sinal de Azure AD com o PageDNA, é necessário completar os seguintes blocos de construção:

1. **[Configure](#configure-azure-ad-single-sign-on)** o único sinal de ad do Azure para permitir que os seus utilizadores utilizem esta funcionalidade.
1. **[Configure o único sinal de pagedNA](#configure-pagedna-single-sign-on)** para configurar as definições de inscrição únicas no lado da aplicação.
1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sign-on da Azure AD com britta Simon.
1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que Britta Simon utilize um único sinal de AD Azure.
1. **[Crie um utilizador de teste pageDNA](#create-a-pagedna-test-user)** para que haja uma utilizadora chamada Britta Simon no PageDNA que está ligada ao utilizador da AD Azure chamada Britta Simon.
1. **[Teste um único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sinal de Azure AD com o PageDNA, tome os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **pageDNA,** selecione **Single sign-on**.

    ![Configure uma única opção de inscrição](common/select-sso.png)

1. No select um único painel de método de **início de sinal,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

1. No **set up Single Sign-On com painel SAML,** selecione **Editar** (o ícone do lápis) para abrir o painel **de configuração SAML básico.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. No painel **de configuração SAML básico,** tome os seguintes passos:

    ![Domínio pagedNA e URLs informação única de inscrição](common/sp-identifier.png)

    1. Na caixa **de URL Sign on,** introduza um URL utilizando um dos seguintes padrões:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>` |
        | `https://<your domain>` |
        | `https://<your domain>/<your site>` |
        | `https://www.nationsprint.com/<your site>` |
        | |

    1. Na caixa **Identifier (Id** da Entidade), introduza um URL utilizando um dos seguintes padrões:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>/saml2ep.cgi` |
        | `https://www.nationsprint.com/<your site>/saml2ep.cgi` |
        | |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL e identificador de inscrição real. Para obter estes valores, contacte a equipa de [suporte pageDNA](mailto:success@pagedna.com). Também pode consultar os padrões mostrados no painel de **configuração SAML Básico** no portal Azure.

1. No **set up Single Sign-On com painel SAML,** na secção Certificado de **Assinatura SAML,** selecione **Download** para descarregar **Certificado (Raw)** das opções dadas e guarde-o no seu computador.

    ![Opção de descarregamento de Certificado (Raw)](common/certificateraw.png)

1. Na secção Configurar o **PageDNA,** copie os URL ou URLs de que necessita:

   * **Login URL**
   * **Identificador Azure AD**
   * **Logout URL**

    ![Copiar os URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>Configure PageDNA single sign-on

Para configurar um único sinal no lado do PageDNA, envie o Certificado descarregado (Raw) e os URLs copiados apropriados do portal Azure para a equipa de [suporte pageDNA](mailto:success@pagedna.com). A equipa pageDNA certificar-se-á de que a ligação SAML SSO está corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, cria-se um utilizador de teste no portal Azure chamado Britta Simon.

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

Nesta secção, permite que a utilizadora Britta Simon utilize um único sinal de Acesso Azure, concedendo ao utilizador acesso ao PageDNA.

1. No portal Azure, selecione **aplicações** > Enterprise**Todas as aplicações** > **PageDNA**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

1. Na lista de aplicações, selecione **PageDNA**.

    ![PageDNA na lista de aplicações](common/all-applications.png)

1. No painel esquerdo, em **MANAGE**, selecione **Utilizadores e grupos**.

    ![A opção "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **+ Adicionar utilizador**, e, em seguida, selecione Utilizadores e **grupos** no painel **de atribuição de adicionar.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

1. No painel **de utilizadores e grupos,** selecione **Britta Simon** na lista de **Utilizadores** e, em seguida, escolha **Selecionar** na parte inferior do painel.

1. Se estiver à espera de um valor de papel na afirmação do SAML, então no painel **Select Role,** selecione a função adequada para o utilizador da lista. Na parte inferior do painel, escolha **Select**.

1. No painel **adicionar atribuição,** selecione **Atribuir**.

### <a name="create-a-pagedna-test-user"></a>Criar um utilizador de teste PageDNA

Uma utilizadora chamada Britta Simon é agora criada no PageDNA. Não tens de fazer nada para criar este utilizador. O PageDNA suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Se um utilizador chamado Britta Simon já não existir no PageDNA, um novo é criado após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal de Acesso AD Azure utilizando o portal My Apps.

Ao selecionar o **PageDNA** no portal My Apps, deve ser automaticamente inscrito na subscrição pageDNA para a qual configura um único sinal. Para mais informações sobre o portal My Apps, consulte [o Access e utilize aplicações no portal My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais para integração de aplicações saaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Inscrição única para candidaturas no Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

