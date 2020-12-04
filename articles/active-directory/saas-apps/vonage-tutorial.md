---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com vonage Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o vonage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/24/2020
ms.author: jeedes
ms.openlocfilehash: 6e702940967262f98ed30642555b7f1cf3d0e7c6
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603632"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-vonage"></a>Tutorial: Azure Ative Directory integração única (SSO) com vonage

Neste tutorial, você vai aprender a integrar vonage com Azure Ative Directory (Azure AD). Quando integrar vonage com Azure AD, você pode:

* Controlo em Azure AD que tem acesso a vonage.
* Permita que os seus utilizadores sejam automaticamente inscritos em vonage com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* assinatura ativada vonage single sign-on (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.


* vonage apoia **SP e IDP** iniciou SSO

## <a name="adding-vonage-from-the-gallery"></a>Adicionando vonage da galeria

Para configurar a integração do vonage no AZure AD, é necessário adicionar vonage da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **da galeria,** **escreva vonage** na caixa de pesquisa.
1. Selecione **vonage** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-vonage"></a>Configure e teste Azure AD SSO para vonage

Configure e teste Azure AD SSO com vonage usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em vonage.

Para configurar e testar a Azure AD SSO com vonage, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure vonage SSO](#configure-vonage-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create vonage test user](#create-vonage-test-user)** - ter uma contraparte de B.Simon em vonage que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **vonage,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `wso2is-<ENVIRONMENT>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://login.auth-<ENVIRONMENT>.vonage.com/accountrecoveryendpoint/saml-translator.jsp?id=<ID>&env=<ENVIRONMENT>&client=Web`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://admin.<ENVIRONMENT>.vocal-<ENVIRONMENT>.com/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de apoio ao cliente vonage](mailto:office@vonage.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **De configuração de vonage,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure um único sinal, concedendo acesso a vonage.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **vonage**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-vonage-sso"></a>Configure vonage SSO

1. Numa janela diferente do navegador web, inscreva-se no site da vonage como administrador.

1. Aceda à **> definições Sign-On única > definições**.

1. Execute os seguintes passos na página seguinte.

    ![Página de Definições de Sign-On única](./media/vonage-tutorial/sso-settings.png)

    a. **Selecione Ativar um único sinal para esta conta**.

    b. Na caixa de texto ID da **Entidade,** cole o valor **identificador Azure AD** que copiou do portal Azure.

    c. Na caixa de texto **URL de entrada de inscrição,** cole o valor URL de **login** que copiou a partir do portal Azure. 

    d. Faça o upload do ficheiro **Certificado (Base64)** que descarregou do portal Azure para o **Certificado de Upload**.

### <a name="create-vonage-test-user"></a>Criar utilizador de teste de vonage

1. Numa janela diferente do navegador web, inscreva-se no site da vonage como administrador.

1. Aceda ao **Sistema telefónico > utilizadores > adicionar novidades**.

    ![adicionar página de utilizador](./media/vonage-tutorial/add-user.png)

1. Adicione os campos necessários na página seguinte e clique em **Guardar**.

    ![adicionar página de formulário de utilizador](./media/vonage-tutorial/add-user-2.png)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o sinal de vonage no URL onde pode iniciar o fluxo de login.  

* Vá diretamente para o URL de inscrição de vonage e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no vonage para o qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo vonage nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no vonage para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Próximos passos

Uma vez configurado vonage, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


