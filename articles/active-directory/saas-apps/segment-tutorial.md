---
title: 'Tutorial: Azure Ative Directy integração única de sign-on (SSO) com segmento | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Segment.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2020
ms.author: jeedes
ms.openlocfilehash: fe8acfd1bfd14f339a0109cab215b8a9ab65256f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021559"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-segment"></a>Tutorial: Azure Ative Directy integração única (SSO) com Segmento

Neste tutorial, você vai aprender a integrar o Segmento com O Diretório Ativo Azure (Azure AD). Quando integra o Segmento com AD Azure, pode:

* Controlo em Azure AD que tem acesso ao Segmento.
* Capacitar os seus utilizadores a serem automaticamente inscritos no Segmento com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição única de segmento (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Segmento suporta **SP e IDP** iniciado SSO
* Segmento suporta **provisão do** utilizador Just In Time

* Uma vez configurado Segmento pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-segment-from-the-gallery"></a>Adicionar Segmento da galeria

Para configurar a integração do Segmento em AD Azure, é necessário adicionar Segmento da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** escreva **segmento** na caixa de pesquisa.
1. Selecione **Segmento** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-segment"></a>Configurar e testar Azure AD único sinal para segmento

Configure e teste Azure AD SSO com Segmento utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Segmento.

Para configurar e testar o Azure AD SSO com segmento, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure segmento SSO](#configure-segment-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Segment test user](#create-segment-test-user)** - ter uma contraparte de B.Simon no Segmento que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **do Segmento,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `urn:auth0:segment-prod:samlp-<CUSTOMER_VALUE>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://segment-prod.auth0.com/login/callback?connection=<CUSTOMER_VALUE>`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite o URL:  `https://app.segment.com`

    > [!NOTE]
    > Estes valores são espaços reservados. Você precisa usar o identificador real, URL de resposta e URL de inscrição. Os passos para obter estes valores são descritos mais tarde neste tutorial.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Segmento de Configuração,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Segmento.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Segmento.**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-segment-sso"></a>Segmento de configuração SSO

1. Numa nova janela do navegador web, inscreva-se no site da empresa do seu Segmento como administrador.

1. Clique no **Ícone de Definições** e desloque-se para baixo para **AUTENTICAÇÃO** e clique em **Conexões**.

    ![Screenshot que mostra o ícone "Definições" selecionado e "Conexões" selecionados no menu "Autenticação".](./media/segment-tutorial/segment1.PNG)

1. Clique em **Adicionar nova ligação.**

    ![Screenshot que mostra a secção "Ligações" com o botão "Adicionar nova ligação" selecionado.](./media/segment-tutorial/segment2.PNG)

1. Selecione **SAML 2.0** como uma ligação à configuração e clique no botão **Select Connection.**

    ![Screenshot que mostra a secção "Escolha uma Ligação" com "S A M L 2.0" e o botão "Selecione Connection" selecionado.](./media/segment-tutorial/segment3.PNG)

1. Na página seguinte, execute os seguintes passos:

    ![Screenshot que mostra a página "Configure Identity Provider" com as caixas de texto "Single Sign-On U R L" e "Audience U R L" e o botão "Seguinte" selecionado.](./media/segment-tutorial/segment4.PNG)

    a. Copie o valor **DE URL de Sign-On único** e cole-o na caixa URL de **resposta** na caixa de diálogo **de configuração SAML básica** no portal Azure.

    b. Copie o valor URL***** do público e cole-o na caixa **de URL do Identificador** na caixa de diálogo **de configuração SAML básica** no portal Azure.

    c. Clique em **Seguinte**.

    ![Configuração do segmento](./media/segment-tutorial/segment5.PNG)

1. Na caixa **URL DE ponto final SAML 2.0,** cole o valor URL de **login** que copiou do portal Azure.

1. Abra o **Cerificate descarregado (Base64)** do portal Azure para o Bloco de Notas e cole o conteúdo na caixa de texto **do Certificado Público.**

1. Clique em **Configurar A Ligação**.

### <a name="create-segment-test-user"></a>Criar utilizador de teste de segmento

Nesta secção, um utilizador chamado B.Simon é criado em Segmento. O segmento suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Segmento, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Segmento no Painel de Acesso, deverá ser automaticamente inscrito no Segmento para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Segmento com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o Segmento com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)