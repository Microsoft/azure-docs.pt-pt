---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com RStudio Server Pro | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o RStudio Server Pro.
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
ms.openlocfilehash: 490ecb201b91cdbdcdddceecdd2d145d2f9bb815
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100390050"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-rstudio-server-pro"></a>Tutorial: Azure Ative Directory single sign-on (SSO) integração com RStudio Server Pro

Neste tutorial, você vai aprender a integrar RStudio Server Pro (RSP) com Azure Ative Directory (Azure AD). Quando integrar o RSP com AZure AD, pode:

* Controlo em Azure AD que tem acesso a RSP.
* Capacitar os seus utilizadores a serem automaticamente inscritos no RSP com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Instalação RSP (versão >= 1.4).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* RSP apoia **SP e IDP** iniciaM SSO

## <a name="adding-rstudio-server-pro-from-the-gallery"></a>Adicionar RStudio Server Pro da galeria

Para configurar a integração do RSP no AZure AD, é necessário adicionar autenticação RStudio Server Pro SAML da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite RStudio Server Pro SAML Authentication** na caixa de pesquisa.
1. Selecione **RStudio Server Pro SAML Autenticação** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-rstudio-server-pro"></a>Configure e teste Azure AD SSO para RStudio Server Pro

Configure e teste Azure AD SSO com RSP usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em RSP.

Para configurar e testar O SSO Azure AD com autenticação RStudio Server Pro SAML, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure rStudio Server Pro SSO](#configure-rstudio-server-pro-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. Crie o utilizador de **[teste RStudio Server Pro](#create-rstudio-server-pro-test-user)** - para ter uma contraparte de B.Simon no RStudio Server Pro que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da **aplicação de autenticação RStudio Server Pro SAML,** encontre a secção **'Gerir'** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<RSP-SERVER>/<PATH>/saml/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<RSP-SERVER>/<PATH>/saml/acs`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<RSP-SERVER>/<PATH>/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URI real da sua instalação RSP. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** clique no botão de cópia para copiar o Url de **metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

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

Nesta secção, você permitirá que B.Simon utilize o Azure single sign-on, concedendo acesso à Autenticação SAML do RStudio Server Pro SAML.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **RStudio Server Pro SAML Authentication**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-rstudio-server-pro-sso"></a>Configurar rstudio server Pro SSO

1. Atualize o ficheiro de configuração RSP `/etc/rstudio/rserver.conf` com o seguinte:

    ```
    auth-saml=1
    auth-saml-metadata-url=<federation-metadata-URI>
    auth-saml-sp-name-id-format=emailaddress
    auth-saml-sp-attribute-username=NameID
    auth-saml-sp-base-uri=<RSP-Server-URI>
    ```

2. Reiniciar o RSP executando o seguinte:

    ```
    sudo rstudio-server restart
    ```

### <a name="create-rstudio-server-pro-test-user"></a>Criar utilizador de teste RStudio Server Pro

Todos os utilizadores que devem utilizar RSP têm de ser a provisionados no servidor. Pode criar o utilizador com o `useradd` `adduser` ou comando.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para O Sinal de Autenticação SAML do RStudio Server Pro SAML no URL, onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de autenticação de autenticação do RStudio Server Pro SAML e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no RStudio Server Pro SAML Authentication para o qual configura o SSO 

Também pode utilizar o Microsoft Access Panel para testar a aplicação em qualquer modo. Quando clicar no azulejo de autenticação RStudio Server Pro SAML no Painel de Acesso, se configurado no modo SP será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no RStudio Server Pro SAML Autenticação para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado A autenticação RStudio Server Pro SAML, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
