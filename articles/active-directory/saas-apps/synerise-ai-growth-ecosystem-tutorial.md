---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com o Ecossistema de Crescimento da AI Synerise Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Synerise AI Growth Ecosystem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/15/2020
ms.author: jeedes
ms.openlocfilehash: 988ec89f1ca9f3f88e92ed226c0bf6d81fae8397
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97676059"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-synerise-ai-growth-ecosystem"></a>Tutorial: Azure Ative Directory integração única (SSO) com o Ecossistema de Crescimento de AI Synerise

Neste tutorial, você aprenderá a integrar o Ecossistema de Crescimento Synerise AI com O Diretório Ativo Azure (Azure AD). Quando integrar o Ecossistema de Crescimento synerise AI com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Ecossistema de Crescimento de AI Synerise.
* Permita que os seus utilizadores sejam automaticamente inscritos no Synerise AI Growth Ecosystem com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Sinerise AI Growth Ecosystem única sign-on (SSO) ativada subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Ecossistema de Crescimento de IA synerise apoia SSO iniciado **SP e IDP**
* Sinaise AI Growth Ecosystem suporta **provisão de** utilizadores just in time

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.


## <a name="adding-synerise-ai-growth-ecosystem-from-the-gallery"></a>Adicionar o Ecossistema de Crescimento sinerise da IA da galeria

Para configurar a integração do Ecossistema de Crescimento synerise AI em Ad Azure, você precisa adicionar o Ecossistema de Crescimento Synerise AI da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva Synerise AI Growth Ecosystem** na caixa de pesquisa.
1. Selecione **Synerise AI Growth Ecosystem** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-synerise-ai-growth-ecosystem"></a>Configure e teste Azure AD SSO para o Ecossistema de Crescimento de AI sinoerizado

Configure e teste Azure AD SSO com o Ecossistema de Crescimento de AI Sinoerise utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Ecossistema de Crescimento de AI Synerise.

Para configurar e testar o Azure AD SSO com o Ecossistema de Crescimento da IA Synerise, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o SSO do Ecossistema de Crescimento de AI](#configure-synerise-ai-growth-ecosystem-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Synerise AI Growth Ecosystem test user](#create-synerise-ai-growth-ecosystem-test-user)** - para ter uma contrapartida de B.Simon no Ecossistema de Crescimento de AI Synerise que está ligada à representação AD AZure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Synerise AI Growth Ecosystem,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://app.synerise.com/api-portal/uauth/saml/auth/<CUSTOMER_PROFILE_HASH>`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://app.synerise.com/api-portal/uauth/saml/auth/<CUSTOMER_PROFILE_HASH>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de resposta real e URL de inscrição. Contacte [a equipa de suporte do Cliente do Ecossistema de Crescimento Synerise AI](mailto:support@synerise.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção Configurar o **Ecossistema de Crescimento de AI Synerise,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Ecossistema de Crescimento synerise AI.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, **selecione Synerise AI Growth Ecosystem**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-synerise-ai-growth-ecosystem-sso"></a>Configure Synerise Synerise Ecossistema de Crescimento SSO

Para configurar um único sign-on no **synerise AI Growth Ecosystem** side, você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para [synerise AI Growth Ecosystem equipe](mailto:support@synerise.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-synerise-ai-growth-ecosystem-test-user"></a>Criar utilizador de teste de ecossistema de crescimento de AI Synerise

Nesta secção, um utilizador chamado Britta Simon é criado no Ecossistema de Crescimento synerise AI. O Ecossistema de Crescimento de AI synerise suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Ecossistema de Crescimento da AI Synerise, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Synerise AI Growth Ecosystem Sign on URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de Sinaise AI Growth Ecosystem E inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Ecossistema de Crescimento de AI synerise para o qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no teisso synerise AI Growth Ecosystem nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Ecossistema de Crescimento de AI sino-sina para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Ecossistema de Crescimento de AI, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


