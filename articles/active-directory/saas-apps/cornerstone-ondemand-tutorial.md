---
title: 'Tutorial: Azure Ative Directory Single sign-on (SSO) integração com a Cornerstone Single Sign-On | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Cornerstone Single Sign-On.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/09/2021
ms.author: jeedes
ms.openlocfilehash: f7167df523ca6f84eacd92fc7af1011e8b3b00b6
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950388"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cornerstone-single-sign-on"></a>Tutorial: Azure Ative Directory Single sign-on (SSO) integração com o Single Sign-On

Neste tutorial, você vai aprender a integrar o Sign-On De Cornerstone Single com Azure Ative Directory (Azure AD). Quando integrar o Sign-On Stonestone Single com Azure AD, pode:

* Controle em Azure AD que tem acesso a Cornerstone Single Sign-On.
* Permita que os seus utilizadores sejam automaticamente inscritos na Sign-On De Single Cornerstone com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada Sign-On Único Single (SSO).

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* A Sign-On Cornerstone Single suporta a **SP** iniciou a SSO.
* A Sign-On Única de Pedra-Angular suporta [o fornecimento automatizado de utilizadores.](cornerstone-ondemand-provisioning-tutorial.md)


## <a name="adding-cornerstone-single-sign-on-from-the-gallery"></a>Adicionando Sign-On de Stonestone Single da galeria

Para configurar a integração do Sign-On Cornerstone Single em AD Azure, precisa de adicionar o Sign-On Cornerstone Single da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **da galeria,** **digite Cornerstone Single Sign-On** na caixa de pesquisa.
1. Selecione **O Sign-On Único da Pedra Angular** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-cornerstone-single-sign-on"></a>Configure e teste Azure AD SSO para Sign-On single de pedra angular

Configure e teste Azure AD SSO com Sign-On de pedra angular utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Sign-On Único de Pedra Angular.

Para configurar e testar O SSO Azure AD com o Sign-On Único da Pedra Angular, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
2. **[Configure a Cornerstone Single Sign-On SSO](#configure-cornerstone-single-sign-on-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Crie o Utilizador de teste de Sign-On De Pedra Angular](#create-cornerstone-single-sign-on-test-user)** - para ter uma contrapartida de B.Simon em Sign-On Single Cornerstone que está ligada à representação AD do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **Sign-On únicas** da Pedra Angular, encontre a secção **'Gerir'** e selecione **'Único sinal de s-on'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com página SAML,** clique no ícone de lápis para **configuração SAML básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<PORTAL_NAME>.csod.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<PORTAL_NAME>.csod.com/samldefault.aspx?ouid=<OUID>`

    c. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<PORTAL_NAME>.csod.com/samldefault.aspx?ouid=<OUID>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de resposta real, identificador e sinal no URL. Contacte [a equipa de suporte Sign-On cliente da Cornerstone Single](mailto:moreinfo@csod.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar a Sílava Única Inscrição,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Cornerstone Single Sign-On.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Cornerstone Single Sign-On**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-cornerstone-single-sign-on-sso"></a>Configurar a Pedra Angular Single Sign-On SSO

1. Inscreva-se no Sign-On Único de Pedra Como administrador.

1. Vá às **Ferramentas de administração ->**.

    ![screeenshot para página de Administrador.](./media/cornerstone-ondemand-tutorial/admin.png)

1. Selecione **painel EDGE** em **Ferramentas de Configuração**.

    ![screeenshot para painel EDGE.](./media/cornerstone-ondemand-tutorial/edge-panel.png)

1. Selecione Sign-On única na secção **Integração.**

    ![screeenshot para opção single Sign-On.](./media/cornerstone-ondemand-tutorial/single-sign-on.png)

1. Clique no botão **Adicionar SSO.** Selecione **INbound SAML** na janela pop up abaixo mostrada e, em seguida, clique em **Adicionar**.

    ![screeenshot para Inbound SAML.](./media/cornerstone-ondemand-tutorial/inbound.png)

1. Execute os passos abaixo na seguinte página:

    ![screeenshot para a secção de configuração para Stonestone.](./media/cornerstone-ondemand-tutorial/configuration.png)

    a. Nas **Propriedades Gerais,** clique no **Upload File** para fazer o upload do ficheiro **Certificado (Base64),** que descarregou a partir do portal Azure.

    b. Selecione a caixa de verificação **Enable** e na caixa de texto **DO URL do IDP,** cole o valor URL de **login** que copiou a partir do portal Azure.

    c. Clique em **Guardar**.

### <a name="create-cornerstone-single-sign-on-test-user"></a>Criar o utilizador de teste de Sign-On único da Pedra Angular

O objetivo desta secção é criar um utilizador chamado B.Simon em Cornerstone Single Sign-On. A Sign-On Única de Pedra-Angular suporta o fornecimento automático do utilizador, que está por defeito. Pode encontrar mais detalhes [aqui](./cornerstone-ondemand-provisioning-tutorial.md) sobre como configurar o fornecimento automático do utilizador.

**Se precisar de criar o utilizador manualmente, execute os seguintes passos:**

1. Inscreva-se no Sign-On Único de Pedra Como administrador.

1. Vá ao **Administrador -> Utilizadores** e clique em **Adicionar Utilizador** na parte inferior da página.

    ![screeenshot para a criação de utilizador de teste de Cornerstone.](./media/cornerstone-ondemand-tutorial/user-1.png)

1. Preencha os campos necessários na página **de utilizador de adicionar e** clique em **Guardar**.

    ![screeenshot para a criação do utilizador de teste com os campos necessários.](./media/cornerstone-ondemand-tutorial/user-2.png)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de inscrição Sign-On Único de Pedra, onde pode iniciar o fluxo de login. 

* Vá diretamente ao Url de inscrição de Sign-On Cornerstone Single e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo de Sign-On Stonestone Single nas Minhas Apps, este irá redirecionar para o Stonestone Single Sign-On URL de inscrição. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Assim que configurar a Sign-On De Pedra-Angular, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)