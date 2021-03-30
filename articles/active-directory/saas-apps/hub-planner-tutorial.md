---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Hub Planner | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Hub Planner.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/12/2020
ms.author: jeedes
ms.openlocfilehash: 2d9dc483a9d60dc395c0aff52b721690d4fc701d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92442444"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hub-planner"></a>Tutorial: Azure Ative Directory integração única (SSO) com Hub Planner

Neste tutorial, você vai aprender a integrar o Hub Planner com o Azure Ative Directory (Azure AD). Quando integrar o Hub Planner com Azure AD, pode:

* Controle em Azure AD que tem acesso ao Hub Planner.
* Ative os seus utilizadores a serem automaticamente inscritos no Hub Planner com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada pelo Hub Planner (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Hub Planner suporta SSO iniciado **SP.**
* Uma vez configurado Hub Planner, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-hub-planner-from-the-gallery"></a>Adicionar Hub Planner da galeria

Para configurar a integração do Hub Planner no Azure AD, precisa adicionar o Hub Planner da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Hub Planner** na caixa de pesquisa.
1. Selecione **o Hub Planner** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-hub-planner"></a>Configurar e testar Azure AD único sinal para Hub Planner

Configure e teste Azure AD SSO com Hub Planner usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Hub Planner.

Para configurar e testar o Azure AD SSO com o Hub Planner, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Hub Planner SSO](#configure-hub-planner-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Hub Planner test user](#create-hub-planner-test-user)** - para ter uma contraparte de B.Simon no Hub Planner que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Hub Planner,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.hubplanner.com`

    b. Na caixa **identifier,** digite um URL utilizando o seguinte padrão: `https://app.hubplanner.com/sso/metadata`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://app.hubplanner.com/sso/callback`

    > [!NOTE]
    > Estes valores são os que vais usar. A única alteração que precisa de fazer é substituir \<SUBDOMAIN\> no **URL de inscrição** pelo subdomínio que recebeu quando se inscreveu no Hub Planner. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar o Hub Planner,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Hub Planner.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Hub Planner**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-hub-planner-sso"></a>Configurar hub planner SSO

Para configurar um único sinal no lado **hub planner,** você precisa iniciar sôms na sua conta Hub Planner e completar as seguintes tarefas. 

### <a name="install-the-extension-in-hub-planner"></a>Instale a extensão no Hub Planner

Para ativar a funcionalidade SSO, tem primeiro de ativar a extensão. Como proprietário da conta ou com permissões equivalentes, complete estes passos:

1. Ir para **Definições**.
1. No menu lateral, selecione **Gerir extensões**  >  **Adicionar/Remover Extensões**.
1. Encontre a extensão para signo único e adicionar ou tentar gratuitamente.
1. Quando solicitado, concorde com os termos e condições e, em seguida, **selecione Add Now**.

### <a name="enable-sso"></a>Ativar o SSO

Depois de ativar a extensão, tem de ativar sSO para a sua conta. 

1. Ir para **Definições**.
1. No menu lateral, selecione **Autenticação.**
1. Selecione **SSO (único sinal)**.
1. Introduza informações de autenticação adicionais, como mostrado na imagem seguinte, e, em seguida, **selecione Guardar**.

![Screenshot das definições de SSO](media/hub-planner-tutorial/sso-settings.png)

### <a name="create-hub-planner-test-user"></a>Criar utilizador de teste de Hub Planner

Se quiser adicionar outros utilizadores, vá a **Definições**  >  **Gerir recursos** e adicionar utilizadores a partir daqui. Certifique-se de adicionar o seu endereço de e-mail e convidá-los. Uma vez convidados, receberão um e-mail e poderão entrar via SSO. 

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo hub Planner no Painel de Acesso, deverá ser automaticamente inscrito no Hub Planner para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Hub Planner com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o Hub Planner com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)