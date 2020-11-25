---
title: 'Tutorial: Integração AZURE AD SSO com Smart Global Governance'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Smart Global Governance.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: jeedes
ms.openlocfilehash: 5a2169db47ca5a6adcddbcc9558161370b896a62
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997074"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smart-global-governance"></a>Tutorial: Azure Ative Directory integração única (SSO) com Smart Global Governance

Neste tutorial, você vai aprender a integrar Smart Global Governance com Azure Ative Directory (Azure AD). Quando integrar o Smart Global Governance com a AD Azure, pode:

* Use a Azure AD para controlar quem pode aceder a Smart Global Governance.
* Permita que os seus utilizadores sejam automaticamente inscritos na Smart Global Governance com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte [o Single sign-on para aplicações no Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição de Governação Global Inteligente com um único sinal de sso ativado.

## <a name="tutorial-description"></a>Descrição tutorial

Neste tutorial, você vai configurar e testar Azure AD SSO em um ambiente de teste.

A Smart Global Governance apoia sSO iniciado pelo SP e iniciado pelo IDP.

Depois de configurar a Smart Global Governance, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-smart-global-governance-from-the-gallery"></a>Adicione Smart Global Governance da galeria

Para configurar a integração do Smart Global Governance em Azure AD, precisa adicionar Smart Global Governance da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta de trabalho ou escola ou com uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione **Azure Active Directory**.
1. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** insira **a Smart Global Governance** na caixa de pesquisa.
1. Selecione **Smart Global Governance** no painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-smart-global-governance"></a>Configurar e testar Azure AD SSO para governação global inteligente

Você vai configurar e testar Azure AD SSO com Smart Global Governance usando um utilizador de teste chamado B.Simon. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador correspondente na Smart Global Governance.

Para configurar e testar o Azure AD SSO com a Smart Global Governance, você vai dar estes passos de alto nível:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem a funcionalidade.
    1. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com um único sinal de sessão.
    1. **[Conceder acesso ao utilizador de teste](#grant-access-to-the-test-user)** para permitir ao utilizador utilizar um único sinal de Azure.
1. **[Configure smart global governance SSO](#configure-smart-global-governance-sso)** no lado da aplicação.
    1. **[Crie um utilizador de teste de Governação Global Inteligente](#create-a-smart-global-governance-test-user)** como contrapartida da representação AD do utilizador.
1. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir que o Azure AD SSO no portal Azure:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Smart Global Governance,** na secção **Gerir,** selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. Na **configuração de Sign-On única com** a página SAML, selecione o botão de lápis para **configuração SAML Básica** para editar as definições:

   ![Botão de lápis para configuração básica de SAML](common/edit-urls.png)

1. Na secção **Configuração Básica saml,** se pretender configurar a aplicação no modo iniciado pelo IDP, tome os seguintes passos.

    a. Na caixa **do identificador,** insira um destes URLs:

    - `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/metadata`
    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/metadata`

    b. Na caixa **URL de resposta,** insira um destes URLs:

    - `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/acs`
    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/acs`

1. Se pretender configurar a aplicação no modo iniciado pelo SP, selecione **Definir URLs adicionais** e completar o passo seguinte.

   - Na caixa **URL de inscrição,** insira um destes URLs:

    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo`
    - `https://eu-fr-south.console.smartglobalprivacy.com/platform`

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** selecione o link **de descarregamento** para **Certificado (Raw)** para descarregar o certificado e guardá-lo no seu computador:

    ![Link de descarregamento de certificado](common/certificateraw.png)

1. Na secção **Configurar a Definição de Governação Global Inteligente,** copie os URL ou URLs apropriados, com base nos seus requisitos:

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste chamado B.Simon no portal Azure.

1. No painel esquerdo do portal Azure, selecione **Azure Ative Directory**. Selecione **Utilizadores** e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** complete estes passos:
   1. Na caixa **Nome,** insira **B.Simon**.  
   1. Na caixa **do nome do utilizador,** \<username> @ \<companydomain> introduza \<extension> . . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione **Mostrar a palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa **palavra-passe.**
   1. Selecione **Criar**.

### <a name="grant-access-to-the-test-user"></a>Conceder acesso ao utilizador de teste

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo a esse utilizador acesso a Smart Global Governance.

1. No portal Azure, selecione **aplicações Enterprise** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Smart Global Governance**.
1. Na página geral da aplicação, na secção **Gerir,** selecione **Utilizadores e grupos**:

   ![Selecionar Utilizadores e grupos](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **de atribuição de adicionar:**

    ![Selecione Adicionar utilizador](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** na lista **de Utilizadores** e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

## <a name="configure-smart-global-governance-sso"></a>Configurar smart global governance SSO

Para configurar um único sign-on no lado smart Global Governance, você precisa enviar o certificado em bruto descarregado e os URLs apropriados que copiou do portal Azure para a equipe de [apoio smart Global Governance](mailto:support.tech@smartglobal.com). Configuram a ligação SSO SAML para ser correta em ambos os lados.

### <a name="create-a-smart-global-governance-test-user"></a>Criar um utilizador de teste de governação global inteligente

Trabalhe com a equipa de apoio à [Governação Global Inteligente](mailto:support.tech@smartglobal.com) para adicionar um utilizador chamado B.Simon na Smart Global Governance. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, irá testar a sua configuração Azure AD SSO utilizando o Painel de Acesso.

Quando selecionar o azulejo smart Global Governance no Painel de Acesso, deverá ser automaticamente inscrito na instância de Governação Global Inteligente para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [o Painel de Introdução ao Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais sobre como integrar apps SaaS com Diretório Ativo Azure ](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)

- [Experimente a Governação Global Inteligente com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger a Governação Global Inteligente com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)