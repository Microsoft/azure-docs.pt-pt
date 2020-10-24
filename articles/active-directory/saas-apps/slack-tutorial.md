---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Slack Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Slack.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: jeedes
ms.openlocfilehash: 1cdce2108130b64533bc6f14a4e9084a15678d2c
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515887"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Tutorial: Azure Ative Directy integração única (SSO) com Slack

Neste tutorial, você vai aprender a integrar Slack com Azure Ative Directory (Azure AD). Quando integrar o Slack com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao Slack.
* Ative os seus utilizadores a serem automaticamente inscritos na Slack com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura de sso ativada por um único sinal de slack (SSO).

> [!NOTE]
> Se você precisa se integrar com mais de uma instância Slack em um inquilino, o identificador para cada aplicação pode ser uma variável.

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Slack suporta **SP** iniciado SSO
* Slack suporta **provisão do** utilizador Just In Time
* Slack suporta fornecimento [ **automatizado** de utilizadores](./slack-provisioning-tutorial.md)
* Uma vez configurado Slack, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-slack-from-the-gallery"></a>Adicionando slack da galeria

Para configurar a integração de Slack em Azure AD, você precisa adicionar Slack da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** escreva **Slack** na caixa de pesquisa.
1. Selecione **Slack** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-slack"></a>Configurar e testar Azure AD SSO para slack

Configure e teste Azure AD SSO com Slack usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Slack.

Para configurar e testar o Azure AD SSO com slack, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Slack SSO](#configure-slack-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Slack test user](#create-slack-test-user)** - para ter uma contraparte de B.Simon in Slack que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Slack,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<DOMAIN NAME>.slack.com/sso/saml/start`

    b. Na caixa de texto **identifier (Entity ID),** digite o URL: `https://slack.com`
    
    c. Para **URL de resposta**, insira um dos seguintes padrões de URL:
    
    | URL de Resposta|
    |----------|
    | `https://<DOMAIN NAME>.slack.com/sso/saml` |
    | `https://<DOMAIN NAME>.enterprise.slack.com/sso/saml` |

    > [!NOTE]
    > Estes valores não são reais. É necessário atualizar estes valores com o URL e URL de resposta de assinaturas reais. Contacte [a equipa de suporte do Cliente Slack](https://slack.com/help/contact) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

    > [!NOTE]
    > O valor para **Identifier (Entity ID)** pode ser uma variável se tiver mais de uma instância slack que precisa de integrar com o inquilino. Use o `https://<DOMAIN NAME>.slack.com` padrão. Neste cenário, também deve emparelhar com outra definição em Slack usando o mesmo valor.

1. A aplicação Slack espera as afirmações DO SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação Slack espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos. Também deve adicionar o `email` atributo. Se o utilizador não tiver um endereço de **e-mail, mapear o endereço de e-mail** para **user.userprincipalname** e **mapear o e-mail** para **user.userprincipalname**.

    | Nome | Atributo de origem |
    | -----|---------|
    | endereço de e-mail | user.userprincipalname |
    | e-mail | user.userprincipalname |

   > [!NOTE]
   > Para configurar a configuração do prestador de serviços (SP), tem de clicar em **Expandir** ao lado **das Opções Avançadas** na página de configuração SAML. Na caixa de **emitente do fornecedor de serviços,** insira o URL do espaço de trabalho. O padrão é slack.com. 

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Slack,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Slack.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Slack**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-slack-sso"></a>Configurar sso de folga

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Slack como administrador.

2. Navegue para **o Microsoft Azure AD** e vá para **as Definições de Equipa**.

     ![Configurar um único sinal no Microsoft Azure AD](./media/slack-tutorial/tutorial-slack-team-settings.png)

3. Na secção **Definições de Equipa,** clique no **separador Autenticação** e, em seguida, clique em **'Alterar Definições'**

    ![Configurar um único sinal de inscrição nas definições de equipa](./media/slack-tutorial/tutorial-slack-authentication.png)

4. No diálogo **de Autenticação SAML,** execute os seguintes passos:

    ![Configurar um único sinal de inscrição nas definições de autenticação SAML](./media/slack-tutorial/tutorial-slack-save-authentication.png)

    a.  Na caixa de texto **SAML 2.0 Endpoint (HTTP),** cole o valor do URL de **login,** que copiou do portal Azure.

    b.  Na caixa de texto do **Fornecedor de Identidade,** cole o valor do **Identificador Azure Ad,** que copiou do portal Azure.

    c.  Abra o seu ficheiro de certificado descarregado no Bloco de Notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **do Certificado Público.**

    d. Configure as três configurações acima, conforme apropriado para a sua equipa Slack. Para mais informações sobre as definições, encontre aqui o **guia de configuração SSO da Slack.** `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    ![Configurar um único sinal no lado da aplicação](./media/slack-tutorial/tutorial-slack-expand.png)

    e. Clique em **expandir** e insira `https://slack.com` na caixa de texto do **emitente do fornecedor de serviço.**

    f.  Clique **em Guardar a configuração**.
    
    > [!NOTE]
    > Se tiver mais de uma instância slack que precisa de integrar com a Azure AD, defina para o `https://<DOMAIN NAME>.slack.com` **emitente do fornecedor de serviços** para que possa emparelhar com a definição de **identificador de** aplicação Azure.

### <a name="create-slack-test-user"></a>Criar utilizador de teste Slack

O objetivo desta secção é criar um utilizador chamado B.Simon in Slack. A folga suporta o provisionamento just-in-time, que é por defeito ativado. Não há nenhum item de ação para si nesta secção. Um novo utilizador é criado durante uma tentativa de aceder ao Slack se ainda não existir. A Slack também suporta o fornecimento automático do utilizador, podendo encontrar mais detalhes [aqui](slack-provisioning-tutorial.md) sobre como configurar o fornecimento automático do utilizador.

> [!NOTE]
> Se precisar de criar um utilizador manualmente, tem de contactar a equipa de [suporte slack](https://slack.com/help/contact).

> [!NOTE]
> Azure AD Connect é a ferramenta de sincronização que pode sincronizar nas instalações Identidades do Diretório Ativo para Azure AD e, em seguida, estes utilizadores sincronizados também podem usar as aplicações como outros utilizadores da nuvem.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Slack no Painel de Acesso, deverá ser automaticamente inscrito na Slack para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente Slack com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)