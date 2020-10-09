---
title: 'Tutorial: Integração do Azure Ative Directory com Servidor Secreto (No-Local) / Microsoft Docs'
description: Saiba como configurar um único sinal de insuflação entre o Azure Ative Directory e o Secret Server (On-In-In-Ins).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.openlocfilehash: eeaf6917187c9688bf6a62b98b2fcf64519b1798
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88543232"
---
# <a name="tutorial-integrate-secret-server-on-premises-with-azure-active-directory"></a>Tutorial: Integrar servidor secreto (on-premis) com diretório ativo Azure

Neste tutorial, você vai aprender a integrar o Secret Server (On-Premis) com o Azure Ative Directory (Azure AD). Quando integra o Secret Server (On-Premis) com Azure AD, pode:

* Controle em Azure AD que tem acesso ao Secret Server (On-In-Ins).
* Ativar os seus utilizadores a serem automaticamente inscritos no Secret Server (On-Premis) com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada pelo Secret Server (On-In-As-Premis) (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Secret Server (On-Premis) suporta SSO iniciado **sp e IDP**

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>Adicionar Servidor Secreto (Instalações) da galeria

Para configurar a integração do Secret Server (On-Premis) em Azure AD, é necessário adicionar o Secret Server (On-Premis) da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Servidor Secreto (Instalações)** na caixa de pesquisa.
1. Selecione **Secret Server (On-Premis)** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com Servidor Secreto (No-Local) usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Servidor Secreto (No-Local).

Para configurar e testar O SSO Azure AD com servidor secreto (instalações), complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Servidor Secreto (No Local) SSO](#configure-secret-server-on-premises-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
5. **[Create Secret Server (On-Premises) test user](#create-secret-server-on-premises-test-user)** - para ter uma contraparte de B.Simon in Secret Server (On-Premis) que está ligada à representação AD AD do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Secret Server (On-Premis),** encontre a secção **'Gerir'** e selecione **'Único sinal de s-on'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier,** insira o valor escolhido pelo utilizador como exemplo: `https://secretserveronpremises.azure`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx`

    > [!NOTE]
    > O ID da Entidade acima mostrado é apenas um exemplo e você é livre de escolher qualquer valor único que identifique a sua instância do Servidor Secreto em Azure AD. Você precisa enviar este ID de entidade para [a equipe de suporte do cliente Secret Server (On-In-As-As-As- Instalações)](https://thycotic.force.com/support/s/) e eles configuram-no do seu lado. Para mais detalhes, por favor leia [este artigo.](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server)

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<SecretServerURL>/login.aspx`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de resposta real e Sign-On URL. Contacte [o Secret Server (On-Premis) Equipa de suporte ao cliente](https://thycotic.force.com/support/s/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. No conjunto single Sign-On com página **SAML,** clique no ícone **Editar** para abrir o diálogo do Certificado de **Assinatura SAML.**

    ![Opções de assinatura](./media/secretserver-on-premises-tutorial/edit-saml-signon.png)

1. Selecione **a Opção de Assinatura** como resposta e **afirmação de Sign SAML**.

    ![Opções de assinatura](./media/secretserver-on-premises-tutorial/signing-option.png)

1. Na secção Configurar o **Servidor Secreto (Instalações)** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-secret-server-on-premises-sso"></a>Configure servidor secreto (no local) SSO

Para configurar um único sinal no lado do **Servidor Secreto (No local),** é necessário enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a [equipa de suporte do Secret Server (On-Premises).](https://thycotic.force.com/support/s/) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Secret Server (On-Premis).

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Secret Server (On-Premis)**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-secret-server-on-premises-test-user"></a>Criar utilizador de teste do Servidor Secreto (No-Local)

Nesta secção, cria-se um utilizador chamado Britta Simon in Secret Server (On-Premis). Trabalhe com a [equipa de suporte do Secret Server (On-Premis)](https://thycotic.force.com/support/s/) para adicionar os utilizadores na plataforma Secret Server (On-Premis). Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo secretório (No local) no Painel de Acesso, deverá ser automaticamente inscrito no Servidor Secreto (Instalações) para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)