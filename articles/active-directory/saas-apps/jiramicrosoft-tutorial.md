---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com JIRA SAML SSO pela Microsoft | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o JIRA SAML SSO pela Microsoft.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 75b8230a1027bbf3ff3d73fb35ce65107c2db7e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98730676"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jira-saml-sso-by-microsoft"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com JIRA SAML SSO pela Microsoft

Neste tutorial, você vai aprender a integrar JIRA SAML SSO pela Microsoft com Azure Ative Directory (Azure AD). Quando integrar o JIRA SAML SSO pela Microsoft com a Azure AD, pode:

* Control em Azure AD que tem acesso ao JIRA SAML SSO pela Microsoft.
* Ative os seus utilizadores a serem automaticamente inscritos no JIRA SAML SSO pela Microsoft com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="description"></a>Description

Utilize a sua conta Microsoft Azure Ative Directory com o servidor Atlassian JIRA para ativar um único s-on. Desta forma, todos os utilizadores da sua organização podem usar as credenciais AZURE AD para iniciar súm na aplicação JIRA. Este plugin usa SAML 2.0 para federação.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o JIRA SAML SSO pela Microsoft, precisa dos seguintes itens:

- Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
- JIRA Core e Software 6.4 a 8.14.0 ou JIRA Service Desk 3.0 a 4.11.1 devem ser instalados e configurados na versão do Windows 64-bit
- O servidor JIRA está com ATIVAR HTTPS
- Note que as versões suportadas para O Plugin JIRA são mencionadas na secção seguinte.
- O servidor JIRA é acessível na Internet particularmente à página de login AD Azure para autenticação e deve poder receber o token da Azure AD
- Credenciais de administração são criadas no JIRA
- WebSudo está desativado no JIRA
- Utilizador de teste criado na aplicação do servidor JIRA

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização de um ambiente de produção do JIRA. Teste a integração em primeiro lugar no ambiente de desenvolvimento ou encenação da aplicação e, em seguida, utilize o ambiente de produção.

Para começar, precisa dos seguintes itens:

* Não utilize o seu ambiente de produção, a não ser que seja necessário.
* JIRA SAML SSO by Microsoft single sign-on (SSO) ativada subscrição.

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="supported-versions-of-jira"></a>Versões apoiadas do JIRA

* JIRA Core e Software: 6.4 a 8.14.0
* Balcão de Serviço JIRA 3.0.0 a 4.11.1
* JIRA também suporta 5.2. Para mais detalhes, clique [no Microsoft Azure Ative Directory para o JIRA 5.2](jira52microsoft-tutorial.md)

> [!NOTE]
> Por favor, note que o nosso JIRA Plugin também funciona na versão Ubuntu 16.04 e Linux.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* JIRA SAML SSO pela Microsoft suporta **SP** iniciado SSO

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>Adicionar JIRA SAML SSO pela Microsoft da galeria

Para configurar a integração do JIRA SAML SSO pela Microsoft em Azure AD, é necessário adicionar o JIRA SAML SSO pela Microsoft da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** digite **JIRA SAML SSO pela Microsoft** na caixa de pesquisa.
1. Selecione **JIRA SAML SSO pela Microsoft** a partir do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-jira-saml-sso-by-microsoft"></a>Configure e teste Azure AD SSO para JIRA SAML SSO pela Microsoft

Configure e teste Azure AD SSO com JIRA SAML SSO pela Microsoft usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no JIRA SAML SSO pela Microsoft.

