---
title: 'Tutorial: Integração do Azure Ative Directory com a SAML 1.1 Token habilitada app LOB | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SAML 1.1 Token ativado pela LOB App.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 477180a6576d52e3386e18b6e2ba12dd33e4d794
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101654417"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Tutorial: Integração do Azure Ative Directory com a SAML 1.1 Token habilitada a aplicação LOB

Neste tutorial, você vai aprender a integrar SAML 1.1 Token habilitado a LOB App com Azure Ative Directory (Azure AD). Quando integra a APLICAÇÃO LOB ativada pela SAML 1.1 com AZure AD, pode:

* Control em Azure AD que tem acesso a SAML 1.1 Token to enabled LOB App.
* Permita que os seus utilizadores sejam automaticamente inscritos na APLICAÇÃO LOB ativada pela SAML 1.1 com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* SAML 1.1 Token ativou a assinatura única ativada (SSO) da App LOB.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* SAML 1.1 Token ativado lob app suporta **SSO** iniciado SP

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="add-saml-11-token-enabled-lob-app-from-the-gallery"></a>Adicione SAML 1.1 Token ativado app LOB da galeria

Para configurar a integração da APP SAML 1.1 Token ativada lob app em Azure AD, você precisa adicionar SAML 1.1 Token habilitado a LOB App da galeria para a sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria, o** tipo **SAML 1.1 Token ativou** a App LOB na caixa de pesquisa.
1. Selecione **SAML 1.1 Token ativou a App LOB** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-saml-11-token-enabled-lob-app"></a>Configure e teste Azure AD SSO para SAML 1.1 Token habilitado a lob app

Configure e teste Azure AD SSO com SAML 1.1 Token ativou a lob app usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Aplicação LOB ativada pela SAML 1.1.

Para configurar e testar o Azure AD SSO com a APP LOB ativada por SAML 1.1 Token, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure SAML 1.1 Token permitiu que a aplicação LOB SSO](#configure-saml-11-token-enabled-lob-app-sso)** - configurasse as definições de inscrição única no lado da aplicação.
    1. **[Criar o UTILIZADOR DE TESTE DE APLICAÇÃO LOB ativado pela SAML 1.1](#create-saml-11-token-enabled-lob-app-test-user)** - ter uma contraparte de B.Simon em SAML 1.1 Token habilitada a LOB App que está ligada à representação AD Ad Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **LOB ativada pela SAML 1.1,** encontre a secção **Gestão** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://your-app-url`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://your-app-url`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte a SAML 1.1 Token permitiu que a equipa de suporte do cliente da LOB App conseguisse estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configuração SAML 1.1 Token ativou** a secção de aplicações LOB, copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à App LOB ativada por SAML 1.1 Token.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **SAML 1.1 Token ativado pela App LOB**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-saml-11-token-enabled-lob-app-sso"></a>Configure SAML 1.1 Token habilitado para a aplicação LOB SSO

Para configurar um único sinal de sação no **lado da Aplicação LOB ativado por SAML 1.1,** é necessário enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de suporte da App LOB ativada pela SAML 1.1 Token. Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-saml-11-token-enabled-lob-app-test-user"></a>Criar utilizador de teste de aplicação LOB ativado SAML 1.1

Nesta secção, cria um utilizador chamado Britta Simon na APP LOB ativada por SAML 1.1 Token. Trabalhar com a SAML 1.1 Token permitiu que a equipa de suporte da LOB App adicionasse os utilizadores na plataforma de aplicações LOB ativada pela SAML 1.1. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de inscrição da aplicação LOB ativado por SAML 1.1, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição da aplicação LOB ativado pela SAML 1.1 e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo da App LOB ativado pela SAML 1.1 nas Minhas Apps, este irá redirecionar para o URL de inscrição da aplicação LOB ativado por SAML 1.1. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado SAML 1.1 Token a Aplicação LOB ativada, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).