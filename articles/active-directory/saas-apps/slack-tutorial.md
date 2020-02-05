---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Slack  SsO) integração com Slack  Slack Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Slack.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c80963976783321d05fc6f32bb24daed36fa105
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985561"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com A Folga

Neste tutorial, aprenderá a integrar o Slack com o Azure Ative Directory (Azure AD). Quando integrar o Slack com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a Slack.
* Permita que os seus utilizadores sejam automaticamente inscritos no Slack com as suas contas Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Slack single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Slack apoia **SP** iniciado SSO
* A margem de atraso dá suporte ao provisionamento **de usuário just in time**
* A margem de atraso dá suporte ao [provisionamento **automatizado** de usuários](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-provisioning-tutorial)
* Assim que configurar o Slack, pode impor o controlo da Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controle de sessão com Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-slack-from-the-gallery"></a>Adicionando folga da galeria

Para configurar a integração da Slack no Azure AD, precisa de adicionar Slack da galeria à sua lista de aplicações saaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. No Add da secção **galeria,** digite **Slack** na caixa de pesquisa.
1. Selecione **A folga** no painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-slack"></a>Configure e teste Azure AD único sign-on para Slack

Configure e teste Azure AD SSO com Slack utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Slack.

Para configurar e testar o Azure AD SSO com o Slack, complete os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configure slack SSO](#configure-slack-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie um utilizador](#create-slack-test-user)** de teste Slack - para ter uma contraparte de B.Simon em Slack que esteja ligada à representação do utilizador da AD Azure.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Slack,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<companyname>.slack.com`

    b. Na caixa de texto **identificador (Id** da entidade), escreva um URL: `https://slack.com`

    > [!NOTE]
    > O valor do Signon on URL não é real. Atualize o valor com o sinal real no URL. Contacte a equipa de suporte do [Cliente Slack](https://slack.com/help/contact) para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. O aplicativo de margem de atraso espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/edit-attribute.png)

1. Além de acima, o aplicativo de margem de atraso espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos. Se os usuários não tiverem endereço de email, mapeie o **EmailAddress** para **User. UserPrincipalName**.

    | Nome | Atributo de origem |
    | -----|---------|
    | EmailAddress | user.userprincipalname |
    | | |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na secção **'Slack' configurar,** copie os URL(s) adequados com base no seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso ao Slack.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Slack**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-slack-sso"></a>Configure Slack SSO

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa Slack como administrador.

2. Navegue até **Microsoft Azure ad** , em seguida, vá para **configurações da equipe**.

     ![Configure um único sinal on no lado da aplicação](./media/slack-tutorial/tutorial_slack_001.png)

3. Na secção Definições de **Equipa,** clique no separador **Autenticação** e, em seguida, clique em **Alterar Definições**.

    ![Configure um único sinal on no lado da aplicação](./media/slack-tutorial/tutorial_slack_002.png)

4. No diálogo de definições de **autenticação SAML,** execute os seguintes passos:

    ![Configure um único sinal on no lado da aplicação](./media/slack-tutorial/tutorial_slack_003.png)

    a.  Na caixa de texto **SAML 2.0 Endpoint (HTTP),** cola o valor do URL de **Login,** que copiou do portal Azure.

    b.  Na caixa de texto **emitentes** de fornecedor de identidade, colhe o valor do **Identificador De Ad Azure,** que copiou do portal Azure.

    c.  Abra o seu ficheiro de certificado descarregado no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto do **Certificado Público.**

    d. Configure as três configurações acima conforme apropriado para a sua equipa Slack. Para mais informações sobre as definições, consulte aqui o **guia de configuração SSO do Slack.** `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Clique na **configuração de guardar**.

### <a name="create-slack-test-user"></a>Criar utilizador de teste Slack

O objetivo desta secção é criar um utilizador chamado B.Simon em Slack. A folga suporta o fornecimento just-in-time, que é por defeito habilitado. Não há nenhum item de ação para você nesta seção. Um novo utilizador é criado durante uma tentativa de aceder ao Slack se ainda não existir. A Slack também suporta o fornecimento automático de utilizadores, pode encontrar mais detalhes [aqui](slack-provisioning-tutorial.md) sobre como configurar o fornecimento automático de utilizadores.

> [!NOTE]
> Se precisar de criar um utilizador manualmente, tem de contactar a equipa de [suporte Slack](https://slack.com/help/contact).

> [!NOTE]
> O Azure AD Connect é a ferramenta de sincronização que pode sincronizar nas instalações Identidades de Diretório Ativo para a AD Azure e, em seguida, estes utilizadores sincronizados também podem usar as aplicações como outros utilizadores da nuvem.

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no azulejo Slack no Painel de Acesso, deve ser automaticamente inscrito na Folga para a qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente slack com Azure AD](https://aad.portal.azure.com/)

- [O que é o controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)