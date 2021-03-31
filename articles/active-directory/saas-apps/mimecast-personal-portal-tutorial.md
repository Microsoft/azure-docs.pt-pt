---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Portal Pessoal mimecast | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Portal Pessoal Mimecast.
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
ms.openlocfilehash: cc12afb9d4a889d53689421a98b522a8e3264da6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644617"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mimecast-personal-portal"></a>Tutorial: Azure Ative Directory integração única (SSO) com o Portal Pessoal mimecast

Neste tutorial, você vai aprender a integrar o Portal Pessoal mimecast com O Diretório Ativo Azure (Azure AD). Quando integrar o Portal Pessoal Mimecast com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Portal Pessoal mimecast.
* Capacitar os seus utilizadores a serem automaticamente inscritos no Mimecast Personal Portal com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Mimecast Personal Portal assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Mimecast Personal Portal suporta **SP e IDP** iniciado SSO
 
## <a name="add-mimecast-personal-portal-from-the-gallery"></a>Adicione o Portal Pessoal Mimecast da galeria

Para configurar a integração do Portal Pessoal mimecast em AD Azure, é necessário adicionar o Portal Pessoal mimecast da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite o Portal Pessoal mimecast** na caixa de pesquisa.
1. Selecione **Mimecast Personal Portal** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-mimecast-personal-portal"></a>Configure e teste Azure AD SSO para o Portal Pessoal mimecast

Configure e teste Azure AD SSO com Mimecast Personal Portal usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Portal Pessoal mimecast.

Para configurar e testar Azure AD SSO com o Portal Pessoal mimecast, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Mimecast Personal Portal SSO](#configure-mimecast-personal-portal-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. Crie o utilizador de **[teste do Portal Pessoal Mimecast](#create-mimecast-personal-portal-test-user)** - para ter uma contrapartida de B.Simon no Portal Pessoal mimecast que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **do Portal Pessoal Mimecast,** encontre a secção **Gerir** e selecione um único sinal **de sação**.
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
    > Encontrará o `accountcode` valor no Portal Pessoal mimecast no Código de   >  **Definições de**  >  **Conta.** Anexar o `accountcode` identificador.

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
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Estados Unidos da América   | `https://us-api.mimecast.com/login/saml`|
    | África do Sul    | `https://za-api.mimecast.com/login/saml`|
    | Austrália       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Portal Pessoal mimecast .

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Mimecast Personal Portal**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-mimecast-personal-portal-sso"></a>Configure Mimecast Personal Portal SSO

1. Numa janela diferente do navegador web, inscreva-se na Consola de Administração Mimecast.

1. Navegar para  >  aplicações **de**  >  **serviços de administração.**

    ![A screenshot mostra a janela Mimecast com aplicações selecionadas.](./media/mimecast-personal-portal-tutorial/services.png)

1. Clique no **separador Perfis de Autenticação.**
    
    ![A screenshot mostra o separador Aplicação com perfis de autenticação selecionados.](./media/mimecast-personal-portal-tutorial/authentication-profiles.png)

1. Clique no separador **Novo Perfil de Autenticação.**

    ![A screenshot mostra o novo perfil de autenticação selecionado.](./media/mimecast-personal-portal-tutorial/new-authenticatio-profile.png)

1. Forneça uma descrição válida na caixa de texto **Descrição** e selecione A autenticação SAML para a caixa de verificação **do Portal Pessoal mimecast.**

    ![A screenshot mostra novo perfil de autenticação selecionado.](./media/mimecast-personal-portal-tutorial/selecting-personal-portal.png)

1. Na **configuração SAML para a página Do Portal Pessoal mimecast,** execute os seguintes passos:

    ![A screenshot mostra onde selecionar a autenticação SAML para consola de administração.](./media/mimecast-personal-portal-tutorial/sso-settings.png)

    a. Para **Fornecedor**, selecione **Azure Ative Directory** do Dropdown.

    b. Na caixa de texto **URL dos metadados,** cole o valor **URL da Federação de Metadados de Aplicação,** que copiou a partir do portal Azure.

    c. Clique **em Importar.** Após a importação do URL dos metadados, os campos serão povoados automaticamente, não havendo necessidade de realizar qualquer ação nestes campos.

    d. Certifique-se de que desmarca **o contexto protegido da palavra-passe** e utilize caixas de **verificação de contexto de autenticação integrada.**

    e. Clique em **Guardar**.

### <a name="create-mimecast-personal-portal-test-user"></a>Criar utilizador de teste do Portal Pessoal Mimecast

1. Numa janela diferente do navegador web, inscreva-se na Consola de Administração Mimecast.

1. Navegar para  >  **Diretórios**  >  **Internos de** Administração .

    ![A screenshot mostra a Configuração SAML para o Portal Pessoal mimecast onde pode introduzir os valores descritos.](./media/mimecast-personal-portal-tutorial/internal-directories.png)

1. Selecione no seu domínio, se o domínio for mencionado abaixo, caso contrário, crie um novo domínio clicando no **Novo Domínio**.

    ![A screenshot mostra a janela mimecast com diretórios internos selecionados.](./media/mimecast-personal-portal-tutorial/domain-name.png)

1. Clique no separador **Novo Endereço.**

    ![A screenshot mostra o domínio selecionado.](./media/mimecast-personal-portal-tutorial/new-address.png)

1. Forneça as informações necessárias do utilizador na página seguinte:

    ![A screenshot mostra a página onde pode introduzir os valores descritos.](./media/mimecast-personal-portal-tutorial/user-information.png)

    a. Na caixa de texto **do Endereço de E-mail,** insira o endereço de e-mail do utilizador como `B.Simon@yourdomainname.com` .

    b. Na caixa de texto **'Nome Global',** insira o **nome completo** do utilizador.

    c. Nas **caixas** de texto password e **confirmar palavra-passe,** introduza a palavra-passe do utilizador.

    d. Selecione **Alterar de Força na** caixa de verificação de início de sessão.

    e. Clique em **Guardar**.

    f. Para atribuir funções ao utilizador, clique em **Role Edit** e atribua a função necessária ao utilizador de acordo com o requisito da sua organização.

    ![A screenshot mostra definições de endereço onde pode selecionar a Edição de Função.](./media/mimecast-personal-portal-tutorial/assign-role.png)


## <a name="test-sso"></a>Teste SSO 
Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Mimecast Personal Portal Sign no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de Acesso ao Portal Pessoal mimecast e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Portal Pessoal mimecast para o qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo do Portal Pessoal Mimecast nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Portal Pessoal mimecast para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Portal Pessoal mimecast pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app)