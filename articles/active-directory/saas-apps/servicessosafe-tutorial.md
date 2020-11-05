---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Services@SoSafe / Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e Services@SoSafe .
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/23/2020
ms.author: jeedes
ms.openlocfilehash: 3fe42dd37bda3bef549570c32018179b88d06957
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93364997"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicessosafe"></a>Tutorial: Azure Ative Directory single sign-on (SSO) integração com Services@SoSafe

Neste tutorial, você vai aprender a Services@SoSafe integrar-se com Azure Ative Direy (Azure AD). Quando se integra Services@SoSafe com a AD Azure, pode:

* Controlo em Azure AD a quem tem acesso Services@SoSafe a .
* Permita que os seus utilizadores sejam automaticamente Services@SoSafe inscritos com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Services@SoSafe assinatura ativada por um único sinal (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Services@SoSafe apoia **SP e IDP** iniciado SSO
* Services@SoSafe suporta **provisão do** utilizador Just In Time

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.


## <a name="adding-servicessosafe-from-the-gallery"></a>Adicionar Services@SoSafe da galeria

Para configurar a integração do Services@SoSafe Azure AD, precisa adicionar Services@SoSafe da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** Services@SoSafe digite na caixa de pesquisa.
1. Selecione Services@SoSafe do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-servicessosafe"></a>Configure e teste Azure AD SSO para Services@SoSafe

Configure e teste Azure AD SSO Services@SoSafe com a utilização de um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Services@SoSafe .

Para configurar e testar a Azure AD SSO Services@SoSafe com, executar os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure ServicesSoSafe SSO](#configure-servicessosafe-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create ServicesSoSafe test user](#create-servicessosafe-test-user)** - para ter uma contraparte de B.Simon em Services@SoSafe que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da Services@SoSafe aplicação, encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure.

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://api.sosafe.de/v1/auth/saml/login/<TENANT_ID>`

    > [!NOTE]
    > O valor url de inscrição não é real. Atualizar o valor com URL de inscrição real. Contacte a [ Services@SoSafe equipa de suporte do Cliente](mailto:support@sosafe.de) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção Services@SoSafe Configuração, copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory** , selecione **Utilizadores** , e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome** , introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Services@SoSafe .

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione Services@SoSafe .
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-servicessosafe-sso"></a>Configure ServiçosSoSafe SSO

1. Numa janela diferente do navegador web, inscreva-se no Services@SoSafe site como administrador.

1. Clique em **Dados Estendidos** e execute os seguintes passos na página seguinte.

    ![Página saml de dados estendido](./media/servicessosafe-tutorial/saml-settings.png)

    a. Cole o valor do valor de ID do inquilino na caixa de texto do **Azure Tenant ID** do portal Azure.

    b. Abra o **Cerificate descarregado (Base64)** do portal Azure para o Bloco de Notas e cole o conteúdo na caixa de texto **certificate.**

    c. Na caixa **URL do Login,** cole o valor URL de **login** que copiou do portal Azure.

    d. Na caixa de **identificador Azure AD,** cole o valor **de ID** da Entidade que copiou do portal Azure.

    e. Na caixa **URL logout,** cole o valor **URL logout** que copiou do portal Azure.

    f. Clique em **SAVE**

### <a name="create-servicessosafe-test-user"></a>Criar serviçosSoSafe utilizador de teste

Nesta secção, é criado um utilizador chamado Britta Simon em Services@SoSafe . Services@SoSafe suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir Services@SoSafe em , um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

1. Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Services@SoSafe Iniciar em URL onde pode iniciar o fluxo de login.  

1. Vá Services@SoSafe diretamente para o URL de inscrição e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no para o Services@SoSafe qual configura o SSO 

Também pode utilizar o Microsoft Access Panel para testar a aplicação em qualquer modo. Quando clicar Services@SoSafe no azulejo no Painel de Acesso, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no para o Services@SoSafe qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Próximos passos

Uma vez Services@SoSafe configurado, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