Para configurar e testar o Azure AD SSO com o JIRA SAML SSO pela Microsoft, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o JIRA SAML SSO pelo Microsoft SSO](#configure-jira-saml-sso-by-microsoft-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Crie o JIRA SAML SSO pelo utilizador](#create-jira-saml-sso-by-microsoft-test-user)** de teste da Microsoft - para ter uma contrapartida de B.Simon em JIRA SAML SSO pela Microsoft que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **JIRA SAML pela Microsoft,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Na caixa **identifier,** digite um URL utilizando o seguinte padrão: `https://<domain:port>/`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL Sign-On. A porta é opcional no caso de ser uma URL chamada. Estes valores são recebidos durante a configuração do plugin Jira, que é explicado mais tarde no tutorial.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao JIRA SAML SSO pela Microsoft.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **JIRA SAML SSO pela Microsoft**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-jira-saml-sso-by-microsoft-sso"></a>Configure JIRA SAML SSO pela Microsoft SSO

1. Numa janela diferente do navegador web, inscreva-se na sua instância JIRA como administrador.

2. Hover on cog e clique nos **Add-ons**.

    ![A screenshot mostra add-ons selecionados a partir do menu Definições.](./media/jiramicrosoft-tutorial/addon1.png)

3. Descarregue o plugin do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56506). Faça o upload manual do plugin fornecido pela Microsoft utilizando o menu **add-on do Upload.** O download do plugin está coberto pelo [Microsoft Service Agreement](https://www.microsoft.com/servicesagreement/).

    ![Screenshot mostra Gerir add-ons com o link add-on upload chamado.](./media/jiramicrosoft-tutorial/addon12.png)

4. Para executar o cenário de procuração inversa jira ou cenário de balançador de carga execute os seguintes passos:

    > [!NOTE]
    > Deve configurar primeiro o servidor com as instruções abaixo e depois instalar o plugin.

    a. Adicione abaixo o atributo na porta **do conector** em **server.xml** ficheiro da aplicação do servidor JIRA.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![A screenshot mostra o ficheiro do ponto x m l do servidor num editor com a nova linha adicionada.](./media/jiramicrosoft-tutorial/reverseproxy1.png)

    b. Altere **o URL base** nas **definições do sistema** de acordo com o equilibrador proxy/load.

    ![A screenshot mostra as Definições de Administração onde pode alterar a Base U R L.](./media/jiramicrosoft-tutorial/reverseproxy2.png)

5. Uma vez instalado o plugin, aparece na secção de add-ons **instalada pelo utilizador** da secção **'Gerir o Add-on'.** Clique **em Configurar** para configurar o novo plugin.

    ![A screenshot mostra o Azure A D SAML Single Sign-on para a secção Jira com configuração selecionada.](./media/jiramicrosoft-tutorial/addon14.png)

6. Execute os seguintes passos na página de configuração:

    ![A screenshot mostra o único sinal de inscrição do Microsoft Azure Ative Directory para a página de configuração Jira.](./media/jiramicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Certifique-se de que existe apenas um certificado mapeado contra a app para que não haja erro na resolução dos metadados. Se houver vários certificados, ao resolver os metadados, a administração recebe um erro.

    a. Na caixa de texto **URL dos metadados,** cole o url **da Federação de Aplicações** que copiou a partir do portal Azure e clique no botão **Resolver.** Lê o URL de metadados IdP e povoa todas as informações dos campos.

    b. Copie o **Identificador, URS de resposta e assine os** valores de URL e cole-os em **Identifier, Url de resposta e assinar em** caixas de texto URL respectivamente em **JIRA SAML SSO por Microsoft Domain e URLs** na secção Azure.

    c. No **Botão de Início O nome** do botão o nome do botão que a sua organização quer que os utilizadores vejam no ecrã de login.
    
    d. Na Descrição do botão de início de **sessão,** a descrição do botão que a sua organização quer que os utilizadores vejam no ecrã de login.

    e. Nas **localizações de ID do utilizador SAML,** selecione que **o ID do utilizador está no elemento NameIdentifier da declaração do Assunto** ou o **ID do utilizador está num elemento Atributo**.  Este ID tem de ser o ID do utilizador JIRA. Se o ID do utilizador não for igualado, então o sistema não permitirá que os utilizadores entrem.

    > [!Note]
    > A localização padrão do ID do utilizador SAML é o identificador de nome. Pode alterá-lo para uma opção de atributo e introduzir o nome de atributo apropriado.

    f. Se selecionar **o ID do utilizador está numa** opção de elemento Atributo, então na caixa de texto do nome Atributo **digite** o nome do atributo onde o ID do utilizador é esperado.

    exemplo, Se estiver a utilizar o domínio federado (como ADFS, etc.) com Azure AD, clique na opção **Enable Home Realm Discovery** e configuure o Nome de **Domínio**.

    h. Em **Nome de Domínio** digite o nome de domínio aqui no caso do login baseado em ADFS.

    i. Verifique **como Iniciar símis** se pretender assinar a partir do Azure AD quando um utilizador assinar no JIRA.
    
    j. Ativar a caixa de verificação **de Login Force Azure,** se pretender iniciar sessão apenas através das credenciais AZure AD.
    
    > [!Note]
    > Para ativar o formulário de login predefinido para o login na página de login quando o login de força estiver ativado, adicione o parâmetro de consulta no URL do navegador.
    > `https://<domain:port>/login.jsp?force_azure_login=false`

    k. Clique **em Guardar** o botão para guardar as definições.

    > [!NOTE]
    > Para mais informações sobre a instalação e resolução de problemas, visite [o Guia de Administração do Conector MS JIRA SSO](./ms-confluence-jira-plugin-adminguide.md). Há também uma [FAQ](./ms-confluence-jira-plugin-adminguide.md) para a sua assistência.

### <a name="create-jira-saml-sso-by-microsoft-test-user"></a>Criar Jira SAML SSO pelo utilizador de teste da Microsoft

Para permitir que os utilizadores de Azure AD inscrevam-se no servidor JIRA no local, devem ser ateados no JIRA SAML SSO pela Microsoft. Para jira SAML SSO pela Microsoft, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu servidor JIRA no local como administrador.

2. Hover on cog e clique na **gestão** do Utilizador .

    ![A screenshot mostra a gestão do utilizador selecionada a partir do menu Definições.](./media/jiramicrosoft-tutorial/user1.png)

3. É redirecionado para a página de Acesso do Administrador para introduzir **palavra-passe** e clicar no botão **Confirmar.**

    ![A screenshot mostra a página de Acesso do Administrador onde introduz as suas credenciais.](./media/jiramicrosoft-tutorial/user2.png)

4. Na secção de separador **de gestão do utilizador,** clique em **criar utilizador.**

    ![A screenshot mostra o separador de gestão do Utilizador onde pode criar o utilizador.](./media/jiramicrosoft-tutorial/user3.png) 

5. Na página de diálogo **"Criar novo utilizador",** execute os seguintes passos:

    ![A screenshot mostra a nova caixa de diálogo do utilizador criar onde pode introduzir as informações neste passo.](./media/jiramicrosoft-tutorial/user4.png) 

    a. Na caixa de texto **do endereço de e-mail,** digite o endereço de e-mail do utilizador como B.simon@contoso.com .

    b. Na caixa de texto **'Nome Completo',** escreva o nome completo do utilizador como B.Simon.

    c. Na caixa de texto **username,** escreva o e-mail do utilizador como B.simon@contoso.com .

    d. Na caixa de texto **da palavra-passe,** digite a palavra-passe do utilizador.

    e. Clique **em Criar utilizador.**

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para JIRA SAML SSO por URL de entrada de assinatura da Microsoft, onde pode iniciar o fluxo de login. 

* Vá diretamente ao JIRA SAML SSO pelo MICROSOFT Sign-on URL e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no SSO JIRA SAML pela Microsoft nas Minhas Apps, este será redirecionado para JIRA SAML SSO pelo URL de assinatura da Microsoft. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado O SSO JIRA SAML pela Microsoft, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)