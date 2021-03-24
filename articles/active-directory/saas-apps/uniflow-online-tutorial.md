---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com uniFLOW Online | Microsoft Docs'
description: Saiba como configurar um único sinal de entrada entre o Azure Ative Directory e o uniFLOW Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 9fdcd8a82b901e00e28f0ddd89ba53d9a2e3fbae
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952698"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Tutorial: Azure Ative Directy integração única (SSO) com uniFLOW Online

Neste tutorial, você vai aprender a integrar uniFLOW Online com Azure Ative Directory (Azure AD). Quando integra o uniFLOW Online com AZure AD, pode:

* Controlo em Azure AD que tem acesso a uniFLOW Online.
* Capacitar os seus utilizadores a iniciar súmido no uniFLOW Online com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* inquilino uniFLOW Online.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* uniFLOW Online suporta **SSO** iniciado SP

## <a name="add-uniflow-online-from-the-gallery"></a>Adicionar uniFLOW Online da galeria

Para configurar a integração do uniFLOW Online no AD Azure, é necessário adicionar uniFLOW Online da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva uniFLOW Online** na caixa de pesquisa.
1. Selecione **uniFLOW Online** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-uniflow-online"></a>Configure e teste Azure AD SSO para uniFLOW Online

Configure e teste Azure AD SSO com uniFLOW Online usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no uniFLOW Online.

Para configurar e testar o Azure AD SSO com uniFLOW Online, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
   1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
   1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure uniFLOW Online SSO](#configure-uniflow-online-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Inscreva-se no uniFLOW Online utilizando o utilizador de teste criado](#sign-in-to-uniflow-online-using-the-created-test-user)** - para testar o pedido de entrada do utilizador no lado da aplicação.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página **uniFLOW de** integração de aplicações online, encontre a secção **Gerir** e selecione um único sinal **de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando um dos seguintes padrões:

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando um dos seguintes padrões:

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do cliente online uniFLOW](mailto:support@nt-ware.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure ou consultar o URL de resposta exibido no seu inquilino uniFLOW Online.

1. a aplicação uniFLOW Online espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação uniFLOW Online espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name |  Atributo de origem|
    | -----------| --------------- |
    | nome de exibição | user.displayname |
    | apelido | user.onpremisessamaccountname |

   > [!NOTE]
   > O `user.onpremisessamaccountname` atributo só conterá um valor se os seus utilizadores AD Azure estiverem sincronizados a partir de um Diretório Ativo do Windows local.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao uniFLOW Online.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **uniFLOW Online.**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

> [!NOTE]
> Para permitir que todos os utilizadores acedam à aplicação sem atribuição manual, vá à secção **Gerir** e selecione **Propriedades.** Em seguida, altere o parâmetro **exigido para** **Nº.**

## <a name="configure-uniflow-online-sso"></a>Configure uniFLOW Online SSO

1. Numa janela diferente do navegador web, inscreva-se no site uniFLOW Online como administrador.

1. A partir do painel de navegação esquerdo, selecione **o separador Utilizador.**

    ![A screenshot mostra o utilizador selecionado a partir do site uniflow Online.](./media/uniflow-online-tutorial/configure-1.png)

1. Clique **em fornecedores de identidade.**

    ![A screenshot mostra fornecedores de identidade selecionados.](./media/uniflow-online-tutorial/configure-2.png)

1. Clique no **Adicionar fornecedor de identidade.**

    ![Screenshot mostra Adicionar fornecedor de identidade selecionado.](./media/uniflow-online-tutorial/configure-3.png)

1. Na secção **ADD IDENTITY PROVIDER,** execute os seguintes passos:

    ![A screenshot mostra a secção ADD IDENTITY PROVIDER onde pode introduzir os valores descritos.](./media/uniflow-online-tutorial/configure-4.png)

    a. Introduza o nome de exibição Ex: *AzureAD SSO*.

    b. Para **o tipo fornecedor**, selecione a opção **WS-Fed** a partir do dropdown.

    c. Para o **tipo WS-Fed,** selecione a opção **Azure Ative Directory** a partir do dropdown.

    d. Clique em **Guardar**.

1. No separador **Geral,** execute os seguintes passos:

    ![A screenshot mostra o separador Geral onde pode introduzir os valores descritos.](./media/uniflow-online-tutorial/configure-5.png)

    a. Introduza o nome de exibição Ex: *AzureAD SSO*.

    b. Selecione a opção **A partir de URL** para os **metadados da Federação ADFS**.

    c. Na caixa de texto **URl da Federação de Metadados,** cole o valor **do url de metadados da Federação de Aplicações,** que copiou a partir do portal Azure.

    d. Selecione **o fornecedor de identidade** como **Ativado**.

    e. Selecione **o registo automático do utilizador** como **Ativado.**

    f. Clique em **Guardar**.

### <a name="sign-in-to-uniflow-online-using-the-created-test-user"></a>Inscreva-se no uniFLOW Online usando o utilizador de teste criado

1. Numa janela diferente do navegador, aceda ao URL uniFLOW Online para o seu inquilino.

1. Selecione o fornecedor de identidade previamente criado para iniciar sação através da sua instância AD Azure.

1. Inscreva-se utilizando o utilizador de teste.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de inscrição online uniFLOW, onde pode iniciar o fluxo de login. 

* Vá diretamente para o URL de inscrição online uniFLOW e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo uniFLOW online nas Minhas Apps, este será redirecionado para o URL de inscrição online uniFLOW. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado uniFLOW Online, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).