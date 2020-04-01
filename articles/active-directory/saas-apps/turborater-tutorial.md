---
title: 'Tutorial: Integração do Diretório Ativo Azure com o TurboRater Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o TurboRater.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: abb116b8-8024-4cc6-bc81-f32ef490ea17
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/8/2019
ms.author: jeedes
ms.openlocfilehash: 3777cf09ec669fe3df6bca13f6960f53c689767c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67088279"
---
# <a name="tutorial-azure-active-directory-integration-with-turborater"></a>Tutorial: Integração do Diretório Ativo Azure com o TurboRater

Neste tutorial, aprende-se a integrar o TurboRater com o Azure Ative Directory (Azure AD).

Integrar o TurboRater com o Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a TurboRater.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no TurboRater (único sinal) com as suas contas Azure AD.
* Pode gerir as suas contas num local central: o portal Azure.

Para mais detalhes sobre o software como uma integração de aplicações de serviço (SaaS) com a Azure AD, veja [o que é o acesso à aplicação e um único sign-on com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o TurboRater, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma subscrição TurboRater com um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

TurboRater suporta um único sinal iniciado por IDP (SSO).

## <a name="add-turborater-from-the-azure-marketplace"></a>Adicione TurboRater do Mercado Azure

Para configurar a integração do TurboRater em Azure AD, você precisa adicionar TurboRater do Azure Marketplace à sua lista de aplicações geridas saaS:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com?azure-portal=true).
1. No painel esquerdo, selecione **Azure Active Directory**.

    ![A opção Azure Ative Diretório](common/select-azuread.png)

1. Vá a **Aplicações Empresariais**e, em seguida, selecione **Todas as Aplicações**.

    ![A opção de aplicações da Enterprise](common/enterprise-applications.png)

1. Para adicionar uma nova aplicação, selecione **+ Nova aplicação** na parte superior do painel.

    ![A nova opção de candidatura](common/add-new-app.png)

1. Na caixa de pesquisa, introduza **TurboRater**. Nos resultados da pesquisa, selecione **TurboRater**, e, em seguida, selecione **Adicionar** para adicionar a aplicação.

    ![TurboRater na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com turboRater com base num utilizador de teste chamado **B Simon**. Para que o início de sessão seja introduzido, deve estabelecer uma ligação entre um utilizador da AD Azure e o utilizador relacionado em TurboRater.

Para configurar e testar o único sinal de Azure AD com o TurboRater, é necessário completar os seguintes blocos de construção:

1. **[Configure](#configure-azure-ad-single-sign-on)** o único sinal de ad do Azure para permitir que os seus utilizadores utilizem esta funcionalidade.
1. **[Configure o turboRater único sinal para](#configure-turborater-single-sign-on)** configurar as definições de inscrição únicas no lado da aplicação.
1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de Azure AD com B. Simon.
1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b. Simon utilize um único sinal de AD Azure.
1. **[Crie um utilizador de teste TurboRater](#create-a-turborater-test-user)** para que haja um utilizador chamado B. Simon em TurboRater que esteja ligado ao utilizador da AD Azure chamado B. Simon.
1. **[Teste um único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sinal de Azure AD com o TurboRater, tome os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **TurboRater,** selecione **Single sign-on**.

    ![Configure uma única opção de inscrição](common/select-sso.png)

1. No select um único painel de método de **início de sinal,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

1. No **set single sign-on com** a página SAML, selecione **Editar** (o ícone do lápis) para abrir o painel **de configuração SAML Básico.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. No painel **de configuração SAML básico,** faça os seguintes passos:

    ![TurboRater domínio e URLs informações únicas de inscrição](common/idp-intiated.png)

    1. Na caixa **identifiedor (Id** da entidade), introduza um URL:

       `https://www.itcdataservices.com`

    1. Na caixa **DERESPOSTA URL (URL do Serviço de Consumidor de Afirmação),** introduza um URL utilizando o seguinte padrão:

       | Ambiente | do IdP |
       | ---------------| --------------- |
       | Test  | `https://ratingqa.itcdataservices.com/webservices/imp/saml/login` |
       | Em direto  | `https://www.itcratingservices.com/webservices/imp/saml/login` |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real e responda URL. Para obter estes valores, contacte a equipa de [suporte turboRater.](https://www.getitc.com/support) Também pode consultar os padrões mostrados no painel de **configuração SAML Básico** no portal Azure.

1. No **set set Single Sign-On com painel SAML,** na secção Certificado de **Assinatura SAML,** selecione **Download** para descarregar o **Federation Metadata XML** das opções dadas e guarde-o no seu computador.

    ![A opção de descarregamento de Metadados Da Federação XML](common/metadataxml.png)

1. Na secção **DeConfiguração turboRater,** copie os URL ou URLs de que necessita:

   * **Login URL**
   * **Identificador Azure AD**
   * **Logout URL**

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-turborater-single-sign-on"></a>Configure TurboRater único sign-on

Para configurar um único sinal no lado do TurboRater, é necessário enviar os Metadados da Federação XML descarregados e os URLs copiados apropriados do portal Azure para a equipa de [suporte turborater](https://www.getitc.com/support). A equipa TurboRater certificar-se-á de que a ligação SAML SSO está corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, cria-se uma utilizadora de teste chamada Britta Simon no portal Azure.

1. No portal Azure, no painel esquerdo,**Users** > selecione Utilizadores de **Diretório**   > Ativo Azure**Todos os utilizadores**.

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

Nesta secção, permite que B. Simon utilize um único sign-on Azure, concedendo o seu acesso ao TurboRater.

1. No portal Azure, selecione **aplicações** > Enterprise**Todas as aplicações** > **TurboRater**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

1. Na lista de aplicações, selecione **TurboRater**.

    ![TurboRater na lista de aplicações](common/all-applications.png)

1. No painel esquerdo, em **MANAGE**, selecione **Utilizadores e grupos**.

    ![A opção "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **+ Adicionar utilizador**, e, em seguida, selecione Utilizadores e **grupos** no painel **de atribuição de adicionar.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

1. No painel **de utilizadores e grupos,** selecione **B. Simon** na lista de **Utilizadores** e, em seguida, escolha **Selecionar** na parte inferior do painel.

1. Se estiver à espera de um valor de papel na afirmação do SAML, então no painel **Select Role,** selecione a função adequada para o utilizador da lista. Na parte inferior do painel, escolha **Select**.

1. No painel **adicionar atribuição,** selecione **Atribuir**.

### <a name="create-a-turborater-test-user"></a>Criar um utilizador de teste TurboRater

Nesta secção, cria-se um utilizador chamado B. Simon em TurboRater. Trabalhe com a equipa de [suporte turboRater](https://www.getitc.com/support) para adicionar B. Simon como utilizador no TurboRater. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal de Acesso AD Azure utilizando o portal My Apps.

Ao selecionar **turboRater** no portal My Apps, deve ser automaticamente inscrito na subscrição TurboRater para a qual configura uma única inscrição. Para mais informações sobre o portal My Apps, consulte [o Access e utilize aplicações no portal My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais para integração de aplicações saaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

* [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
