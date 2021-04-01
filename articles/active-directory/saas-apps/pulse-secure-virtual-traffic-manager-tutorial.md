---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Pulse Secure Virtual Traffic Manager | Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o Pulse Secure Virtual Traffic Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/11/2020
ms.author: jeedes
ms.openlocfilehash: 1e295075a5c1ae8daa6673757770bbef01208c1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92505749"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-virtual-traffic-manager"></a>Tutorial: Azure Ative Directy integração única de sign-on (SSO) com Pulse Secure Virtual Traffic Manager

Neste tutorial, você vai aprender a integrar Pulse Secure Virtual Traffic Manager com Azure Ative Directory (Azure AD). Quando integrar o Pulse Secure Virtual Traffic Manager com Azure AD, pode:

* Control em Azure AD que tem acesso a Pulse Secure Virtual Traffic Manager.
* Ative os seus utilizadores a serem automaticamente inscritos no Pulse Secure Virtual Traffic Manager com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Pulse Secure Virtual Traffic Manager única assinatura ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Pulse Secure Virtual Traffic Manager suporta **SSO** iniciado SP

## <a name="adding-pulse-secure-virtual-traffic-manager-from-the-gallery"></a>Adicionar Pulse Secure Virtual Traffic Manager da galeria

Para configurar a integração do Pulse Secure Virtual Traffic Manager em Azure AD, é necessário adicionar o Pulse Secure Virtual Traffic Manager da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Pulse Secure Virtual Traffic Manager** na caixa de pesquisa.
1. Selecione **Pulse Secure Virtual Traffic Manager** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-virtual-traffic-manager"></a>Configurar e testar Azure AD SSO para Pulse Secure Virtual Traffic Manager

Configure e teste Azure AD SSO com Pulse Secure Virtual Traffic Manager usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Pulse Secure Virtual Traffic Manager.

