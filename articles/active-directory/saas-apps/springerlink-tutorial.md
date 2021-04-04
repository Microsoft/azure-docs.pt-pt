---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Springer Link | Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o Springer Link.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: jeedes
ms.openlocfilehash: 9e3080e8179af1437438f84853952a6c362b62b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181330"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-springer-link"></a>Tutorial: Azure Ative Directory integração única (SSO) com Springer Link

Neste tutorial, você vai aprender a integrar Springer Link com Azure Ative Directory (Azure AD). Quando integrar a Springer Link com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Springer Link.
* Ative os seus utilizadores a serem automaticamente inscritos no Link Springer com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição única ativada por Springer Link (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Springer Link suporta **SP e IDP** iniciado SSO

## <a name="adding-springer-link-from-the-gallery"></a>Adicionando Link Springer da galeria

Para configurar a integração do Springer Link no Azure AD, é necessário adicionar springer Link da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Springer Link** na caixa de pesquisa.
1. Selecione **Springer Link** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar Azure AD SSO

Configure e teste Azure AD SSO com Springer Link usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Springer Link.

Para configurar e testar o Azure AD SSO com springer link, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
2. **[Configure o Springer Link SSO](#configure-springer-link-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Springer Link,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier,** digite o URL: `https://fsso.springer.com`

    b. Na caixa de texto **URL de resposta,** digite o URL: `https://fsso.springer.com/federation/Consumer/metaAlias/SpringerServiceProvider`

    c. Clique **em Definir URLs adicionais**.

    d. Na caixa de texto **do Estado de Retransmissão,** digite o URL: `https://link.springer.com`

5. Se desejar configurar a aplicação no modo iniciado pela **SP,** execute o seguinte passo:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://fsso.springer.com/saml/login?idp=<entityID>&targetUrl=https://link.springer.com`

    > [!NOTE]
    > O valor url de inscrição não é real. Atualize o valor com o URL de Sign-On real. `<entityID>` é o identificador AZure AD copiado da secção **set up Springer Link,** descrito mais tarde em tutorial. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique no ícone de cópia para copiar o **Url de metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de metadados](common/copy_metadataurl.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Springer Link.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Springer Link**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-springer-link-sso"></a>Configure a ligação springer SSO

Para configurar um único sinal no lado **springer link,** é necessário enviar o **url de metadados da Federação de Aplicações** copiada para a [equipa de suporte da Springer Link](mailto:onlineservice@springernature.com). A equipa de suporte de ligação Springer utiliza este URL para configurar corretamente a ligação SSO SAML de ambos os lados.

### <a name="create-springer-link-test-user"></a>Criar utilizador de teste de Link Springer

Nesta secção, cria-se um utilizador chamado Britta Simon em Springer Link. Trabalhe com a [equipa de suporte springer Link](mailto:onlineservice@springernature.com) para adicionar os utilizadores na plataforma Springer Link. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o Sinal de Link Springer no URL, onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de acesso de link springer e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Link Springer para o qual configura o SSO 

Também pode utilizar o Microsoft Access Panel para testar a aplicação em qualquer modo. Quando clicar no azulejo do Link Springer no Painel de Acesso, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Link Springer para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos Seguintes

Uma vez configurado Springer Link, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).


- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)