---
title: 'Tutorial: Integração do Azure Ative Directory com a Consola De Administrador Mimecast | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Mimecast Admin Console.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 606665cead215c049e55f22a5f8ff0f668181fb4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101647207"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mimecast-admin-console"></a>Tutorial: Azure Ative Directory integração única (SSO) com a Consola de Administração Mimecast

Neste tutorial, você vai aprender a integrar a Consola de Administração Mimecast com Azure Ative Directory (Azure AD). Quando integrar a Consola de Administração Mimecast com Azure AD, pode:

* Control em Azure AD que tem acesso à Consola de Administração Mimecast.
* Ative os seus utilizadores a serem automaticamente inscritos na Consola de Administração Mimecast com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Mimecast Admin Console única sign-on (SSO) ativada subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Consola Mimecast Admin suporta **SP e IDP** iniciado SSO

## <a name="add-mimecast-admin-console-from-the-gallery"></a>Adicione a Consola de Administração Mimecast da galeria

Para configurar a integração da Consola Admin Mimecast em Azure AD, é necessário adicionar a Consola De Administração Mimecast da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Mimecast Admin Console** na caixa de pesquisa.
1. Selecione **Mimecast Admin Consola** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-mimecast-admin-console"></a>Configure e teste Azure AD SSO para a Consola De Administração Mimecast

Configure e teste Azure AD SSO com Mimecast Admin Console usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Consola de Administração Mimecast.

Para configurar e testar O Azure AD SSO com a Consola De Administração Mimecast, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Mimecast Admin Console SSO](#configure-mimecast-admin-console-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. Crie o utilizador de **[teste de Consola Mimecast Admin](#create-mimecast-admin-console-test-user)** - para ter uma contraparte de B.Simon na Consola de Administração Mimecast que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Mimecast Admin Console,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo IDP, execute os seguintes passos:

    a. Na caixa de texto **identifier,** digite o URL utilizando o seguinte padrão:

    | Region  |  Valor | 
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Estados Unidos da América   | `https://us-api.mimecast.com/sso/<accountcode>`|
    | África do Sul    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Austrália       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Offshore        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    > [!NOTE]
    > Encontrará o `accountcode` valor na Consola de Administração Mimecast no Código  >  **de Definições de**  >  **Conta**. Anexar o `accountcode` identificador.

    b. Na caixa de texto **URL de resposta,** digite o URL: 

    | Region  |  Valor | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Estados Unidos da América   | `https://us-api.mimecast.com/login/saml`|
    | África do Sul    | `https://za-api.mimecast.com/login/saml`|
    | Austrália       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

1. Se desejar configurar a aplicação no modo iniciado pela **SP:**

    Na caixa de texto **URL de entrada de sinais,** digite o URL: 

    | Region  |  Valor | 
    | --------------- | --------------- | 
    | Europa          | `https://login-eu.mimecast.com/administration/app/#/administration-dashboard`|
    | Estados Unidos da América   | `https://login-us.mimecast.com/administration/app/#/administration-dashboard`|
    | África do Sul    | `https://login-za.mimecast.com/administration/app/#/administration-dashboard`|
    | Austrália       | `https://login-au.mimecast.com/administration/app/#/administration-dashboard`|
    | Offshore        | `https://login-jer.mimecast.com/administration/app/#/administration-dashboard`|

1. Clique em **Guardar**.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Consola de Administração Mimecast.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Mimecast Admin Console**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-mimecast-admin-console-sso"></a>Configurar a Consola Admin mimecast SSO

1. Numa janela diferente do navegador web, inscreva-se na Consola de Administração Mimecast.

1. Navegar para  >  aplicações **de**  >  **serviços de administração.**

    ![A screenshot mostra a janela Mimecast com aplicações selecionadas.](./media/mimecast-admin-console-tutorial/services.png)

1. Clique no **separador Perfis de Autenticação.**
    
    ![A screenshot mostra o separador Aplicação com perfis de autenticação selecionados.](./media/mimecast-admin-console-tutorial/authentication-profiles.png)

1. Clique no separador **Novo Perfil de Autenticação.**

    ![A screenshot mostra novo perfil de autenticação selecionado.](./media/mimecast-admin-console-tutorial/new-authenticatio-profile.png)

1. Forneça uma descrição válida na caixa de texto **Descrição** e **selecione A autenticação SAML para caixa** de verificação da consola de administração.

    ![A screenshot mostra onde selecionar a autenticação SAML para consola de administração.](./media/mimecast-admin-console-tutorial/selecting-admin-consle.png)

1. Na página **saml configuração para consola de administração,** execute os seguintes passos:

    ![A screenshot mostra a configuração SAML para consola de administração onde pode introduzir os valores descritos.](./media/mimecast-admin-console-tutorial/sso-settings.png)

    a. Para **Fornecedor**, selecione **Azure Ative Directory** do Dropdown.

    b. Na caixa de texto **URL dos metadados,** cole o valor **URL da Federação de Metadados de Aplicação,** que copiou a partir do portal Azure.

    c. Clique **em Importar.** Após a importação do URL dos metadados, os campos serão povoados automaticamente, não havendo necessidade de realizar qualquer ação nestes campos.

    d. Certifique-se de que desmarca **o contexto protegido da palavra-passe** e utilize caixas de **verificação de contexto de autenticação integrada.**

    e. Clique em **Guardar**.

### <a name="create-mimecast-admin-console-test-user"></a>Criar utilizador de teste de consola de admin Mimecast

1. Numa janela diferente do navegador web, inscreva-se na Consola de Administração Mimecast.

1. Navegar para  >  **Diretórios**  >  **Internos de** Administração .

    ![A screenshot mostra a janela mimecast com diretórios internos selecionados.](./media/mimecast-admin-console-tutorial/internal-directories.png)

1. Selecione no seu domínio, se o domínio for mencionado abaixo, caso contrário, crie um novo domínio clicando no **Novo Domínio**.

    ![A screenshot mostra o domínio selecionado.](./media/mimecast-admin-console-tutorial/domain-name.png)

1. Clique no separador **Novo Endereço.**

    ![A screenshot mostra novo endereço selecionado.](./media/mimecast-admin-console-tutorial/new-address.png)

1. Forneça as informações necessárias do utilizador na página seguinte:

    ![A screenshot mostra a página onde pode introduzir os valores descritos.](./media/mimecast-admin-console-tutorial/user-information.png)

    a. Na caixa de texto **do Endereço de E-mail,** insira o endereço de e-mail do utilizador como `B.Simon@yourdomainname.com` .

    b. Na caixa de texto **'Nome Global',** insira o **nome completo** do utilizador.

    c. Nas **caixas** de texto password e **confirmar palavra-passe,** introduza a palavra-passe do utilizador.

    d. Selecione **Alterar de Força na** caixa de verificação de início de sessão.

    e. Clique em **Guardar**.

    f. Para atribuir funções ao utilizador, clique em **Role Edit** e atribua a função necessária ao utilizador de acordo com o requisito da sua organização.

    ![A screenshot mostra definições de endereço onde pode selecionar a Edição de Função.](./media/mimecast-admin-console-tutorial/assign-role.png)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o Sinal de Consola de Administração Mimecast no URL, onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de assinatura da consola de administração mimecast e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na Consola de Administração Mimecast para a qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo da Consola de Admin Mimecast nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na Consola de Administração Mimecast para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado a Consola De Administração Mimecast pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).