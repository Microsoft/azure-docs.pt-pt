---
title: 'Tutorial: Integração do Active Directory do Azure com Uberflip | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Uberflip.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 754b1f5b-6694-4fd6-9e1e-9fad769c64db
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: fb55840a3423f32d2d6d6d2531628ae4361a0cc3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67088214"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>Tutorial: Integração do Active Directory do Azure com Uberflip

Neste tutorial, saiba como integrar Uberflip com o Azure Active Directory (Azure AD).

Integrar Uberflip no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Uberflip.
* Pode permitir que os utilizadores ter automaticamente sessão iniciada no Uberflip (início de sessão único) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central: portal do Azure.

Para obter detalhes sobre o software como uma integração de aplicação de serviço (SaaS) com o Azure AD, consulte [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Uberflip, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma subscrição de Uberflip com início de sessão único ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

Uberflip suporta as seguintes funcionalidades:

* Iniciado por SP e iniciado o IDP início de sessão único (SSO).
* Aprovisionamento de utilizadores de just-in-time.

## <a name="add-uberflip-from-the-azure-marketplace"></a>Adicionar Uberflip no Azure Marketplace

Para configurar a integração do Uberflip com o Azure AD, terá de adicionar Uberflip no Azure Marketplace à sua lista de aplicações de SaaS geridas:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No painel esquerdo, selecione **Azure Active Directory**.

   ![A opção do Azure Active Directory](common/select-azuread.png)

1. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

   ![O painel de aplicações empresariais](common/enterprise-applications.png)

1. Para adicionar uma nova aplicação, selecione **+ nova aplicação** na parte superior do painel.

   ![A nova opção de aplicação](common/add-new-app.png)

1. Na caixa de pesquisa, introduza **Uberflip**. Nos resultados da pesquisa, selecione **Uberflip**e, em seguida, selecione **Add** para adicionar a aplicação.

   ![Uberflip na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Uberflip com base num utilizador de teste com o nome **B Simon**. Para início de sessão único funcione, tem de estabelecer uma ligação entre um utilizador do Azure AD e um utilizador relacionado no Uberflip.

Para configurar e testar o Azure AD início de sessão único com Uberflip, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  para permitir aos utilizadores utilizar esta funcionalidade.
1. **[Configurar Uberflip início de sessão único](#configure-uberflip-single-sign-on)**  para configurar as definições de início de sessão únicas no lado do aplicativo.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com Simon B.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar a Simon B. utilizar o Azure AD início de sessão único.
1. **[Criar um utilizador de teste Uberflip](#create-an-uberflip-test-user)**  para que haja um utilizador nomeado Simon B. Uberflip que está ligada ao utilizador do Azure AD com o nome Simon B.
1. **[Testar início de sessão único](#test-single-sign-on)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Uberflip, siga os passos seguintes:

1. Na [portal do Azure](https://portal.azure.com/), na **Uberflip** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a opção de início de sessão único](common/select-sso.png)

1. Na **selecionar um método de início de sessão único** painel, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

1. Na **definir a segurança de início de sessão único com o SAML** painel, selecione **editar** (o ícone de lápis) para abrir o **configuração básica de SAML** painel.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** painel, efetue um dos seguintes passos, dependendo do modo SSO que pretende configurar:

   * Para configurar a aplicação no modo SSO iniciado por IDP, a **URL de resposta (URL do serviço de consumidor de asserção)** , introduza um URL ao utilizar o seguinte padrão:

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![Informações de início de sessão de único Uberflip domínio e URLs](common/both-replyurl.png)

     > [!NOTE]
     > Este valor não é real. Atualize este valor com o URL de resposta real. Para obter o valor real, entre em contato com o [equipa de suporte de Uberflip](mailto:support@uberflip.com). Também pode consultar os padrões mostrados a **configuração básica de SAML** painel no portal do Azure.

   * Para configurar a aplicação no modo SSO iniciado por SP, selecione **definir URLs adicionais**e, no **URL de início de sessão** , introduza este URL:

     `https://app.uberflip.com/users/login`

     ![Informações de início de sessão de único Uberflip domínio e URLs](common/both-signonurl.png)

1. Na **definir a segurança de início de sessão único com o SAML** painel, na **certificado de assinatura SAML** secção, selecione **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado e guarde-o no seu computador.

   ![A opção de download do XML de metadados de Federação](common/metadataxml.png)

1. Na **configurar Uberflip** painel, copie o URL ou URLS que tem de:

   * **URL de início de sessão**
   * **Azure AD Identifier**
   * **URL de fim de sessão**

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>Configurar Uberflip início de sessão único

Para configurar o início de sessão único no lado do Uberflip, terá de enviar o XML de metadados de Federação transferidos e copiados apropriado URLs a partir do portal do Azure para o [equipa de suporte de Uberflip](mailto:support@uberflip.com). A equipe de Uberflip será Certifique-se de que a ligação de SAML SSO está definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, vai criar um utilizador de teste com o nome Simon B. no portal do Azure.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory** > **utilizadores** > **todos os utilizadores**.

    ![Os utilizadores e de "Todos os utilizadores" Opções](common/users.png)

1. Na parte superior do ecrã, selecione **+ novo utilizador**.

    ![Nova opção de utilizador](common/new-user.png)

1. Na **utilizador** painel, siga os passos abaixo:

    ![O painel do utilizador](common/user-properties.png)

    1. Na **Name** , introduza **BSimon**.
  
    1. Na **nome de utilizador** , introduza **BSimon\@\<yourcompanydomain >.\< extensão >** . Por exemplo, **BSimon\@contoso.com**.

    1. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Simon B. utilizar o Azure início de sessão único ao conceder o acesso para Uberflip.

1. No portal do Azure, selecione **aplicações empresariais** > **todas as aplicações** > **Uberflip**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

1. Na lista de aplicações, selecione **Uberflip**.

    ![Uberflip na lista de aplicações](common/all-applications.png)

1. No painel esquerdo, sob **MANAGE**, selecione **utilizadores e grupos**.

    ![A opção "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **+ adicionar utilizador**e, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** painel.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

1. No **utilizadores e grupos** painel, selecione **B Simon** no **utilizadores** lista e, em seguida, escolha **selecione** na parte inferior do painel.

1. Se estiver à espera de um valor de função na asserção de SAML, em seguida, no **selecionar função** painel, selecione a função adequada para o utilizador a partir da lista. Na parte inferior do painel, escolha **selecione**.

1. Na **adicionar atribuição** painel, selecione **atribuir**.

### <a name="create-an-uberflip-test-user"></a>Criar um utilizador de teste Uberflip

Um usuário chamado Simon B. agora é criado no Uberflip. Não tem de fazer nada para criar este utilizador. Uberflip suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Se um usuário chamado Simon B. já não existir no Uberflip, é criado um novo após a autenticação.

> [!NOTE]
> Se precisar de criar manualmente um utilizador, entre em contato com o [equipa de suporte de Uberflip](mailto:support@uberflip.com).

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar seu única início de sessão em configuração do Azure AD com o portal as minhas aplicações.

Quando seleciona **Uberflip** no portal as minhas aplicações, deve estar automaticamente conectado à subscrição Uberflip para o qual configura o início de sessão único. Para obter mais informações sobre o portal as minhas aplicações, consulte [aplicações de acesso e a utilização no portal minhas aplicações](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais para integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

* [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
