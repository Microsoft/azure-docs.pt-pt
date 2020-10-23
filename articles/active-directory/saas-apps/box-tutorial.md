---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Caixa / Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Box.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2020
ms.author: jeedes
ms.openlocfilehash: 54906395d117f99cd3ce44cbd2457afe857beeaf
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92456845"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-box"></a>Tutorial: Azure Ative Directy integração única (SSO) com Caixa

Neste tutorial, você vai aprender a integrar Box com Azure Ative Direy (Azure AD). Quando integra a Box com AZure AD, pode:

* Controlo em Azure AD que tem acesso ao Box.
* Ative os seus utilizadores a serem automaticamente inscritos na Box com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura de inscrição única (SSO) ativada.

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Box suporta **SSO** iniciado SP
* Caixa suporta [ **fornecimento automatizado** de utilizadores e desprovisionamento](./box-userprovisioning-tutorial.md) (recomendado)
* Caixa suporta **provisão do** utilizador Just In Time
* Uma vez configurar a Caixa, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O Controlo de Sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-box-from-the-gallery"></a>Adicionar caixa da galeria

Para configurar a integração da Box no Azure AD, precisa adicionar a Box da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva **Box** na caixa de pesquisa.
1. Selecione **Box** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-box"></a>Configurar e testar Azure AD único sinal para box

Configure e teste Azure AD SSO com caixa usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Box.

Para configurar e testar o Azure AD SSO com Box, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Caixa SSO](#configure-box-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Box test user](#create-box-test-user)** - para ter uma contraparte de B.Simon in Box que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Box,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.account.box.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL: `box.net`

    c. Na caixa de texto **URL de resposta,** digite um URL: `https://sso.services.box.net/sp/ACS.saml2`

    > [!NOTE]
    > O valor url de inscrição não é real. Atualize o valor com o URL de Sign-On real. Contacte [a equipa de suporte do Cliente box](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Box.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Box**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-box-sso"></a>Caixa de configuração SSO

Para configurar sSO para a sua aplicação, siga o procedimento na [Configuração SSO por conta própria](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown).

> [!NOTE]
> Se não conseguir configurar as definições SSO para a sua conta Box, tem de enviar a equipa de [suporte](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire) **de Metadados XML** da Federação descarregada. Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-box-test-user"></a>Criar utilizador de teste box

Nesta secção, um utilizador chamado Britta Simon é criado na Box. A caixa suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na Box, um novo é criado após a autenticação.

> [!NOTE]
> Se necessitar de criar um utilizador manualmente, contacte a [equipa de suporte da Caixa.](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire)

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo da Caixa no Painel de Acesso, deverá ser automaticamente inscrito na Caixa para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente caixa com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger a Caixa com visibilidade e controlos avançados](/cloud-app-security/protect-box)