---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Clever | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Clever.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2020
ms.author: jeedes
ms.openlocfilehash: 29384457f946eff5708c319e14161d670adf6ca8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98729895"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-clever"></a>Tutorial: Azure Ative Directory integração única (SSO) com Clever

Neste tutorial, você vai aprender a integrar Clever com Azure Ative Directory (Azure AD). Quando integras o Clever com a AD Azure, podes:

* Controlo em Azure AD que tem acesso ao Clever.
* Permita que os seus utilizadores sejam automaticamente inscritos no Clever com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura inteligente de um único sinal (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Apoio inteligente **SP** iniciou SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-clever-from-the-gallery"></a>Adicionando Inteligente da galeria

Para configurar a integração de Clever em Azure AD, você precisa adicionar Clever da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **da galeria,** escreva **Clever** na caixa de pesquisa.
1. Selecione **Smart** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-clever"></a>Configure e teste Azure AD SSO para Inteligente

Configure e teste Azure AD SSO com Clever usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Clever.

Para configurar e testar a Azure AD SSO com Clever, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Clever SSO](#configure-clever-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Clever test user](#create-clever-test-user)** - para ter uma contraparte de B.Simon in Clever que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **Clever,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://clever.com/in/<companyname>`

    b. Na caixa de texto **identifier (Entity ID),** digite o URL: `https://clever.com/oauth/saml/metadata.xml`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://clever.com/<companyname>`
    
    > [!NOTE]
    >  Estes valores não são reais. Atualize estes valores com o URL e URL de resposta de assinatura real. Contacte [a equipa de suporte do Cliente Inteligente](https://clever.com/about/contact/) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** clique no botão de cópia para copiar o Url de **metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Clever.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Clever**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-clever-sso"></a>Configurar SSO Inteligente

Siga as instruções dadas no [link](https://support.clever.com/hc/articles/205889768-Single-Sign-On-SSO-Log-in-with-Office-365-Azure-) para configurar um único sinal no lado inteligente.

### <a name="create-clever-test-user"></a>Criar utilizador de teste inteligente

Para permitir que os utilizadores de Azure AD assinem com o Clever, devem ser adsejados em Clever.

No caso de Clever, trabalhe com [a equipa de suporte do Cliente Inteligente](https://clever.com/about/contact/) para adicionar os utilizadores na plataforma Clever. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas inteligentes de criação de conta de utilizador ou APIs fornecidas pela Clever para fornecer contas de utilizador Azure AD.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para O URL de inscrição inteligente onde pode iniciar o fluxo de login. 

* Vá diretamente para o URL de inscrição inteligente e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Inteligente nas Minhas Apps, isto irá redirecionar para O URL de Inscrição Inteligente. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Clever, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)