Para configurar e testar O Azure AD SSO com Pulse Secure Virtual Traffic Manager, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Pulse Secure Virtual Traffic Manager SSO](#configure-pulse-secure-virtual-traffic-manager-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. Crie o utilizador de **[teste DeRrsigure de Tráfego Virtual Secure Pulse](#create-pulse-secure-virtual-traffic-manager-test-user)** - para ter uma contraparte de B.Simon em Pulse Secure Virtual Traffic Manager que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Pulse Secure Virtual Traffic Manager,** encontre a secção **Gerir** e selecione um único sinal **de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<published virtual server FQDN>/saml/consume`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<published virtual server FQDN>/saml/metadata`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<published virtual server FQDN>/saml/consume`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente Do Gestor de Tráfego Virtual Pulse Secure](mailto:support@pulsesecure.net) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Pulse Secure Virtual Traffic Manager,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Pulse Secure Virtual Traffic Manager.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Pulse Secure Virtual Traffic Manager**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-pulse-secure-virtual-traffic-manager-sso"></a>Configure Pulse Secure Virtual Traffic Manager SSO

Esta secção cobre a configuração necessária para permitir a autenticação Azure AD SAML no Gestor de Tráfego Virtual Pulse. Todas as alterações de configuração são feitas no Pulse Virtual Traffic Manager utilizando a UI web Admin. 

#### <a name="create-a-saml-trusted-identity-provider"></a>Criar um fornecedor de identidade fidedigno SAML

a. Aceda ao catálogo de catálogo de > de fornecedores de **identidade fidedignas** da Pulse Virtual Traffic Manager, > SAML > e clique em **Editar**.

![página de catálogos saml](./media/pulse-secure-virtual-traffic-manager-tutorial/saml-catalogs.png)

b. Adicione os detalhes para o novo Fornecedor de Identidade Fidedigna SAML, copiando as informações da aplicação Azure AD Enterprise na página de definições de assinatura única e, em seguida, clique em **Criar Novo Fornecedor de Identidade Fidedigna**.

![Criar novo fornecedor de identidade fidedigna](./media/pulse-secure-virtual-traffic-manager-tutorial/create-trusted-identity-provider.png)

* Na caixa de texto **Name,** insira um nome para o fornecedor de identidade fidedigno. 

* Na **Entity_id** caixa de texto, insira o valor **do identificador Azure AD** que copiou a partir do portal Azure.  

* Na caixa de texto **url,** introduza o valor URL de **login** que copiou a partir do portal Azure. 

* Abra o **Certificado** descarregado do portal Azure para o Bloco de Notas e cole o conteúdo na caixa de texto **do Certificado.**

c. Verifique se o novo Fornecedor de Identidade SAML foi criado com sucesso. 

![Verificar Fornecedor de Identidade Fidedigna](./media/pulse-secure-virtual-traffic-manager-tutorial/verify-saml-identity-provider.png)

#### <a name="configure-the-virtual-server-to-use-azure-ad-authentication"></a>Configure o Servidor Virtual para utilizar a autenticação AD Azure

a. Aceda ao **gestor de tráfego virtual pulse Admin UI > Services >** página de Servidores Virtuais e clique em **Editar** ao lado do servidor Virtual anteriormente criado.

![Edição de Servidores Virtuais](./media/pulse-secure-virtual-traffic-manager-tutorial/virtual-servers.png)

b. Na secção **autenticação,** clique em **Editar**. 

![Secção de autenticação](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication.png)

c. Configure as seguintes definições de autenticação para o servidor virtual: 

1. Autenticação -

    ![definições de autenticação para servidor virtual](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-1.png)

    a. No **Auth!type,** selecione **PRESTADOR de Serviços SAML** 

    b. No **Auth!verbose,** definido para "Sim" para resolver problemas de autenticação, caso contrário, deixe o padrão como "Não" 

2. Gestão de Sessão de Autenticação -

    ![Gestão de Sessão de Autenticação](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-session.png)

    a. Para **Auth!session!cookie_name,** deixe o padrão como "VS_SamlSP_Auth" 

    b. Para **auth!session!timeout,** deixe o padrão para "7200" 

    c. Em **auth!session!log_external_state,** definido para "Sim" para resolver problemas de autenticação, caso contrário, deixe o padrão como "Não" 

    d. In **auth!session!cookie_attributes,** change to "HTTPOnly" 

3. Prestador de Serviços SAML -

    ![Prestador de Serviços SAML](./media/pulse-secure-virtual-traffic-manager-tutorial/saml-service-provider.png)

    a. Na caixa de texto **auth!saml!sp_entity_id,** definida para o mesmo URL utilizado como o Identificador de configuração única de sinalização Azure AD (ID da entidade). `https://pulseweb.labb.info/saml/metadata`Como. 

    b. No **auth!saml!sp_acs_url,** definido para o mesmo URL utilizado como o URL de repetição de repetição de um único sinal de Azure AD (URL de serviço ao consumidor de afirmação). `https://pulseweb.labb.info/saml/consume`Como. 

    c. No **auth!saml!idp,** selecione o **Fornecedor de Identidade Fidedigna** que criou no passo anterior. 

    d. No auth!saml!time_tolerance, deixe o padrão em "5" segundos. 

    e. No auth!saml!nameid_format, selecione **não especificado**.

    f. Aplicar alterações clicando em **Atualização** na parte inferior da página.
    
### <a name="create-pulse-secure-virtual-traffic-manager-test-user"></a>Criar utilizador de teste de Gestor de Tráfego Virtual Seguro de Pulso

Nesta secção, cria um utilizador chamado Britta Simon em Pulse Secure Virtual Traffic Manager. Trabalhe com [a equipa de suporte do Pulse Secure Virtual Traffic Manager](mailto:support@pulsesecure.net) para adicionar os utilizadores na plataforma Pulse Secure Virtual Traffic Manager. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

1. Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de entrada virtual do Gestor de Tráfego Virtual Pulse Secure, onde pode iniciar o fluxo de login. 

2. Vá diretamente ao URL de entrada virtual do Pulse Secure Traffic Manager e inicie o fluxo de login a partir daí.

3. Pode utilizar o Microsoft Access Panel. Quando clicar no azulejo do Gestor de Tráfego Virtual Pulse Secure no Painel de Acesso, este irá redirecionar para o URL de inscrição do Gestor de Tráfego Virtual Pulse Secure. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Assim que configurar o Pulse Secure Virtual Traffic Manager, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).