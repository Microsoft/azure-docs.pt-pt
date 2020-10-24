---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Watch by Colors Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Watch by Colors.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: jeedes
ms.openlocfilehash: a1071cd2bcf29760667bfcc2b63c0a193fa3cf68
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521684"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-watch-by-colors"></a>Tutorial: Azure Ative Directory integração única (SSO) com Watch by Colors

Neste tutorial, você vai aprender a integrar Watch by Colors com Azure Ative Directory (Azure AD). Quando integrar o Watch by Colors com Azure AD, pode:

* Controle em Azure AD que tem acesso a Watch by Colors.
* Ative os seus utilizadores a serem automaticamente inscritos para Ver por Cores com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assista por Colors uma única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Watch by Colors suporta **SP e IDP** iniciado SSO

## <a name="adding-watch-by-colors-from-the-gallery"></a>Adicionar Relógio por Cores da galeria

Para configurar a integração do Watch by Colors no AD Azure, é necessário adicionar Watch by Colors da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva Watch by Colors** na caixa de pesquisa.
1. Selecione **Watch by Colors** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-watch-by-colors"></a>Configurar e testar Azure AD único sign-on para Watch by Colors

Configure e teste Azure AD SSO com Relógio por Cores usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Watch by Colors.

Para configurar e testar Azure AD SSO com Relógio por Cores, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Watch by Colors SSO](#configure-watch-by-colors-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Watch by Colors test user](#create-watch-by-colors-test-user)** - para ter uma contraparte de B.Simon in Watch by Colors que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Watch by Colors,** encontre a secção **Gerir** e selecione um único sinal **de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** a aplicação está pré-configurada no modo iniciado pelo **IDP** e os URLs necessários já estão pré-povoados com Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL:  `https://app.colorscorporation.com/login`

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Watch by Colors.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Watch by Colors**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-watch-by-colors-sso"></a>Configure Relógio por Cores SSO

1. Para automatizar a configuração dentro do Watch by Colors, tem de instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique na **configuração Watch by Colors** irá direcioná-lo para a aplicação Watch by Colors. A partir daí, forneça as credenciais de administração para assinar no Watch by Colors. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-5.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o Watch by Colors manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Watch by Colors como administrador e execute os seguintes passos:

4. No canto superior direito da página, clique em **SSO**de Definições de Conta de perfil  >  **Account Settings**  >  **(Signo Único)**.

    ![Ver por Configuração colors](./media/watch-by-colors-tutorial/config01.png)

5. Na página **SSO (Single Sign On),** execute os seguintes passos:

    ![Ver por Configuração colors](./media/watch-by-colors-tutorial/config02.png)

    a. Toggle **Enable SAML** to **ON**.

    b. Na caixa de texto **URL,** cole o **Url de Metadados da Federação,** que copiou do portal Azure.

    c. Clique **em Importar,** em seguida, os seguintes campos são automaticamente povoados automaticamente na página.

    d. Clique em **Guardar**.

### <a name="create-watch-by-colors-test-user"></a>Criar Relógio por Cores test utilizador

Para permitir que os utilizadores de Azure AD inscrevam-se no Watch by Colors, devem ser ateados no Watch by Colors. No Watch by Colors, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se para Ver por Cores como Administrador de Segurança.

1. No canto superior direito da página, clique no **perfil**  >  **Utilizadores**  >  **Adicionar Utilizador**.

    ![Ver por Configuração colors](./media/watch-by-colors-tutorial/config03.png)

1. Na página **'Dados do Utilizador',** execute os seguintes passos:

    ![Ver por Configuração colors](./media/watch-by-colors-tutorial/config04.png)

    a. Na caixa de texto **First Name,** insira o primeiro nome do utilizador como **B**.

    b. Na caixa de texto **Last Name,** insira o último nome do utilizador como **Simon**.

    c. Na caixa de texto **por e-mail,** insira o e-mail do utilizador como `B.Simon@contoso.com` .

    d. Na caixa de texto **da palavra-passe,** introduza a palavra-passe.

    e. Selecione **Permissões de Conta** conforme a sua organização.

    f. Clique em **Guardar**.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Watch by Colors no Painel de Acesso, deverá ser automaticamente inscrito no Relógio por Cores para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente ver por cores com Azure AD](https://aad.portal.azure.com/)