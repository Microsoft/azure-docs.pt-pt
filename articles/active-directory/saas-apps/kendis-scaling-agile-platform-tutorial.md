---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Kendis - Azure AD Integration | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Kendis - Azure AD Integration.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/04/2021
ms.author: jeedes
ms.openlocfilehash: 8409a4d897ea9b20528a5b30273819e6962774cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184496"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kendis---azure-ad-integration"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Kendis - Azure AD Integration

Neste tutorial, você vai aprender a integrar Kendis - Azure AD Integration com Azure Ative Directory (Azure AD). Quando integrar a Integração da AD do Azure com a Azure AD, pode:

* Control em Azure AD que tem acesso a Kendis - Azure AD Integration.
* Capacitar os seus utilizadores a serem automaticamente inscritos no Kendis - Azure AD Integration com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Kendis - Azure AD Integração única sign-on (SSO) ativada subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Kendis - Azure AD Integration apoia **SP e IDP** iniciado SSO
* Kendis - Azure AD Integration suporta **provisão de** utilizadores just in time


## <a name="adding-kendis---azure-ad-integration-from-the-gallery"></a>Adicionar Kendis - Azure AD Integração da galeria

Para configurar a integração da Kendis - Azure AD Integration em Azure AD, você precisa adicionar Kendis - AD Integração AD da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **de galeria,** **digite Kendis - Azure AD Integration** na caixa de pesquisa.
1. Selecione **Kendis - Azure AD Integração** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-kendis---azure-ad-integration"></a>Configure e teste Azure AD SSO para Kendis - Integração AD Azure

Configure e teste Azure AD SSO com Kendis - Azure AD Integration usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Integração AD do Kendis - Azure.

Para configurar e testar a Azure AD SSO com kendis - Azure AD Integration, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configurar Kendis-Azure SSO de Integração AD](#configure-kendis-azure-ad-integration-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. Crie Kendis-Azure utilizador de **[teste de integração AD](#create-kendis-azure-ad-integration-test-user)** - para ter uma contrapartida de B.Simon em Kendis - Azure AD Integration que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da **aplicação Kendis - Ad Integration,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.kendis.io`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.kendis.io/login/saml`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<SUBDOMAIN>.kendis.io/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de apoio ao cliente da Azure AD Integration para](mailto:support@kendis.io) obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configuração Kendis - Azure AD Integration,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Integração AD Kendis - Azure.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Kendis - Azure AD Integration**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-kendis-azure-ad-integration-sso"></a>Configurar Kendis-Azure SSO de Integração AD

1. Para automatizar a configuração dentro da Integração AD Dom - Azure, é necessário instalar a **extensão do navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar Kendis - Azure AD Integration** irá direcioná-lo para a aplicação Kendis - Azure AD Integration. A partir daí, forneça as credenciais de administração para assinar na Kendis - Azure AD Integration. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-5.

    ![Configuração de configuração](common/setup-sso.png)

3. Se quiser configurar manualmente a Integração Kendis - AD AD, numa janela diferente do navegador web, inscreva-se no site da empresa Kendis - Ad Integration como administrador.

4. Aceda às **Configurações > SAML**.

    ![definições para configurações SAML](./media/kendis-scaling-agile-platform-tutorial/settings.png)

5. Clique no botão **Editar** na parte inferior da página e execute os seguintes passos.

    ![Configurações SAML](./media/kendis-scaling-agile-platform-tutorial/saml-configuration-settings.png)

    a. Copiar o valor **DO URL de callback,** colar este valor na caixa de texto **URL de resposta** na secção configuração básica do SAML no portal Azure.

    b. No **fornecedor de identidade único sinal na** caixa de texto URL, cole o valor URL de **login** que copiou a partir do portal Azure.

    c.  Na caixa de texto **do Fornecedor de Identidade,** cole o valor **Azure AD Identifier (Entity ID)** que copiou do portal Azure.

    d. Abra o Certificado descarregado **(Base64)** do portal Azure para o Bloco de Notas e cole o conteúdo na caixa de texto **do Certificado X.509.**

    e. **Selecione Grupo Predefinido** da lista de opções. 

    f. Clique em **Guardar**.

### <a name="create-kendis-azure-ad-integration-test-user"></a>Criar Kendis-Azure utilizador de teste de integração de AD

Nesta secção, um utilizador chamado Britta Simon é criado em Kendis - Azure AD Integration. Kendis - Azure AD Integration suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Kendis - Azure AD Integration, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Kendis - Sinal de Integração AD Azure no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao Kendis - Azure AD Integration S-on URL e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na Integração Kendis - AD Azure para a qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo de integração do AD Dom - Azure nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na Integração AD Kendis - Azure para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Kendis - Azure AD Integration, pode impor o controlo de sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).