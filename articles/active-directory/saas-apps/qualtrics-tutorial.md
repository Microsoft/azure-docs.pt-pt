---
title: 'Tutorial: Integração do Azure Ative Directory com | SAP Qualtrics Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o SAP Qualtrics.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 2031864ea57a2f061c69219a2382429ee035804b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652545"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-qualtrics"></a>Tutorial: Azure Ative Directory integração única (SSO) com SAP Qualtrics

Neste tutorial, você vai aprender a integrar SAP Qualtrics com Azure Ative Directory (Azure AD). Quando integrar a SAP Qualtrics com Azure AD, pode:

* Controlo em Azure AD que tem acesso a SAP Qualtrics.
* Permita que os seus utilizadores sejam automaticamente inscritos na SAP Qualtrics com as suas contas AD AZure.
* Gerencie as suas contas num local central: o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa do seguinte:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição SAP Qualtrics ativada para uma única sso (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* A SAP Qualtrics suporta **o SP** e **o IDP** iniciado sSO.
* A SAP Qualtrics suporta o fornecimento do utilizador **Just In Time.**

## <a name="add-sap-qualtrics-from-the-gallery"></a>Adicione SAP Qualtrics da galeria

Para configurar a integração da SAP Qualtrics no Azure AD, é necessário adicionar a SAP Qualtrics da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione **Azure Ative Directory**.
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva SAP Qualtrics** na caixa de pesquisa.
1. Selecione **SAP Qualtrics** a partir de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-qualtrics"></a>Configurar e testar Azure AD único sinal para SAP Qualtrics

Configure e teste Azure AD SSO com SAP Qualtrics, utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação ligada entre um utilizador Azure AD e o utilizador relacionado na SAP Qualtrics.

Para configurar e testar o Azure AD SSO com a SAP Qualtrics, complete os seguintes blocos de construção:

1. [Configure Azure AD SSO](#configure-azure-ad-sso) para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. [Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user) para testar o Azure AD com B.Simon.
    1. [Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que a B.Simon utilize um único sinal de Ad AD.
1. [Configure o SAP Qualtrics SSO](#configure-sap-qualtrics-sso) para configurar as definições de inscrição única no lado da aplicação.
    1. [Crie um utilizador de teste SAP Qualtrics](#create-sap-qualtrics-test-user) para ter uma contraparte de B.Simon em SAP Qualtrics, ligada à representação AZure AD do utilizador.
1. [Teste SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **SAP Qualtrics,** encontre a secção **Gerir.** Selecione **um único sinal de inscrição**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, selecione o ícone de lápis para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. No **set-on único com** a página SAML, se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:
    
    a. Na caixa de texto **identifier,** digite um URL que utiliza o seguinte padrão:

    `https://< DATACENTER >.qualtrics.com`
   
    b. Na caixa de texto **URL de resposta,** digite um URL que utiliza o seguinte padrão:

    `https://< DATACENTER >.qualtrics.com/login/v1/sso/saml2/default-sp`

    c. Na caixa de texto **do Estado de Retransmissão,** digite um URL que utiliza o seguinte padrão:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

1. Selecione **Definir URLs adicionais** e executar o seguinte passo se quiser configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **url de acesso,** digite um URL que utiliza o seguinte padrão:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de inscrição real, identificador, URL de resposta e estado de retransmissão. Para obter estes valores, contacte a equipa de apoio ao [Cliente Qualtrics.](https://www.qualtrics.com/support/) Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de sessão com** a página SAML, na secção **Certificado de Assinatura SAML,** selecione o ícone de cópia para copiar o Url de **metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, cria-se um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >    >  **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de senha show** e, em seguida, escreva a palavra-passe para baixo.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que B.Simon utilize a Azure um único sinal, permitindo o acesso à SAP Qualtrics.

1. No portal Azure, selecione **Aplicações empresariais**  >  **Todas as aplicações**.
1. Na lista de candidaturas, selecione **SAP Qualtrics**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. Selecione **Adicionar utilizador**. Em seguida, na caixa de diálogo **de atribuição de adicionar,** selecione **Utilizadores e grupos**.
1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Em seguida, escolha **Selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, escolha **Selecione** na parte inferior do ecrã.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

## <a name="configure-sap-qualtrics-sso"></a>Configurar SAP Qualtrics SSO

Para configurar um único sinal no lado SAP Qualtrics, envie o **url de metadados** copiado da Federação de Aplicações do portal Azure para a equipa de [suporte SAP Qualtrics](https://www.qualtrics.com/support/). A equipa de apoio garante que a ligação SSO SAML está corretamente definida em ambos os lados.

### <a name="create-sap-qualtrics-test-user"></a>Criar utilizador de teste SAP Qualtrics

A SAP Qualtrics suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nenhuma ação adicional para tomar. Se um utilizador já não existir na SAP Qualtrics, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para SAP Qualtrics Sign no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de inscrição em qualícia SAP e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na SAP Qualtrics para a qual configura o SSO.

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo SAP Qualtrics nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na Qualrícia SAP para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Depois de configurar a SAP Qualtrics, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o acesso condicional. Para obter mais informações, consulte [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security.](/cloud-app-security/proxy-deployment-any-app)