---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com siteIntel | Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o SiteIntel.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: jeedes
ms.openlocfilehash: 0c8224bf84a2235086d941df7d02ab6f458f3f16
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92510003"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-siteintel"></a>Tutorial: Azure Ative Directory integração única (SSO) com siteIntel

Neste tutorial, você vai aprender a integrar o SiteIntel com o Azure Ative Directory (Azure AD). Quando integra o SiteIntel com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao SiteIntel.
* Permita que os seus utilizadores sejam automaticamente inscritos no SiteIntel com as suas contas AD Azure.
* Gerencie as suas contas numa localização central, o portal Azure.

Para saber mais sobre software como integração de aplicações de serviço (SaaS) com Azure AD, veja o que é o acesso à [aplicação e um único acesso com o Azure Ative Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada pelo SiteIntel (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* O SiteIntel suporta sSO iniciado pelo SP e iniciado pelo IdP.
* Depois de configurar o SiteIntel, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o acesso condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-siteintel-from-the-gallery"></a>Adicionar SiteIntel da galeria

Para configurar a integração do SiteIntel no Azure AD, é necessário adicionar o SiteIntel da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione **Azure Ative Directory**.
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na caixa de adicionar a partir da caixa **de galeria,** **insira siteIntel**.
1. Na lista de resultados, selecione **SiteIntel** e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-siteintel"></a>Configurar e testar Azure AD único sign-on para SiteIntel

Configure e teste Azure AD SSO com siteIntel utilizando um utilizador de teste chamado *B.Simon*. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no SiteIntel.

Para configurar e testar o Azure AD SSO com o SiteIntel, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.  

    a. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o único sinal de Azure AD com o utilizador B.Simon.  

    b. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir ao utilizador B.Simon utilizar um único sinal de Azure.

1. **[Configurar o SiteIntel SSO](#configure-siteintel-sso)** para configurar as definições de inscrição única no lado da aplicação.

    * **[Crie um utilizador de teste SiteIntel](#create-a-siteintel-test-user)** para ter uma contrapartida do utilizador B.Simon no SiteIntel que está ligada à representação AD Azure do utilizador.

1. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Para ativar o Azure AD SSO no portal Azure, faça o seguinte:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **SiteIntel,** aceda à secção **Gerir** e, em seguida, selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, ao lado da **Configuração Básica SAML,** selecione **Editar** (ícone da caneta).

   ![Screenshot do painel "Configurar single-sign-on com SAML"](common/edit-urls.png)

1. Para configurar a aplicação no modo iniciado pelo IdP, na secção **Configuração Básica SAML,** faça o seguinte:

    a. Na caixa **identifier,** digite um URL no seguinte formato: `urn:amazon:cognito:sp:<REGION>_<USERPOOLID>`

    b. Na caixa **URL de resposta,** digite um URL no seguinte formato: `https://<CLIENT>.auth.siteintel.com/saml2/idpresponse`

    c. Na caixa **'Retransmissão' estatal,** digite um URL no seguinte formato: `https://<CLIENT>.siteintel.com`

1. Para configurar a aplicação no modo iniciado pelo SP, selecione **Definir URLs adicionais** e, em seguida, fazer o seguinte:

   * Na caixa **URL de entrada de inscrição,** digite um URL no seguinte formato: `https://<CLIENT>.siteintel.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize-os com o identificador real, URL de resposta, URL de inscrição e Estado de retransmissão. Para obter estes valores, contacte [a equipa de suporte do Cliente SiteIntel.](mailto:support@intalytics.com) Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** selecione o botão **Copiar** para copiar o URL na caixa de url **de metadados da Federação de Aplicações.**

    ![Screenshot do botão de cópia "App Federation Metadata Url"](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, cria-se um utilizador de teste no portal Azure chamado B.Simon.

1. No painel esquerdo do portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >    >  **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do painel.
1. Nas propriedades **do Utilizador,** faça o seguinte:

   a. Na caixa **Nome,** insira **B.Simon**.  

   b. Na caixa **do nome do utilizador,** introduza o nome de utilizador no seguinte formato: `username@companydomain.extension` (por exemplo, `B.Simon@contoso.com` ).

   c. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**

   d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, permite ao utilizador B.Simon utilizar o Azure single sign-on, concedendo acesso ao SiteIntel.

1. No portal Azure, selecione **Aplicações empresariais**  >  **Todas as aplicações**.
1. Na lista **de Aplicações,** selecione **SiteIntel**.
1. Na página geral da aplicação, na secção **Gerir,** selecione **Utilizadores e grupos**.

   ![Screenshot do link "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador** e, em seguida, no painel de atribuição de **adicionar,** selecione **Utilizadores e grupos**.

    ![Screenshot do botão "Adicionar utilizador"](common/add-assign-user.png)

1. No painel **de Utilizadores e grupos,** selecione **B.Simon** e, em seguida, selecione o botão **Selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no painel **'Função Seletiva',** selecione a função adequada para o utilizador da lista e, em seguida, selecione o botão **Selecione.**
1. No painel **'Adicionar Atribuição',** selecione o botão **Atribuir.**

## <a name="configure-siteintel-sso"></a>Configure SiteIntel SSO

Para configurar um único sinal no lado do SiteIntel, envie o URL que copiou da url da Federação de **Aplicações** para a equipa de [suporte do SiteIntel](mailto:support@intalytics.com). Eles definem este valor para estabelecer a ligação SSO SAML corretamente em ambos os lados.

### <a name="create-a-siteintel-test-user"></a>Criar um utilizador de teste SiteIntel

Nesta secção, cria-se um utilizador chamado *Britta Simon* no SiteIntel. Trabalhe com a [equipa de suporte do SiteIntel](mailto:support@intalytics.com) para adicionar os utilizadores na plataforma SiteIntel. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de acesso Azure AD utilizando o Painel de Acesso.

Quando selecionar o **azulejo SiteIntel** no Painel de Acesso, deverá ser automaticamente inscrito no SiteIntel para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com Diretório Ativo Azure ](./tutorial-list.md)
- [O que são acesso a aplicações e um único acesso com o Azure Ative Directory?](../manage-apps/what-is-single-sign-on.md)
- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)
- [Experimente siteIntel com Azure AD](https://aad.portal.azure.com/)
- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
- [Como proteger o SiteIntel com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)