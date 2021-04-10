---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Confluence SAML SSO pela Microsoft | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Confluence SAML SSO da Microsoft.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/25/2020
ms.author: jeedes
ms.openlocfilehash: 34365a8bd7a15f502aa89a966adb14807e802cc4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98737003"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-confluence-saml-sso-by-microsoft"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Confluence SAML SSO pela Microsoft

Neste tutorial, você aprenderá a integrar o Confluence SAML SSO pela Microsoft com o Azure Ative Directory (Azure AD). Quando integrar o Confluence SAML SSO pela Microsoft com a AZure AD, pode:

* Control em Azure AD que tem acesso a Confluence SAML SSO pela Microsoft.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Confluence SAML SSO pela Microsoft com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.


## <a name="description"></a>Descrição:

Utilize a sua conta Microsoft Azure Ative Directory com o servidor Atlassian Confluence para ativar um único sence. Desta forma, todos os utilizadores da sua organização podem usar as credenciais AZure AD para iniciar súm na aplicação Confluence. Este plugin usa SAML 2.0 para federação.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a Confluence SAML SSO pela Microsoft, precisa dos seguintes itens:

- Uma subscrição do Azure
- Aplicação do servidor confluência instalada num servidor Windows 64 bits (no local ou na infraestrutura IaaS da nuvem)
- O servidor de confluência está ativado em HTTPS
- Note que as versões suportadas para o Plugin Confluence são mencionadas na secção seguinte.
- O servidor confluence é acessível na internet particularmente à página de Login AZure para autenticação e deve ser capaz de receber o token do Azure AD
- As credenciais de administração são criadas em Confluência
- WebSudo é desativado em Confluência
- Utilizador de teste criado na aplicação do servidor Confluence

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização de um ambiente de produção de Confluência. Teste a integração em primeiro lugar no ambiente de desenvolvimento ou encenação da aplicação e, em seguida, utilize o ambiente de produção.

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

Para começar, precisa dos seguintes itens:

* Não utilize o seu ambiente de produção, a não ser que seja necessário.
* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Confluence SAML SSO by Microsoft single sign-on (SSO) ativada subscrição.

## <a name="supported-versions-of-confluence"></a>Versões suportadas da Confluência

A partir de agora, as seguintes versões de Confluência são suportadas:

- Confluência: 5.0 a 5.10
- Confluência: 6.0.1 a 6.15.9
- Confluência: 7.0.1 a 7.9.3

> [!NOTE]
> Por favor, note que o nosso Plugin Confluence também funciona na versão Ubuntu 16.04

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Confluence SAML SSO pela Microsoft suporta **SP** iniciado SSO

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Adicionar Confluence SAML SSO pela Microsoft da galeria

Para configurar a integração da Confluence SAML SSO pela Microsoft em Azure AD, é necessário adicionar o Confluence SAML SSO pela Microsoft da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Confluence SAML SSO pela Microsoft** na caixa de pesquisa.
1. Selecione **Confluence SAML SSO pela Microsoft** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-confluence-saml-sso-by-microsoft"></a>Configure e teste Azure AD SSO para Confluence SAML SSO pela Microsoft

Configure e teste Azure AD SSO com Confluence SAML SSO pela Microsoft usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Confluence SAML SSO pela Microsoft.

Para configurar e testar Azure AD SSO com Confluence SAML SSO pela Microsoft, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Confluence SAML SSO by Microsoft SSO](#configure-confluence-saml-sso-by-microsoft-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Confluence SAML SSO by Microsoft test user](#create-confluence-saml-sso-by-microsoft-test-user)** - para ter uma contraparte de B.Simon em Confluence SAML SSO pela Microsoft que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **Confluence SAML by Microsoft,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Na caixa **identifier,** digite um URL utilizando o seguinte padrão: `https://<domain:port>/`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL Sign-On. A porta é opcional no caso de ser uma URL chamada. Estes valores são recebidos durante a configuração do plugin confluence, que é explicado mais tarde no tutorial.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Confluence SAML SSO pela Microsoft.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Confluence SAML SSO pela Microsoft**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-confluence-saml-sso-by-microsoft-sso"></a>Configure Confluence SAML SSO by Microsoft SSO

1. Numa janela diferente do navegador web, inscreva-se na sua instância confluência como administrador.

1. Hover on cog e clique nos **Add-ons**.

    ![Screenshot que mostra o ícone "Cog" selecionado, e "Add-ons" realçados no menu suspenso.](./media/confluencemicrosoft-tutorial/addon1.png)

1. Descarregue o plugin do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56503). Faça o upload manual do plugin fornecido pela Microsoft utilizando o menu **add-on do Upload.** O download do plugin está coberto pelo [Microsoft Service Agreement](https://www.microsoft.com/servicesagreement/).

    ![Screenshot que mostra a página "Gerir add-ons" com a ação "Upload add-on" selecionada.](./media/confluencemicrosoft-tutorial/addon12.png)

1. Para executar o cenário de procuração inversa confluência ou cenário de balançador de carga execute os seguintes passos:

    > [!NOTE]
    > Deve configurar primeiro o servidor com as instruções abaixo e depois instalar o plugin.

    a. Adicione abaixo o atributo na porta **do conector** em **server.xml** ficheiro da aplicação do servidor JIRA.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Screenshot que mostra o ficheiro "server.xml" com o atributo adicionado à porta "connector".](./media/confluencemicrosoft-tutorial/reverseproxy1.png)

    b. Altere **o URL base** nas **definições do sistema** de acordo com o equilibrador proxy/load.

    ![Screenshot que mostra a página "Administração - Definições" com "URL base" realçada.](./media/confluencemicrosoft-tutorial/reverseproxy2.png)

