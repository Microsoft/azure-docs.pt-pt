---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com SendSafely Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o SendSafely.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: jeedes
ms.openlocfilehash: c43756e7526a260f238ed6e9d0c1f253041b05e7
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92674406"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sendsafely"></a>Tutorial: Azure Ative Directory integração única (SSO) com SendSafely

Neste tutorial, você vai aprender a integrar o SendSafely com o Azure Ative Directory (Azure AD). Quando integrar o SendSafely com a AD Azure, pode:

* Controle em Azure AD que tem acesso ao SendSafely.
* Ativar os seus utilizadores a serem automaticamente inscritos no SendSafely com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Enviar Subscrição ativada por Um sinal único (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* SendSafely suporta **SP e IDP** iniciado SSO
* SendSafely suporta o fornecimento do utilizador **Just In Time**

* Uma vez configurado o SendSafely, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-sendsafely-from-the-gallery"></a>Adicionar SendSafely da galeria

Para configurar a integração do SendSafely no AD Azure, é necessário adicionar o SendSafely da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações** .
1. Para adicionar nova aplicação, selecione **Nova aplicação** .
1. Na secção Adicionar a partir da secção **de galeria,** **digite SendSafely** na caixa de pesquisa.
1. Selecione **SendSafely** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sendsafely"></a>Configurar e testar Azure AD único sinal de inscrição para SendSafely

Configure e teste Azure AD SSO com SendSafely usando um utilizador de teste chamado **B.Simon** . Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no SendSafely.

Para configurar e testar o Azure AD SSO com o SendSafely, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o SendSafely SSO](#configure-sendsafely-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create SendSafely test user](#create-sendsafely-test-user)** - para ter uma contraparte de B.Simon em SendSafely que está ligada à representação AD AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **SendSafely,** encontre a secção **Gerir** e selecione **um único sinal de sação** .
1. Na página de método **de inscrição** única, selecione **SAML** .
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<SENDSAFELY_URL>/auth/saml2/`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<SENDSAFELY_URL>/auth/saml2/`

1. Clique **em Definir URLs adicionais** e execute os seguintes passos se desejar configurar a aplicação no modo iniciado **sp:**

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<SENDSAFELY_URL>/auth/`

    b. Na caixa de texto **do Estado de retransmissão,** digite um URL utilizando o seguinte padrão: `https://<SENDSAFELY_URL>/auth/saml2/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta, URL de inscrição e Estado de retransmissão. Contacte [a equipa de suporte do Cliente SendSafely](mailto:support@sendsafely.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configuração Enviar Segurança,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory** , selecione **Utilizadores** , e, em seguida, selecione **Todos os utilizadores** .
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome** , introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon utilize o Azure single sign-on, concedendo acesso ao SendSafely.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações** .
1. Na lista de aplicações, selecione **SendSafely** .
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos** .

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-sendsafely-sso"></a>Configurar SendSafely SSO

Para configurar um único sinal no lado **SendSafely,** siga os passos previstos [aqui](https://sendsafely.zendesk.com/hc/articles/360004152492-Setup-Single-Sign-On-SSO-with-SAML).

### <a name="create-sendsafely-test-user"></a>Criar utilizador de teste SendSafely

Nesta secção, um utilizador chamado B.Simon é criado em SendSafely. O SendSafely suporta o provisionamento just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no SendSafely, um novo é criado quando tenta aceder ao SendSafely.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SendSafely no Painel de Acesso, deverá ser automaticamente inscrito no SendSafely para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Tente SendSafely com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o SendSafely com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)