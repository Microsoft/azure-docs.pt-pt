---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com o InVision Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o InVision.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/09/2020
ms.author: jeedes
ms.openlocfilehash: 1be5a46afbaa248232ff113cbfb45a8798af77c1
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92459871"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-invision"></a>Tutorial: Azure Ative Directory integração única (SSO) com o InVision

Neste tutorial, você vai aprender a integrar o InVision com O Azure Ative Directory (Azure AD). Quando integra o InVision com AD Azure, pode:

* Control em Azure AD que tem acesso ao InVision.
* Capacitar os seus utilizadores a serem automaticamente inscritos no InVision com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição única ativada inVision (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* InVision suporta SSO iniciado **sp e IDP**
* Uma vez configurado o InVision, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-invision-from-the-gallery"></a>Adicionar InVision da galeria

Para configurar a integração do InVision no AD Azure, é necessário adicionar o InVision da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite InVision** na caixa de pesquisa.
1. Selecione **InVision** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-invision"></a>Configurar e testar a Azure AD um único sign-on para o InVision

Configure e teste Azure AD SSO com InVision usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no InVision.

Para configurar e testar o Azure AD SSO com o InVision, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure InVision SSO](#configure-invision-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create InVision test user](#create-invision-test-user)** - para ter uma contraparte de B.Simon no InVision que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **InVision,** encontre a secção **Gerir** e selecione **um único sinal de sing.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.invisionapp.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.invisionapp.com//sso/auth`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<SUBDOMAIN>.invisionapp.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de suporte do Cliente InVision](mailto:support@invisionapp.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar InVision,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao InVision.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **InVision**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-invision-sso"></a>Configurar inVision SSO

1. Numa janela diferente do navegador web, inscreva-se no site do InVision como administrador.

1. Clique em **Equipa** e selecione **Definições**.

    ![A screenshot mostra o separador Equipa com Definições selecionadas.](./media/invision-tutorial/config1.png)

1. Desloque-se para baixo para **um único sinal de inscrição** e, em seguida, clique em **Alterar**.

    ![A screenshot mostra o botão De alteração para um único sinal de inscrição.](./media/invision-tutorial/config3.png)

1. Na página **de inscrição única,** execute os seguintes passos:

    ![A screenshot mostra a página de assinatura única onde introduz os valores neste passo.](./media/invision-tutorial/config4.png)

    a. Alterar **Requer SSO para cada membro da < nome da conta >** para **On**.

    b. Na caixa de texto **do nome,** insira o nome por exemplo como `azureadsso` .

    c. Introduza o valor URL de entrada na caixa de texto **URL de entrada** de inscrição.

    d. Na caixa de texto **URL de inscrição,** cole o valor URL **de Log out,** que copiou a partir do portal Azure.

    e. Na caixa de texto **do Certificado SAML,** abra o Certificado descarregado **(Base64)** no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto do Certificado SAML.

    f. Na caixa de texto **do formato ID Name,** utilize `Unspecified` para o formato **ID name**.

    exemplo, Selecione **SHA-256** a partir do dropdown para o **algoritmo HASH**.

    h. Introduza o nome apropriado para a **etiqueta de botão SSO**.

    i. Faça **permitir o provisionamento just-in-time** on.

    j. Clique em **Atualizar**.

### <a name="create-invision-test-user"></a>Criar utilizador de teste InVision

1. Numa janela diferente do navegador web, inscreva-se no site do InVision como administrador.

1. Clique em **Equipa** e selecione **Pessoas.**

    ![A screenshot mostra o separador Equipa com pessoas selecionadas.](./media/invision-tutorial/config2.png)

1. Clique no **+ ICON** para adicionar novo utilizador.

    ![A screenshot mostra o ícone + para adicionar um utilizador.](./media/invision-tutorial/user1.png)

1. Introduza o endereço de e-mail do utilizador e clique em **Seguinte**.

    ![A screenshot mostra a caixa de diálogo Convite para dialogar onde pode introduzir endereços.](./media/invision-tutorial/user2.png)

1. Uma vez que verifique o endereço de e-mail e, em seguida, clique em **Convidar**.

    ![A screenshot mostra o diálogo do Convite onde pode selecionar Convidar a prosseguir.](./media/invision-tutorial/user3.png)

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo InVision no Painel de Acesso, deverá ser automaticamente inscrito no InVision para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente InVision com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o InVision com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)