1. Uma vez instalado o plugin, aparece na secção de add-ons **instalada pelo utilizador** da secção **'Gerir o Add-on'.** Clique **em Configurar** para configurar o novo plugin.

    ![Screenshot que mostra a secção "User Installed" com o botão "Configurar" realçado.](./media/confluencemicrosoft-tutorial/addon15.png)

1. Execute os seguintes passos na página de configuração:

    ![Screenshot que mostra a única página de configuração de inscrição.](./media/confluencemicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Certifique-se de que existe apenas um certificado mapeado contra a app para que não haja erro na resolução dos metadados. Se houver vários certificados, a administração obtém um erro ao resolver os metadados.

    1. Na caixa de texto **URL dos metadados,** cole o url **da Federação de Aplicações** que copiou a partir do portal Azure e clique no botão **Resolver.** Lê o URL de metadados IdP e povoa todas as informações dos campos.

    1. Copie o **Identificador, URS de Resposta e Assine nos** valores de URL e cole-os em **Identifier, URL de resposta e assinar em** caixas de texto URL, respectivamente, na secção de **Configuração SAML Básica** no portal Azure.

    1. No **Botão de Início O nome** do botão o nome do botão que a sua organização quer que os utilizadores vejam no ecrã de login.

    1. Na Descrição do botão de início de **sessão,** a descrição do botão que a sua organização quer que os utilizadores vejam no ecrã de login.

    1. Nas **localizações de ID do utilizador SAML**, selecione que **o ID do utilizador está no elemento NameIdentifier da declaração do Assunto** ou o **ID do utilizador está num elemento Atributo**.  Esta identificação tem de ser a identificação do utilizador da Confluência. Se o ID do utilizador não for igualado, então o sistema não permitirá que os utilizadores entrem. 

       > [!Note]
       > A localização padrão do ID do utilizador SAML é o identificador de nome. Pode alterá-lo para uma opção de atributo e introduzir o nome de atributo apropriado.

    1. Se selecionar **o ID do utilizador está numa** opção de elemento Atributo, então na caixa de texto do nome Atributo **digite** o nome do atributo onde o ID do utilizador é esperado. 

    1. Se estiver a utilizar o domínio federado (como ADFS, etc.) com Azure AD, clique na opção **Enable Home Realm Discovery** e configuure o Nome de **Domínio**.

    1. Em **Nome de Domínio** digite o nome de domínio aqui no caso do login baseado em ADFS.

    1. Verifique **ativar a assinatura única** se pretender assinar a partir do Azure AD quando um utilizador assinar a partir da Confluence. 

    1. Ativar a caixa de verificação **de Login Force Azure,** se pretender iniciar sessão apenas através das credenciais AZure AD.

       > [!Note]
       > Para ativar o formulário de login predefinido para o login na página de login quando o login de força estiver ativado, adicione o parâmetro de consulta no URL do navegador.
       > `https://<domain:port>/login.action?force_azure_login=false`

    1. Clique **em Guardar** o botão para guardar as definições.

       > [!NOTE]
       > Para obter mais informações sobre a instalação e resolução de problemas, visite [o Guia de Administração do Conector SSO MS Confluence SSO](./ms-confluence-jira-plugin-adminguide.md). Há também uma [FAQ](./ms-confluence-jira-plugin-adminguide.md) para a sua assistência.

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Criar Confluence SAML SSO pelo utilizador de teste da Microsoft

Para permitir que os utilizadores de Azure AD inscrevam-se no servidor Confluence no local, devem ser ateados no Confluence SAML SSO pela Microsoft. Para a Confluence SAML SSO da Microsoft, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu servidor Confluence no local como administrador.

1. Hover on cog e clique na **gestão** do Utilizador .

    ![Adicionar Empregado](./media/confluencemicrosoft-tutorial/user1.png)

1. Na secção Utilizadores, clique no **separador Adicionar utilizadores.** Na página de diálogo **Adicionar um utilizador,** execute os seguintes passos:

    ![Screenshot que mostra a "administração confluência" com o separador "Adicionar utilizadores" selecionado e "Adicionar um Utilizador" inseriu.000 informações.](./media/confluencemicrosoft-tutorial/user2.png)

    a. Na caixa de texto **username,** escreva o e-mail do utilizador como B.Simon.

    b. Na caixa de texto **'Nome Completo',** digite o nome completo do utilizador como B.Simon.

    c. Na caixa de texto **por e-mail,** digite o endereço de e-mail do utilizador como B.Simon@contoso.com .

    d. Na caixa de texto **da palavra-passe,** digite a palavra-passe para B.Simon.

    e. Clique **em Confirmar A Palavra-passe** reentre na palavra-passe.

    f. Clique no botão **Adicionar.**

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Confluence SAML SSO pelo URL de entrada de assinatura da Microsoft, onde pode iniciar o fluxo de login. 

* Vá diretamente ao Confluence SAML SSO pelo MICROSOFT Sign-on URL e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no SSO SamL de Confluence pela Microsoft nas Minhas Apps, este será redirecionado para Confluence SAML SSO por URL de assinatura da Microsoft. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Confluence SAML SSO pela Microsoft, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)