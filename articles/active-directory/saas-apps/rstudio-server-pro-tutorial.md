---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com A autenticação RStudio Server Pro SAML Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o RStudio Server Pro SAML Authentication.
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
ms.openlocfilehash: ecefc7c585f2f556e76efe6a3a272e38de98e297
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181565"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-rstudio-server-pro-saml-authentication"></a>Tutorial: Azure Ative Directory integração única (SSO) com autenticação RStudio Server Pro SAML

Neste tutorial, você vai aprender a integrar A autenticação RStudio Server Pro SAML com Azure Ative Directory (Azure AD). Quando integrar a autenticação RStudio Server Pro SAML com AD Azure, pode:

* Control em Azure AD que tem acesso à Autenticação RStudio Server Pro SAML.
* Ativar os seus utilizadores a serem automaticamente inscritos na Autenticação RStudio Server Pro SAML com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* RStudio Server Pro SAML Authentication single sign-on (SSO) ativada por subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* RStudio Server Pro SAML Authentication suporta SSO iniciado **SP e IDP**

## <a name="adding-rstudio-server-pro-saml-authentication-from-the-gallery"></a>Adicionar autenticação RStudio Server Pro SAML da galeria

Para configurar a integração da Autenticação RStudio Server Pro SAML em AD Azure, é necessário adicionar autenticação RStudio Server Pro SAML da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite RStudio Server Pro SAML Authentication** na caixa de pesquisa.
1. Selecione **RStudio Server Pro SAML Autenticação** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-rstudio-server-pro-saml-authentication"></a>Configurar e testar Azure AD SSO para autenticação RStudio Server Pro SAML

Configure e teste Azure AD SSO com A autenticação RStudio Server Pro SAML utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Autenticação RStudio Server Pro SAML.

Para configurar e testar O SSO Azure AD com autenticação RStudio Server Pro SAML, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure RStudio Server Pro SAML Authentication SSO](#configure-rstudio-server-pro-saml-authentication-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. Criar utilizador de teste de **[autenticação RStudio Server Pro SAML](#create-rstudio-server-pro-saml-authentication-test-user)** - para ter uma contraparte de B.Simon no RStudio Server Pro SAML Authentication que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da **aplicação de autenticação RStudio Server Pro SAML,** encontre a secção **'Gerir'** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.rstudioservices.com/<PATH>/saml/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.rstudioservices.com/<PATH>/saml/acs`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<SUBDOMAIN>.rstudioservices.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de suporte ao cliente de autenticação RStudio Server Pro SAML](mailto:support@rstudio.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

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

## <a name="configure-rstudio-server-pro-saml-authentication-sso"></a>Configurar RStudio Server Pro SAML Authentication SSO

Para configurar um único sinal de inscrição no lado **de autenticação RStudio Server Pro SAML,** é necessário enviar o Url de **Metadados da Federação de Aplicações** para a equipa de suporte à [autenticação RStudio Server Pro SAML](mailto:support@rstudio.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-rstudio-server-pro-saml-authentication-test-user"></a>Criar utilizador de teste de autenticação RStudio Server Pro SAML

Nesta secção, cria um utilizador chamado B.Simon in RStudio Server Pro SAML Authentication. Trabalhe com a equipa de [suporte de autenticação RStudio Server Pro SAML](mailto:support@rstudio.com) para adicionar os utilizadores na plataforma de autenticação RStudio Server Pro SAML. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

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