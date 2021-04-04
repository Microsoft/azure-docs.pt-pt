---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com 8x8 | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o 8x8.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 81a7efea268600e661981b35f79149fe814ef084
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180681"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-8x8"></a>Tutorial: Azure Ative Directy integração única (SSO) com 8x8

Neste tutorial, você vai aprender a integrar 8x8 com Azure Ative Directory (Azure AD). Quando integrar o 8x8 com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a 8x8.
* Permita que os seus utilizadores sejam automaticamente inscritos no 8x8 com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura 8x8.

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* 8x8 suporta **SP e IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-8x8-from-the-gallery"></a>Adicionando 8x8 da galeria

Para configurar a integração do 8x8 no AD Azure, é necessário adicionar 8x8 da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** digite **8x8** na caixa de pesquisa.
1. Selecione **8x8** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-8x8"></a>Configurar e testar Azure AD SSO para 8x8

Configure e teste Azure AD SSO com 8x8 usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em 8x8.

Para configurar e testar a Azure AD SSO com 8x8, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure 8x8 SSO](#configure-8x8-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Crie o utilizador de teste 8x8](#create-8x8-test-user)** - para ter uma contraparte de B.Simon em 8x8 que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **8x8,** encontre a secção **Gerir** e selecione **um único sinal de sing.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **identifier,** digite um URL: `https://sso.8x8.com/saml2`

    b. Na caixa de texto **URL de resposta,** digite um URL: `https://sso.8x8.com/saml2`

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador. Utilizará o certificado mais tarde no tutorial na secção **Configure 8x8 SSO.**

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar 8x8,** copie os URL(s) e utilizará estes valores de URL mais tarde no tutorial.

    ![URLs de configuração de cópia](./media/8x8virtualoffice-tutorial/copy-configuration-urls.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao 8x8.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **8x8**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-8x8-sso"></a>Configurar 8x8 SSO

A próxima parte do tutorial depende do tipo de subscrição que tem com 8x8.

* Para 8x8 Edições e clientes da Série X utilizando o Gestor de Configuração para administração, consulte a [Consola 8x8 Admin](#configure-8x8-admin-console).
* Para clientes do Gabinete Virtual que utilizem o Gestor de Conta para administração, consulte o Gestor de [Conta 8x8](#configure-8x8-account-manager).

### <a name="configure-8x8-admin-console"></a>Configurar consola de administração 8x8

1. Para automatizar a configuração dentro do 8x8, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar 8x8** irá direcioná-lo para a aplicação 8x8. A partir daí, forneça as credenciais de administração para assinar em 8x8. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar o 8x8 manualmente, inscreva-se na [Consola Admin](https://admin.8x8.com/) 8x8 como administrador.

1. A partir da página inicial clique **em Gestão de Identidade.**

    ![Screenshot que destaca o azulejo de Gestão de Identidade.](./media/8x8virtualoffice-tutorial/configure1.png)

1. Verifique **o signo único ligado (SSO)** e, em seguida, selecione **Microsoft Azure AD**.

    ![Screenshot que realça as opções de Signo Único (SSO) e Microsoft Azure AD.](./media/8x8virtualoffice-tutorial/configure2.png)

1. Copie os três URLs e assine o certificado de assinatura do **Single Sign-On configurado com** a página SAML em AD Azure na secção Microsoft **Azure AD SAML Settings** na Consola de Administração 8x8.

    ![Consola 8x8 Admin](./media/8x8virtualoffice-tutorial/configure3.png)

    a. Copie **URL de login** para URL de login do **IDP**.

    b. Copiar **O identificador AD Ad** para **o URL/URN do Emitente IDP**.

    c. Copiar **URL logout** para **URL de logout IDP.**

    d. Baixar **Certificado (Base64)** e fazer o upload para **o Certificado.**

    e. Clique em **Guardar**.

### <a name="configure-8x8-account-manager"></a>Configurar gestor de conta 8x8

1. Inscreva-se no seu inquilino do 8x8 Virtual Office como administrador.

1. Selecione **Mgr de Conta de Escritório Virtual** no Painel de Aplicações.

    ![Screenshot que destaca o azulejo da Conta de Escritório Virtual Mgr.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. Selecione **conta Negócio** para gerir e clicar No botão **'Iniciar'.**

    ![Screenshot que realça a opção Negócio e o botão Iniciar.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. Clique no separador **CONTAS** na lista de menus.

    ![Screenshot que realça o separador CONTAS na lista de menus.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. Clique **em 'S sign's Single On** na lista de Contas.

    ![Screenshot que realça a opção Single Sign On.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. Selecione **single sign on** sob métodos de autenticação e clique em **SAML**.

    ![Screenshot que realça o SAML sob single sign on.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. Na placa **única SAML na** secção, execute os seguintes passos:

    ![Configurar no lado da app](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. Na caixa de texto **do Sign In URL,** cole o valor URL do **Login** que copiou do portal Azure.

    b. Na caixa de texto **url sign out,** cole o valor **URL logout** que copiou a partir do portal Azure.

    c. Na caixa de texto URL do **emitente,** cole o valor **do identificador Azure AD** que copiou do portal Azure.

    d. Clique no botão **Browse** para fazer o upload do certificado que descarregou a partir do portal Azure.

    e. Clique no botão **Guardar**.

### <a name="create-8x8-test-user"></a>Criar utilizador de teste 8x8

Nesta secção, cria-se um utilizador chamado Britta Simon em 8x8. Trabalhe com [a equipa de suporte 8x8](https://www.8x8.com/about-us/contact-us) para adicionar os utilizadores na plataforma 8x8. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para 8x8 Assinar no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de inscrição 8x8 e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no 8x8 para o qual configura o SSO 

Também pode utilizar o Microsoft Access Panel para testar a aplicação em qualquer modo. Quando clicar no azulejo 8x8 no Painel de Acesso, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no 8x8 para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado 8x8, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).