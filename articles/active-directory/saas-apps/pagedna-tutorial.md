---
title: 'Tutorial: Integração do Active Directory do Azure com PageDNA | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e PageDNA.
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
ms.openlocfilehash: 54c3ae22b9cc2e447960b9e3527bbbb0afae3e54
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095101"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>Tutorial: Integração do Active Directory do Azure com PageDNA

Neste tutorial, saiba como integrar PageDNA com o Azure Active Directory (Azure AD).

Integrar PageDNA no Azure AD fornece as seguintes vantagens:

* No Azure AD, pode controlar quem tem acesso a PageDNA.
* Pode permitir que os utilizadores ter automaticamente sessão iniciada no PageDNA (início de sessão único) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central: portal do Azure.

Para obter detalhes sobre o software como uma integração de aplicação de serviço (SaaS) com o Azure AD, consulte [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com PageDNA, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma subscrição de PageDNA com início de sessão único ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste e integrar PageDNA no Azure AD.

PageDNA suporta as seguintes funcionalidades:

* Iniciado por SP início de sessão único (SSO).

* Aprovisionamento de utilizadores de just-in-time.

## <a name="add-pagedna-from-the-azure-marketplace"></a>Adicionar PageDNA no Azure Marketplace

Para configurar a integração do PageDNA com o Azure AD, terá de adicionar PageDNA no Azure Marketplace à sua lista de aplicações de SaaS geridas:

1. Inicie sessão no [portal do Azure](https://portal.azure.com?azure-portal=true).
1. No painel esquerdo, selecione **Azure Active Directory**.

    ![A opção do Azure Active Directory](common/select-azuread.png)

1. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

1. Para adicionar uma nova aplicação, selecione **+ nova aplicação** na parte superior do painel.

    ![A nova opção de aplicação](common/add-new-app.png)

1. Na caixa de pesquisa, introduza **PageDNA**. Nos resultados da pesquisa, selecione **PageDNA**e, em seguida, selecione **Add** para adicionar a aplicação.

    ![PageDNA na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com PageDNA com base num utilizador de teste com o nome **Eduarda Almeida**. Para o início de sessão único funcione, tem de estabelecer uma ligação entre um utilizador do Azure AD e o utilizador relacionado PageDNA.

Para configurar e testar o Azure AD início de sessão único com PageDNA, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  para permitir aos utilizadores utilizar esta funcionalidade.
1. **[Configurar PageDNA início de sessão único](#configure-pagedna-single-sign-on)**  para configurar as definições de início de sessão únicas no lado do aplicativo.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Criar um utilizador de teste PageDNA](#create-a-pagedna-test-user)**  para que haja um utilizador nomeado Eduarda Almeida na PageDNA que está ligada ao utilizador do Azure AD com o nome Eduarda Almeida.
1. **[Testar início de sessão único](#test-single-sign-on)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com PageDNA, siga os passos seguintes:

1. Na [portal do Azure](https://portal.azure.com/), na **PageDNA** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a opção de início de sessão único](common/select-sso.png)

1. Na **selecionar um método de início de sessão único** painel, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

1. Na **definir a segurança de início de sessão único com o SAML** painel, selecione **editar** (o ícone de lápis) para abrir o **configuração básica de SAML** painel.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Na **configuração básica de SAML** painel, siga os passos seguintes:

    ![Informações de início de sessão de único PageDNA domínio e URLs](common/sp-identifier.png)

    1. Na **iniciar sessão no URL** , introduza um URL com um dos seguintes padrões:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>` |
        | `https://<your domain>` |
        | `https://<your domain>/<your site>` |
        | `https://www.nationsprint.com/<your site>` |
        | |

    1. Na **identificador (ID de entidade)** , introduza um URL com um dos seguintes padrões:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>/saml2ep.cgi` |
        | `https://www.nationsprint.com/<your site>/saml2ep.cgi` |
        | |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão real e o identificador. Para obter estes valores, entre em contato com o [equipa de suporte de PageDNA](mailto:success@pagedna.com). Também pode consultar os padrões mostrados a **configuração básica de SAML** painel no portal do Azure.

1. No **definir a segurança de início de sessão único com o SAML** painel, na **certificado de assinatura SAML** secção, selecione **transferir** transferir **certificado (bruto)** entre as opções de determinado e guarde-o no seu computador.

    ![A opção de download (não processados) do certificado](common/certificateraw.png)

1. Na **configurar PageDNA** secção, copie o URL ou URLs que tem de:

   * **URL de início de sessão**
   * **Azure AD Identifier**
   * **URL de fim de sessão**

    ![Copie os URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>Configurar PageDNA início de sessão único

Para configurar o início de sessão único no lado do PageDNA, envie o certificado transferido (bruto) e os URLs de copiado apropriados do portal do Azure para o [equipa de suporte de PageDNA](mailto:success@pagedna.com). A equipe de PageDNA será Certifique-se de que a ligação de SAML SSO está definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, vai criar um utilizador de teste no portal do Azure com o nome Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**   > **utilizadores** > **todos os utilizadores**.

    ![Os utilizadores e de "Todos os utilizadores" Opções](common/users.png)

1. Na parte superior do ecrã, selecione **+ novo utilizador**.

    ![Nova opção de utilizador](common/new-user.png)

1. Na **utilizador** painel, siga os passos abaixo:

    ![O painel do utilizador](common/user-properties.png)

    1. Na **Name** , introduza **BrittaSimon**.
  
    1. Na **nome de utilizador** , introduza **BrittaSimon\@\<yourcompanydomain >.\< extensão >** . Por exemplo, **BrittaSimon\@contoso.com**.

    1. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para PageDNA.

1. No portal do Azure, selecione **aplicações empresariais** > **todas as aplicações** > **PageDNA**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

1. Na lista de aplicações, selecione **PageDNA**.

    ![PageDNA na lista de aplicações](common/all-applications.png)

1. No painel esquerdo, sob **MANAGE**, selecione **utilizadores e grupos**.

    ![A opção "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **+ adicionar utilizador**e, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** painel.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

1. No **utilizadores e grupos** painel, selecione **Eduarda Almeida** no **utilizadores** lista e, em seguida, escolha **selecione** na parte inferior do painel.

1. Se estiver à espera de um valor de função na asserção de SAML, em seguida, no **selecionar função** painel, selecione a função adequada para o utilizador a partir da lista. Na parte inferior do painel, escolha **selecione**.

1. Na **adicionar atribuição** painel, selecione **atribuir**.

### <a name="create-a-pagedna-test-user"></a>Criar um utilizador de teste PageDNA

Um usuário chamado Eduarda Almeida está agora criado no PageDNA. Não tem de fazer nada para criar este utilizador. PageDNA suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Se um usuário chamado Eduarda Almeida ainda não existir no PageDNA, é criado um novo após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar seu única início de sessão em configuração do Azure AD com o portal as minhas aplicações.

Quando seleciona **PageDNA** no portal as minhas aplicações, deve estar automaticamente conectado à subscrição PageDNA para o qual configura o início de sessão único. Para obter mais informações sobre o portal as minhas aplicações, consulte [aplicações de acesso e a utilização no portal minhas aplicações](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais para integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)