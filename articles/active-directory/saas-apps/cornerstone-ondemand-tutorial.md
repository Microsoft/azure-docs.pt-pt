---
title: 'Tutorial: Azure Ative Directory Single sign-on (SSO) integração com a Cornerstone OnDemand / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Cornerstone OnDemand.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 2837f8c5b098b0502bd293ec2414ad7401cc4f95
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996173"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cornerstone-ondemand"></a>Tutorial: Azure Ative Directory Single sign-on (SSO) integração com a Cornerstone OnDemand

Neste tutorial, você vai aprender a integrar a Cornerstone OnDemand com o Azure Ative Directory (Azure AD). Quando integrar a Cornerstone OnDemand com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Cornerstone OnDemand.
* Permita que os seus utilizadores sejam automaticamente inscritos na Cornerstone OnDemand com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por StoneA OnDemand (SSO).

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Stonestone OnDemand apoia **SP** iniciado SSO
* A Cornerstone OnDemand suporta [o fornecimento automatizado de utilizadores](cornerstone-ondemand-provisioning-tutorial.md)
* Assim que configurar a Cornerstone OnDemand, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>Adicionando Stonestone OnDemand da galeria

Para configurar a integração da Cornerstone OnDemand no AD Azure, precisa de adicionar a Cornerstone OnDemand da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **da galeria,** **digite Cornerstone OnDemand** na caixa de pesquisa.
1. Selecione **StoneStone OnDemand** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cornerstone-ondemand"></a>Configurar e testar Azure AD único sign-on para Stonestone OnDemand

Configure e teste Azure AD SSO com a Cornerstone OnDemand usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Cornerstone OnDemand.

Para configurar e testar a Azure AD SSO com a Cornerstone OnDemand, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
2. **[Configure a Cornerstone OnDemand SSO](#configure-cornerstone-ondemand-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create Cornerstone OnDemand test user](#create-cornerstone-ondemand-test-user)** - para ter uma contraparte de B.Simon em Cornerstone OnDemand que está ligada à representação AD AD do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Cornerstone OnDemand,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<company>.csod.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<company>.csod.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte a [equipa de suporte do Cliente Cornerstone OnDemand](mailto:moreinfo@csod.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar a Pedra Angular OnDemand,** copie os URL(s) apropriados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
    1. No campo **Nome**, introduza `B.Simon`.  
    1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
    1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
    1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Cornerstone OnDemand.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Cornerstone OnDemand**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-cornerstone-ondemand-sso"></a>Configure Cornerstone OnDemand SSO

Para configurar um único sinal no lado **da Cornerstone OnDemand,** é necessário enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de [suporte da Cornerstone OnDemand.](mailto:moreinfo@csod.com) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-cornerstone-ondemand-test-user"></a>Criar utilizador de teste Cornerstone OnDemand

O objetivo desta secção é criar um utilizador chamado B.Simon em Cornerstone OnDemand. A Cornerstone OnDemand suporta o fornecimento automático do utilizador, que é por defeito ativado. Pode encontrar mais detalhes [aqui](./cornerstone-ondemand-provisioning-tutorial.md) sobre como configurar o fornecimento automático do utilizador.

**Se precisar de criar o utilizador manualmente, execute os seguintes passos:**

Para configurar o provisionamento do utilizador, envie as informações (por exemplo: Nome, e-mail) sobre o utilizador Azure AD que pretende prestar à equipa de [suporte da Cornerstone OnDemand](mailto:moreinfo@csod.com).

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador da Cornerstone OnDemand ou APIs fornecidas pela Cornerstone OnDemand para fornecer contas de utilizadores Azure AD.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Stonestone OnDemand no Painel de Acesso, deverá ser automaticamente inscrito na Cornerstone OnDemand para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente a Cornerstone OnDemand com a Azure AD](https://aad.portal.azure.com)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)