---
title: 'Tutorial: Integração do Azure Ative Directory com AStudio Connect SAML Authentication / Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o RStudio Connect SAML Authentication.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/21/2020
ms.author: jeedes
ms.openlocfilehash: 84e8c7fc1d2655ea0685ac79841a9c467bf766cf
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182398"
---
# <a name="tutorial-azure-active-directory-integration-with-rstudio-connect-saml-authentication"></a>Tutorial: Integração do Diretório Ativo Azure com autenticação RStudio Connect SAML

Neste tutorial, aprende a integrar a Autenticação RStudio Connect SAML com o Azure Ative Directory (Azure AD).
A integração da autenticação RStudio Connect SAML com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso à Autenticação RStudio Connect SAML.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no RStudio Connect SAML Authentication (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a autenticação RStudio Connect SAML, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* RStudio Ligue a autenticação SAML. Há uma [avaliação gratuita de 45 dias.](https://www.rstudio.com/products/connect/)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* RStudio Connect SAML Authentication suporta SSO iniciado **SP e IDP**

* RStudio Connect SAML Authentication suporta **provisão do** utilizador Just In Time

## <a name="adding-rstudio-connect-saml-authentication-from-the-gallery"></a>Adicionar autenticação RStudio Connect SAML da galeria

Para configurar a integração da Autenticação RStudio Connect SAML em Ad AZure, é necessário adicionar a Autenticação RStudio Connect SAML da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite RStudio Connect SAML Authentication** na caixa de pesquisa.
1. Selecione **RStudio Connect SAML Authentication** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-rstudio-connect-saml-authentication"></a>Configure e teste Azure AD SSO para autenticação SAML de ligação RStudio

Configure e teste Azure AD SSO com RStudio Connect SAML Authentication usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Autenticação SAML RStudio Connect.

Para configurar e testar O SSO Azure AD com Autendio Connect SAML Authentication, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
2. **[Configure RStudio Connect SAML Authentication SSO](#configure-rstudio-connect-saml-authentication-sso)** - para configurar as definições de Sign-On únicas no lado da aplicação.
    * Criar utilizador de teste de **[autenticação RStudio Connect SAML](#create-rstudio-connect-saml-authentication-test-user)** - para ter uma contraparte de Britta Simon em RStudio Connect SAML Authentication que está ligada à representação AD Ad Azure do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da **aplicação RStudio Connect SAML Authentication,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos, substituindo `<example.com>` pelo endereço e porta do servidor de autenticação RStudio Connect SAML:

    ![RStudio Connect SAML Authentication Domain and URLs informações únicas de assinatura](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<example.com>/__login__/saml`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<example.com>/__login__/saml/acs`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![RStudio Connect SAML Authentication metadados carregados](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<example.com>/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. São determinados a partir do endereço do servidor de autenticação SAML RStudio Connect `https://example.com` (nos exemplos acima). Contacte a [equipa de suporte para autenticação RStudio Connect SAML](mailto:support@rstudio.com) se tiver problemas. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. A sua aplicação de autenticação RStudio Connect SAML espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de ficha SAML. A imagem seguinte mostra a lista de atributos predefinidos, onde como **identificador** de nome é mapeado com **user.userprincipalname**. A aplicação RStudio Connect SAML Authentication espera que **o identificador** de nomes seja mapeado com **o user.mail**, pelo que é necessário editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

7. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações** e guarde-o no seu computador.

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

Nesta secção, você permitirá que B.Simon utilize o único sinal de Azure, permitindo o acesso à Autenticação SAML RStudio Connect.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **RStudio Connect SAML Authentication**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-rstudio-connect-saml-authentication-sso"></a>Configure RStudio Connect SAML Authentication SSO

Para configurar um único sinal de insuflação para **a autenticação RStudio Connect SAML,** é necessário utilizar o Url de **Metadados da Federação de Aplicações** e o **Endereço do Servidor** utilizado acima. Isto é feito no ficheiro de configuração de autenticação RStudio Connect SAML em `/etc/rstudio-connect.rstudio-connect.gcfg` .

Este é um ficheiro de configuração de exemplo:

```
[Server]
SenderEmail =

; Important! The user-facing URL of your RStudio Connect SAML Authentication server.
Address = 

[Http]
Listen = :3939

[Authentication]
Provider = saml

[SAML]
Logging = true

; Important! The URL where your IdP hosts the SAML metadata or the path to a local copy of it placed in the RStudio Connect SAML Authentication server.
IdPMetaData = 

IdPAttributeProfile = azure
SSOInitiated = IdPAndSP
```

Guarde o **seu Endereço de Servidor** no valor e o Url de `Server.Address` **Metadados da Federação de Aplicações** no `SAML.IdPMetaData` valor. Note que esta configuração da amostra utiliza uma ligação HTTP não encriptada, enquanto a Azure AD requer a utilização de uma ligação HTTPS encriptada. Pode utilizar um [representante inverso](https://docs.rstudio.com/connect/admin/proxy/) em frente à Autenticação RStudio Connect SAML ou configurar a autenticação RStudio Connect SAML para utilizar [https diretamente](https://docs.rstudio.com/connect/admin/appendix/configuration/#HTTPS). 

Se tiver problemas com a configuração, pode ler o Guia de [Administração de Autenticação RStudio Connect SAML](https://docs.rstudio.com/connect/admin/authentication/saml/) ou enviar ajuda à equipa de suporte do [RStudio.](mailto:support@rstudio.com)

### <a name="create-rstudio-connect-saml-authentication-test-user"></a>Criar utilizador de teste de autenticação RStudio Connect SAML

Nesta secção, um utilizador chamado Britta Simon é criado na Autenticação SAML RStudio Connect. A autenticação RStudio Connect SAML suporta o provisionamento just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na Autenticação SAML RStudio Connect, cria-se uma nova quando tenta aceder à Autenticação RStudio Connect SAML.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para O Sinal de Autenticação SAML do RStudio Connect NO URL, onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de autenticação de autenticação SAML RStudio e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na Autenticação SAML RStudio Connect para a qual configura o SSO 

Também pode utilizar o Microsoft Access Panel para testar a aplicação em qualquer modo. Quando clicar no azulejo de autenticação RStudio Connect SAML no Painel de Acesso, se configurado no modo SP será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na Autenticação RStudio Connect SAML para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado A autenticação RStudio Connect SAML, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).