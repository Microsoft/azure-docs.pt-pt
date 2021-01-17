---
title: 'Tutorial: Integração do Azure Ative Directory com o administrador de acesso à Internet Zscaler | Microsoft Docs'
description: Saiba como configurar um único sinal de acesso entre o Azure Ative Directory e o Administrador de Acesso à Internet Zscaler.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: c3a2f2b04827fac06a0729e45b46765928aedd34
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539784"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>Tutorial: Integração do Azure Ative Directory com o Administrador de Acesso à Internet Zscaler

Neste tutorial, você vai aprender a integrar o Administrador de Acesso à Internet Zscaler com o Azure Ative Directory (Azure AD). Quando integrar o Administrador de Acesso à Internet Zscaler com Azure AD, pode:

* Controle em Azure AD que tem acesso ao Administrador de Acesso à Internet Zscaler.
* Habilifique os seus utilizadores a serem automaticamente inscritos no Administrador de Acesso à Internet da Zscaler com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o Administrador de Acesso à Internet Zscaler, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do Administrador de Acesso à Internet Zscaler

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Administrador de acesso à Internet Zscaler suporta **IDP** iniciado SSO

## <a name="adding-zscaler-internet-access-administrator-from-the-gallery"></a>Adicionar administrador de acesso à Internet Zscaler da galeria

Para configurar a integração do Administrador de Acesso à Internet Zscaler em Azure AD, é necessário adicionar o Administrador de Acesso à Internet Zscaler da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Zscaler Internet Access Administrator** na caixa de pesquisa.
1. Selecione o Administrador de **Acesso à Internet Zscaler** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-internet-access-administrator"></a>Configure e teste Azure AD SSO para administrador de acesso à Internet Zscaler

Configure e teste Azure AD SSO com o Administrador de Acesso à Internet Zscaler usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Zscaler Internet Access Administrator.

Para configurar e testar o Azure AD SSO com o Administrador de Acesso à Internet Zscaler, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
2. **[Configure o Zscaler Internet Access Administrator SSO](#configure-zscaler-internet-access-administrator-sso)** - para configurar as definições de Sign-On únicas no lado da aplicação.
    1. **[Create Zscaler Internet Access Administrator test user](#create-zscaler-internet-access-administrator-test-user)** - para ter uma contraparte de Britta Simon em Zscaler Internet Access Administrator que está ligada à representação AD Ad do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Zscaler Internet Access Administrator,** encontre a secção **Gerir** e selecione **'S-on' único**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com página SAML,** clique no ícone de lápis para **configuração SAML básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier,** digite um URL de acordo com o seu requisito:

    | Identificador |
    |------------|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` |
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. Na caixa de texto **URL de resposta,** digite um URL de acordo com o seu requisito:

    | URL de Resposta |
    |-----------|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` |
    | `https://admin.zscalertwo.net/adminsso.do` |
    | `https://admin.zscalerthree.net/adminsso.do` |
    | `https://admin.zscloud.net/adminsso.do` |
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. A aplicação Zscaler Internet Access Administrator espera as afirmações SAML num formato específico. Configure os seguintes pedidos para este pedido. Pode gerir os valores destes atributos a partir da secção **Atributos & Reclamações** do Utilizador na página de integração de aplicações. Na **configuração de Sign-On única com a página SAML,** clique no botão **Editar** para abrir **os atributos do utilizador & o** diálogo Claims.

    ![O link Atributo](./media/zscaler-internet-access-administrator-tutorial/tutorial_zscaler-internet_attribute.png)

6. Na secção **'Reclamações** de Utilizador' no diálogo **'Atributos do Utilizador',** configurar o atributo de ficha SAML como mostrado na imagem acima e executar os seguintes passos:

    | Name  | Atributo de origem  |
    | ---------| ------------ |
    | Função | user.assignedroles |

    a. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    b. A partir da lista **de atributos Fonte,** selecione o valor do atributo.

    c. Clique em **OK**.

    d. Clique em **Guardar**.

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui) para saber como configurar o Papel em Azure AD.

7. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

8. Na secção **De Administrador de Acesso à Internet Do Zscaler,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Administrador de Acesso à Internet Zscaler.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Zscaler Internet Access Administrator**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se tiver configurado as funções como explicado no acima, pode selecioná-lo a partir do Dropdown de **função Select.**
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**


## <a name="configure-zscaler-internet-access-administrator-sso"></a>Configure Zscaler Administrador de Acesso à Internet SSO

1. Numa janela diferente do navegador web, inicie sessão no seu Zscaler Internet Access Admin UI.

2. Vá à **Administração > Gestão de Administradores** e execute os seguintes passos e clique em Guardar:

    ![O Screenshot mostra a Gestão do Administrador com opções para Ativar a Autenticação SAML, carregar o Certificado S L e especificar um Emitente.](./media/zscaler-internet-access-administrator-tutorial/AdminSSO.png "Administração")

    a. Verifique **a autenticação SAML**.

    b. Clique em **Upload**, para carregar o certificado de assinatura Azure SAML que descarregou a partir do portal Azure no **Certificado SSL Público**.

    c. Opcionalmente, para segurança adicional, adicione os detalhes do **Emitente** para verificar o Emitente da resposta SAML.

3. Na UI administrador, execute os seguintes passos:

    ![A screenshot mostra o Administrador U I onde podes executar os passos.](./media/zscaler-internet-access-administrator-tutorial/ic800207.png)

    a. Passe por cima do menu **de ativação** perto do canto inferior esquerdo.

    b. Clique **em Ativar**.

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Criar utilizador de teste de administrador de acesso à Internet Zscaler

O objetivo desta secção é criar um utilizador chamado Britta Simon em Zscaler Internet Access Administrator. O Zscaler Internet Access não suporta o fornecimento de Just-In-Time para o Administrador SSO. É-lhe exigido que crie manualmente uma conta administrador.
Para obter etapas sobre como criar uma conta de administrador, consulte a documentação Zscaler:

https://help.zscaler.com/zia/adding-admins

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no Administrador de Acesso à Internet Zscaler para o qual configura o SSO

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo do Administrador de Acesso à Internet Zscaler nas Minhas Aplicações, deverá ser automaticamente inscrito no Administrador de Acesso à Internet Zscaler para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Administrador de Acesso à Internet Zscaler, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
