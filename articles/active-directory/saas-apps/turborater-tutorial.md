---
title: 'Tutorial: Integração do Diretório Ativo Azure com a | TurboRater Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o TurboRater.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 3/8/2019
ms.author: jeedes
ms.openlocfilehash: 7483e7a9300088d07e85d238ad897522d59c5464
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92519389"
---
# <a name="tutorial-azure-active-directory-integration-with-turborater"></a>Tutorial: Integração do Azure Ative Directory com o TurboRater

Neste tutorial, aprende-se a integrar o TurboRater com o Azure Ative Directory (Azure AD).

A integração do TurboRater com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao TurboRater.
* Pode permitir que os seus utilizadores se inscrevam automaticamente no TurboRater (súpido único) com as suas contas AD AZure.
* Pode gerir as suas contas num local central: o portal Azure.

Para mais detalhes sobre o software como uma integração de aplicações de serviço (SaaS) com a Azure AD, consulte [o que é o acesso à aplicação e um único acesso com o Azure Ative Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o TurboRater, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma assinatura TurboRater com um único sinal ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

TurboRater suporta um único sinal de inscrição (SSO) iniciado pelo IDP.

## <a name="add-turborater-from-the-azure-marketplace"></a>Adicione TurboRater do Azure Marketplace

Para configurar a integração do TurboRater no AD Azure, é necessário adicionar o TurboRater do Azure Marketplace à sua lista de aplicações geridas pela SaaS:

1. Inicie sessão no [portal do Azure](https://portal.azure.com?azure-portal=true).
1. No painel esquerdo, selecione **Azure Active Directory**.

    ![A opção Azure Ative Directory](common/select-azuread.png)

1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.

    ![A opção de aplicações da Enterprise](common/enterprise-applications.png)

1. Para adicionar uma nova aplicação, selecione **+ Nova aplicação** na parte superior do painel.

    ![A nova opção de aplicação](common/add-new-app.png)

1. Na caixa de pesquisa, insira **o TurboRater.** Nos resultados da pesquisa, selecione **TurboRater** e, em seguida, **selecione Adicionar** para adicionar a aplicação.

    ![TurboRater na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o TurboRater com base num utilizador de teste chamado  **B Simon**. Para um único sinal de sação a funcionar, deve estabelecer uma ligação entre um utilizador AD Azure e o utilizador relacionado no TurboRater.

Para configurar e testar o Azure AD com o TurboRater, é necessário completar os seguintes blocos de construção:

1. **[Configurar o Azure AD um único sinal para](#configure-azure-ad-single-sign-on)** permitir que os seus utilizadores utilizem esta funcionalidade.
1. **[Configurar o turbo-on único](#configure-turborater-single-sign-on)** para configurar as definições de inscrição única no lado da aplicação.
1. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com B. Simon.
1. **[Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b. Simon utilize um único sinal de Ad AD.
1. **[Crie um utilizador de teste TurboRater](#create-a-turborater-test-user)** para que haja um utilizador chamado B. Simon no TurboRater que esteja ligado ao utilizador da AD Azure chamado B. Simon.
1. **[Teste um único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o TurboRater, tome os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **TurboRater,** selecione **Single sign-on**.

    ![Configurar a opção de inscrição única](common/select-sso.png)

1. No painel **de método de inscrição,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súm.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

1. Na **configuração single Sign-On com página SAML,** selecione **Editar** (o ícone do lápis) para abrir o painel **de configuração DE SAML básico.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. No painel **de configuração básico do SAML,** faça os seguintes passos:

    ![TurboRater domínio e URLs informações únicas de sinal-on](common/idp-intiated.png)

    1. Na caixa **identifier (Entity ID),** insira um URL:

       `https://www.itcdataservices.com`

    1. Na caixa **URL de resposta (URL de serviço ao consumidor de afirmação),** introduza um URL utilizando o seguinte padrão:

       | Ambiente | URL |
       | ---------------| --------------- |
       | Teste  | `https://ratingqa.itcdataservices.com/webservices/imp/saml/login` |
       | Em direto  | `https://www.itcratingservices.com/webservices/imp/saml/login` |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real e responda URL. Para obter estes valores, contacte a equipa de apoio do [TurboRater.](https://www.getitc.com/support) Também pode consultar os padrões indicados no painel **de configuração SAML básico** no portal Azure.

1. Na **configuração single Sign-On com painel SAML,** na secção Certificado de Assinatura **SAML,** selecione **Descarregamento** para descarregar o **Metdata XML** da Federação das opções dadas e guarde-o no seu computador.

    ![A opção de descarregamento de metadados XML da Federação](common/metadataxml.png)

1. Na secção **Configurar TurboRater,** copie o URL ou URLs de que necessita:

   * **Login URL**
   * **Identificador de Azure Ad**
   * **Logout URL**

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-turborater-single-sign-on"></a>Configurar o turborater single sign-on

Para configurar um único sinal no lado turboRater, é necessário enviar o Metadadata XML da Federação descarregado e os URLs copiados apropriados do portal Azure para a equipa de suporte do [TurboRater.](https://www.getitc.com/support) A equipa TurboRater certificar-se-á de que a ligação SSO SAML está corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, cria-se um utilizador de teste chamado Britta Simon no portal Azure.

1. No portal Azure, no painel esquerdo, selecione **Utilizadores do Diretório Ativo Azure**    >    >  **Todos os utilizadores**.

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

Nesta secção, você permite que B. Simon utilize o Azure single sign-on, concedendo o seu acesso ao TurboRater.

1. No portal Azure, selecione **aplicações Enterprise**  >  **Todas as aplicações**  >  **TurboRater**.

    ![Painel de aplicações da empresa](common/enterprise-applications.png)

1. Na lista de candidaturas, selecione **TurboRater**.

    ![TurboRater na lista de candidaturas](common/all-applications.png)

1. No painel esquerdo, em **MANAGE,** **selecione Utilizadores e grupos**.

    ![A opção "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione + Adicione o utilizador** e, em seguida, selecione **Utilizadores e grupos** no painel **de atribuição de adicionar.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

1. No painel **de Utilizadores e grupos,** selecione **B. Simon** na lista **de Utilizadores** e, em seguida, escolha **Selecione** na parte inferior do painel.

1. Se está à espera de um valor de função na afirmação SAML, então no painel **'Escolha' de Função,** selecione a função adequada para o utilizador da lista. Na parte inferior do painel, escolha **Select**.

1. No painel **de atribuição de adição,** selecione **Atribuir**.

### <a name="create-a-turborater-test-user"></a>Criar um utilizador de teste TurboRater

Nesta secção, cria-se um utilizador chamado B. Simon no TurboRater. Trabalhe com a [equipa de apoio do TurboRater](https://www.getitc.com/support) para adicionar B. Simon como utilizador no TurboRater. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal de Azure AD utilizando o portal My Apps.

Quando selecionar o **TurboRater** no portal My Apps, deverá ser automaticamente inscrito na subscrição do TurboRater para a qual configura uma única súbs. Para obter mais informações sobre o portal My Apps, consulte [o Access e utilize aplicações no portal My Apps.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais para integrar aplicações saaS com diretório ativo Azure](./tutorial-list.md)

* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

* [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)