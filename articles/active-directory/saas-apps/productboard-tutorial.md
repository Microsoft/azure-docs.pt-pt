---
title: 'Tutorial: Integração do Azure Ative Directory com | de placa de produto Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o productboard.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.openlocfilehash: 0ccf7832ec569266e6fa16e5f130e3f2f74d6f48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515258"
---
# <a name="tutorial-integrate-productboard-with-azure-active-directory"></a>Tutorial: Integrar o painel de produtos com o Azure Ative Directory

Neste tutorial, você vai aprender a integrar o productboard com Azure Ative Directory (Azure AD). Quando integrar o painel de produtos com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao painel de produtos.
* Permita que os seus utilizadores sejam automaticamente inscritos no productboard com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode ter um mês de teste gratuito [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* subscrição única do quadro de produtos (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* productboard suporta **SP e IDP** iniciado SSO
* productboard suporta **provisão do** utilizador Just In Time

## <a name="adding-productboard-from-the-gallery"></a>Adicionar o painel de produtos da galeria

Para configurar a integração do productboard no Azure AD, é necessário adicionar o productboard da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** escreva **o painel de produto** na caixa de pesquisa.
1. Selecione **o painel** de produtos do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com placa de produto usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no painel de produtos.

Para configurar e testar o Azure AD SSO com o painel de produto, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o quadro de produto SSO](#configure-productboard-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Crie o utilizador](#create-productboard-test-user)** de teste do productboard - para ter uma contraparte de B.Simon no painel de produtos que está ligado à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação do painel de **produto,** encontre a secção **Gerir** e selecione **'Único sinal de súb'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://<projectname>.productboard.com/users/auth/saml/callback`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<projectname>.productboard.com/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de resposta real e Sign-On URL. Contacte [a equipa de suporte do cliente](mailto:support@productboard.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações** e guarde-o no seu computador.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao painel de produtos.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **productboard**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-productboard-sso"></a>Configurar o sSO do productboard

1. Para automatizar a configuração dentro do painel de produtos, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **Configurar o productboard**, que o direciona para a aplicação do productboard. A partir daí, forneça as credenciais de administração para iniciar sinssutar no painel de produtos. A extensão do navegador configurará automaticamente a aplicação para si.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar manualmente um único sinal de inscrição no painel de **produtos,** tem de enviar o **Url de Metadados da Federação de Aplicações** para a equipa de suporte do [painel de produtos.](mailto:support@productboard.com) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-productboard-test-user"></a>Criar utilizador de teste de tabuleiro de produto

Nesta secção, um utilizador chamado Britta Simon é criado em painel de produtos. o productboard suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no painel de produtos, um novo é criado após a autenticação.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do painel de produtos no Painel de Acesso, deverá ser automaticamente inscrito no painel de produto para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o tabuleiro com Azure AD](https://aad.portal.azure.com/)