---
title: 'Tutorial: Integração do Diretório Ativo Azure com o LaunchDarkly | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o LaunchDarkly.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: ddfffb77ca889aea9ff32c7be1ce2e4cb7fc04a7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037612"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Tutorial: Integração do Azure Ative Directory com o LaunchDarkly

Neste tutorial, você vai aprender a integrar o LaunchDarkly com o Azure Ative Directory (Azure AD). Quando integrar o LaunchDarkly com a AD Azure, pode:

* Controle em Azure AD que tem acesso ao LaunchDarkly.
* Permitir que os seus utilizadores sejam automaticamente inscritos no LaunchDarkly com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

    > [!NOTE]
    > A integração do LaunchDarkly Azure Ative Directory é unidireccionária. Depois de configurar a integração, pode utilizar o AZure AD para gerir utilizadores, SSO e contas no LaunchDarkly, mas **não pode** utilizar o LaunchDarkly para gerir utilizadores, SSO e contas em Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Lançamento Assinatura ativada por um único sinal.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* LaunchDarkly suporta **IDP** iniciado SSO.
* O LaunchDarkly suporta o fornecimento do utilizador **Just In Time.**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="add-launchdarkly-from-the-gallery"></a>Adicione LaunchDarkly da galeria

Para configurar a integração do LaunchDarkly no AD Azure, é necessário adicionar o LaunchDarkly da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **escreva LaunchDarkly** na caixa de pesquisa.
1. Selecione **LaunchDarkly** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-launchdarkly"></a>Configurar e testar Azure AD SSO para o LaunchDarkly

Configure e teste Azure AD SSO com LaunchDarkly usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no LaunchDarkly.

Para configurar e testar o Azure AD SSO com launchDarkly, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure LaunchDarkly SSO](#configure-launchdarkly-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create LaunchDarkly test user](#create-launchdarkly-test-user)** - para ter uma contraparte de B.Simon no LaunchDarkly que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **LaunchDarkly,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **identifier,** digite o URL: `app.launchdarkly.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`

    > [!NOTE]
    > O valor URL de resposta não é real. Irá atualizar o valor com o URL de resposta real, que é explicado mais tarde no tutorial. Se pretender utilizar a aplicação no modo **IDP,** tem de deixar o Sinal em branco no campo **URL,** caso contrário não poderá iniciar o login a partir do **IDP**. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

7. Na secção **set up LaunchDarkly,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao LaunchDarkly.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **LaunchDarkly**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-launchdarkly-sso"></a>Configurar lançamentoDarkly SSO

1. Numa janela diferente do navegador web, inicie sessão no seu site da empresa LaunchDarkly como administrador.

2. Selecione **Definições** de Conta do painel de navegação esquerdo.

    ![A screenshot mostra o item Definições de Conta selecionados em Produção.](./media/launchdarkly-tutorial/configure-1.png)

3. Clique no separador **Segurança.**

    ![A screenshot mostra o separador segurança das definições de Conta.](./media/launchdarkly-tutorial/configure-2.png)

4. Clique **EM ATIVAR SSO** e, em seguida, **EDITAR CONFIGURAÇÃO SAML**.

    ![A screenshot mostra a página de assinatura única onde pode ATIVAR S S O e EDIT SAML CONFIGURAÇÃO.](./media/launchdarkly-tutorial/configure-3.png)

5. Na secção **de configuração da Editar,** execute os seguintes passos:

    ![A screenshot mostra a secção de configuração DOML onde pode escriminar as alterações descritas aqui.](./media/launchdarkly-tutorial/configure-4.png)

    a. Copie o **URL de serviço ao consumidor SAML** para o seu exemplo e cole-o na caixa de texto de URL de resposta na secção **LaunchDarkly Domain and URLs** no portal Azure.

    b. Na caixa de texto **URL de acesso,** cole o valor URL de **login,** que copiou a partir do portal Azure.

    c. Abra o certificado descarregado do portal Azure para o Bloco de Notas, copie o conteúdo e, em seguida, cole-o na caixa **de certificados X.509** ou pode fazer o upload direto do certificado clicando no **upload um**.

    d. Clique em **Guardar**.

### <a name="create-launchdarkly-test-user"></a>Criar LaunchDarkly test user

Nesta secção, um utilizador chamado B.Simon é criado no LaunchDarkly. O LaunchDarkly suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nada de ação para ti nesta secção. Se um utilizador já não existir no LaunchDarkly, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no LaunchDarkly para o qual configura o SSO.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo LaunchDarkly nas Minhas Apps, deverá ser automaticamente inscrito no LaunchDarkly para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o LaunchDarkly, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
