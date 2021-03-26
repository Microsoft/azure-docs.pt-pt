---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com Saba Cloud | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Saba Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 5ce9eb41755d7faa2ce00b38dfd971313443bfb7
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572695"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-cloud"></a>Tutorial: Azure Ative Directory integração única (SSO) com Saba Cloud

Neste tutorial, você vai aprender a integrar Saba Cloud com Azure Ative Directory (Azure AD). Quando integrar a Nuvem Saba com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Saba Cloud.
* Permita que os seus utilizadores sejam automaticamente inscritos na Saba Cloud com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por Saba Cloud (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Saba Cloud suporta **SP e IDP** iniciado SSO.
* Saba Cloud suporta o fornecimento **de utilizadores Just In Time.**
* A aplicação Saba Cloud Mobile pode agora ser configurada com Azure AD para ativar sSO. Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

## <a name="adding-saba-cloud-from-the-gallery"></a>Adicionando Nuvem Saba da galeria

Para configurar a integração da Saba Cloud em Azure AD, precisa adicionar Saba Cloud da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Saba Cloud** na caixa de pesquisa.
1. Selecione **Saba Cloud** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-saba-cloud"></a>Configurar e testar Azure AD SSO para Saba Cloud

Configure e teste Azure AD SSO com Saba Cloud usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Saba Cloud.

Para configurar e testar Azure AD SSO com Saba Cloud, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Saba Cloud SSO](#configure-saba-cloud-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Saba Cloud test user](#create-saba-cloud-test-user)** - para ter uma contraparte de B.Simon em Saba Cloud que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.
1. **[Teste SSO para Saba Cloud (móvel)](#test-sso-for-saba-cloud-mobile)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Saba Cloud,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `<CUSTOMER_NAME>_SPLN_PRINCIPLE`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<SIGN-ON URL>/Saba/saml/SSO/alias/<ENTITY_ID>`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<CUSTOMER_NAME>.sabacloud.com`

    b. Na caixa de texto **do Estado de Retransmissão,** digite um URL utilizando o seguinte padrão: `IDP_INIT---SAML_SSO_SITE=<SITE_ID> ` ou no caso de SAML ser configurado para um microsite, digite um URL utilizando o seguinte padrão: `IDP_INIT---SAML_SSO_SITE=<SITE_ID>---SAML_SSO_MICRO_SITE=<MicroSiteId>`

    > [!NOTE]
    > Para obter mais informações sobre a configuração do Retransmissor, consulte [este](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-idp-and-sp-initiated-sso-for-a-microsite.html) link.

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta, URL de inscrição e Estado de retransmissão. Contacte [a equipa de suporte do Cliente Saba Cloud](mailto:support@saba.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar a Nuvem Saba,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Saba Cloud.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Saba Cloud**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-saba-cloud-sso"></a>Configurar saba cloud Sso

1. Inscreva-se no site da empresa Saba Cloud como administrador.
1. Clique no ícone **do Menu** e clique em **Administração** e, em seguida, selecione o separador **Dedmin do Sistema.**

    ![screenshot para administração do sistema](./media/saba-cloud-tutorial/system.png)

1. No **Sistema configurar,** selecione **CONFIGURAÇÃO SSO SAML** e clique no botão **SETUP SAML SSO.** 

    ![screenshot para configuração](./media/saba-cloud-tutorial/configure.png)

1. Na janela pop-up, selecione **Microsite** a partir do dropdown e clique em **ADD AND CONFIGURE**.

    ![screenshot para adicionar site/microsite](./media/saba-cloud-tutorial/microsite.png)

1. Na secção **IDP configurar,** clique em **BROWSE** para carregar o ficheiro **XML de metadados da Federação,** que descarregou a partir do portal Azure. Ative a caixa de verificação específica do **IDP do site** e clique em **IMPORT**.

    ![screenshot para importação de certificado](./media/saba-cloud-tutorial/certificate.png) 

1. Na secção **Configure SP,** copie o valor da **Entidade Alias** e cole este valor na caixa de texto **identifier (Entity ID)** na secção **configuração de SAML básico** no portal Azure. Clique **em GERAR**.

    ![screenshot para Configure SP](./media/saba-cloud-tutorial/generate-metadata.png) 

1. Na secção **Configure Properties,** verifique os campos povoados e clique em **SAVE**. 

    ![screenshot para Configure Properties](./media/saba-cloud-tutorial/configure-properties.png) 

### <a name="create-saba-cloud-test-user"></a>Criar utilizador de teste Saba Cloud

Nesta secção, um utilizador chamado Britta Simon é criado em Saba Cloud. Saba Cloud suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Saba Cloud, um novo é criado após a autenticação.

> [!NOTE]
> Para ativar o fornecimento do utilizador SAML a tempo, consulte [esta](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-user-provisioning-with-saml.html) documentação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Saba Cloud Sign no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de inscrição da Nuvem Saba e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na Nuvem Saba para a qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo da Nuvem Saba nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na Nuvem Saba para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

> [!NOTE]
> Se o URL de entrada não for preenchido no Azure AD, a aplicação é tratada como modo iniciado por IDP e se o URL de entrada de entrada for povoado, o Azure AD irá sempre redirecionar o utilizador para a aplicação Saba Cloud para o fluxo iniciado pelo prestador de serviços.

## <a name="test-sso-for-saba-cloud-mobile"></a>Teste SSO para Nuvem Saba (móvel)

1. Abra a aplicação Saba Cloud Mobile, dê o Nome do **Site** na caixa de texto e clique **em Entrar**.

    ![Screenshot para o nome do site.](./media/saba-cloud-tutorial/site-name.png)

1. Insira o seu **endereço de e-mail** e clique em **Seguinte**.

    ![Screenshot para endereço de e-mail.](./media/saba-cloud-tutorial/email-address.png)

1. Finalmente após o sômeduque com sucesso, a página de aplicação será exibida.

    ![Screenshot para o sucesso do sinal.](./media/saba-cloud-tutorial/dashboard.png)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Saba Cloud, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


