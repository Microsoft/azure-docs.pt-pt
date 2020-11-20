---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com a Netsparker Enterprise Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Netsparker Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: jeedes
ms.openlocfilehash: 629b5f0b4f4d8b4f63e278802d2a36aea77792c4
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981289"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netsparker-enterprise"></a>Tutorial: Azure Ative Directory integração única (SSO) com a Netsparker Enterprise

Neste tutorial, você vai aprender a integrar a Netsparker Enterprise com a Azure Ative Directory (Azure AD). Quando integrar a Netsparker Enterprise com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à Netsparker Enterprise.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Netsparker Enterprise com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Netsparker Enterprise assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Netsparker Enterprise apoia **SP e IDP** iniciado SSO
* Netsparker Enterprise suporta **provisão de** utilizadores just in time

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.


## <a name="adding-netsparker-enterprise-from-the-gallery"></a>Adicionar Netsparker Enterprise da galeria

Para configurar a integração da Netsparker Enterprise em Azure AD, é necessário adicionar a Netsparker Enterprise da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **de galeria,** **digite Netsparker Enterprise** na caixa de pesquisa.
1. Selecione **Netsparker Enterprise** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-netsparker-enterprise"></a>Configure e teste Azure AD SSO para Netsparker Enterprise

Configure e teste Azure AD SSO com a Netsparker Enterprise usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Netsparker Enterprise.

Para configurar e testar a Azure AD SSO com a Netsparker Enterprise, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Netsparker Enterprise SSO](#configure-netsparker-enterprise-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Netsparker Enterprise test user](#create-netsparker-enterprise-test-user)** - para ter uma contraparte de B.Simon na Netsparker Enterprise que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **netsparker Enterprise,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://www.netsparkercloud.com/account/assertionconsumerservice/?spId=<SPID>`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite o URL:  `https://www.netsparkercloud.com/account/ssosignin/`

    > [!NOTE]
    > O valor URL de resposta não é real. Atualizar o valor com o URL de resposta real. Contacte [a equipa de suporte ao cliente da Netsparker Enterprise](mailto:support@netsparker.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure. 

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Netsparker Enterprise,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Netsparker Enterprise.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Netsparker Enterprise**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-netsparker-enterprise-sso"></a>Configure Netsparker Enterprise SSO

1.  Faça login na Netsparker Enterprise como administrador.

1. Aceda às **Definições > único sinal de inscrição**.

1. Na janela **'Sign-On' única,** selecione o **separador Azure Ative Directory.** 

1. Execute os seguintes passos na página seguinte.

    ![Separador Azure Ative Directory](./media/netsparker-enterprise-tutorial/configure-sso.png)

    a. Copie o valor **do Identificador,** cole este valor na caixa de texto **do Identificador** na secção **Configuração Básica SAML** no portal Azure.

    b. Copie o valor **URL de serviço SAML 2.0,** cole este valor na caixa de texto **URL de resposta** na secção **configuração BÁSICA SAML** no portal Azure.

    c. Cole o valor **do Identificador,** que copiou do portal Azure para o campo **identificador IdP.**

    e. Cole o valor **URL de resposta,** que copiou do portal Azure para o campo **PONTO final SAML 2.0.**

    f. Abra o Certificado descarregado **(Base64)** do portal Azure para o Bloco de Notas e cole o conteúdo na caixa de texto **do Certificado x.509.**

    exemplo, Verifique **ativar o provisionamento automático** e **exija que as afirmações DE SAML sejam encriptadas** conforme necessário.

    h. Clique em **Guardar Alterações**.

### <a name="create-netsparker-enterprise-test-user"></a>Criar utilizador de teste netsparker Enterprise

Nesta secção, um utilizador chamado Britta Simon é criado na Netsparker Enterprise. A Netsparker Enterprise suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na Netsparker Enterprise, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de assinatura da Empresa Netsparker, onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de assinatura da Empresa Netsparker e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na Netsparker Enterprise para a qual configura o SSO 

Também pode utilizar o Microsoft Access Panel para testar a aplicação em qualquer modo. Quando clicar no azulejo netsparker Enterprise no Painel de Acesso, se configurado no modo SP, será redirecionado para a página de início da inscrição para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na Netsparker Enterprise para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado a Netsparker Enterprise, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


