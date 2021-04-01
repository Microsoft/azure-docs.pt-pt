---
title: 'Tutorial: Integração do Azure Ative Directory com a ArcGIS Online | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o ArcGIS Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/08/2021
ms.author: jeedes
ms.openlocfilehash: 7a445eefa31e741562105e89fa105d404ccc0c7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646355"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Tutorial: Integração do Azure Ative Directory com o ArcGIS Online

Neste tutorial, você vai aprender a integrar o ArcGIS Online com o Azure Ative Directory (Azure AD). Quando integra o ArcGIS Online com AZure AD, pode:

* Controle em Azure AD que tem acesso ao ArcGIS Online.
* Ative os seus utilizadores a serem automaticamente inscritos no ArcGIS Online com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada pelo ArcGIS Online (SSO).

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* ArcGIS Online suporta SSO iniciado **SP.**

## <a name="add-arcgis-online-from-the-gallery"></a>Adicione ArcGIS Online da galeria

Para configurar a integração do ArcGIS Online no AD Azure, é necessário adicionar o ArcGIS Online da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite ArcGIS Online** na caixa de pesquisa.
1. Selecione **ArcGIS Online** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-arcgis-online"></a>Configure e teste Azure AD SSO para ArcGIS Online

Configure e teste Azure AD SSO com ArcGIS Online usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no ArcGIS Online.

Para configurar e testar o Azure AD SSO com o ArcGIS Online, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o ArcGIS Online SSO](#configure-arcgis-online-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create ArcGIS Online test user](#create-arcgis-online-test-user)** - para ter uma contraparte de B.Simon no ArcGIS Online que está ligada à representação AD AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **Online ArcGIS,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<companyname>.maps.arcgis.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `<companyname>.maps.arcgis.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte ao Cliente Online ArcGIS](https://support.esri.com/en/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Para automatizar a configuração dentro do **ArcGIS Online,** é necessário instalar a **extensão do navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![image](./media/arcgis-tutorial/install-extension.png)

7. Depois de adicionar extensão ao navegador, clique na **configuração ArcGIS Online** irá direcioná-lo para a aplicação ArcGIS Online. A partir daí, forneça as credenciais de administração para assinar no ArcGIS Online. A extensão do navegador configurará automaticamente a aplicação para si e automatizará passos na secção **Configure ArcGIS Online Single Sign-On**.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao ArcGIS Online.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **ArcGIS Online**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-arcgis-online-sso"></a>Configurar arcgis online SSO

1. Se pretender configurar o ArcGIS Online manualmente, abra uma nova janela do navegador web e inicie sessão no site da empresa ArcGIS como administrador e execute os seguintes passos:

2. CLIQUE **EM EDITAR DEFINIÇÕES**.

    ![Editar Definições](./media/arcgis-tutorial/settings.png "Editar Definições")

3. Clique **em Segurança**.

    ![Segurança](./media/arcgis-tutorial/secure.png "Segurança")

4. Em **Logins empresariais,** clique **em SET IDENTITY PROVIDER**.

    ![Logins empresariais](./media/arcgis-tutorial/enterprise.png "Logins empresariais")

5. Na página de configuração **do Fornecedor de Identidade Definido,** execute os seguintes passos:

    ![Definir Fornecedor de Identidade](./media/arcgis-tutorial/identity-provider.png "Definir Fornecedor de Identidade")

    a. Na caixa de texto **Name,** escreva o nome da sua organização.

    b. Para **metadados para o Fornecedor de Identidade da Empresa será fornecido utilizando**, selecione Um **Ficheiro**.

    c. Para fazer o upload do seu ficheiro de metadados descarregado, clique em **Escolher o ficheiro**.

    d. CLIQUE **NO FORNECEDOR DE IDENTIDADE DEFINIDO.**

### <a name="create-arcgis-online-test-user"></a>Criar utilizador de teste ArcGIS Online

Para permitir que os utilizadores de Azure AD acedam ao ArcGIS Online, devem ser a provisionados no ArcGIS Online.  
No caso do ArcGIS Online, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Faça login no seu inquilino **ArcGIS.**

2. Clique **em CONVIDAR MEMBROS.**

    ![Convidar membros](./media/arcgis-tutorial/invite.png "Convidar membros")

3. **Selecione Adicionar os membros automaticamente sem enviar um e-mail** e, em seguida, clique em **SEGUINTE**.

    ![Adicionar membros automaticamente](./media/arcgis-tutorial/members.png "Adicionar membros automaticamente")

4. Na página de diálogo dos **membros,** execute os seguintes passos:

    ![Adicionar e rever](./media/arcgis-tutorial/review.png "Adicionar e rever")

     a. Introduza o **e-mail**, **Nome próprio** e **Último Nome** de uma conta AD válida que pretende obter.

     b. CLIQUE **EM ADICIONAR E REVER**.
5. Reveja os dados introduzidos e, em seguida, clique em **ADD MEMBERS**.

    ![Adicionar membro](./media/arcgis-tutorial/add.png "Adicionar membro")

    > [!NOTE]
    > O titular da conta Azure Ative Directory receberá um e-mail e seguirá um link para confirmar a sua conta antes de ficar ativa.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de inscrição on-on online ArcGIS, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição on-line ArcGIS e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Online ArcGIS nas Minhas Apps, este será redirecionado para o URL de inscrição online do ArcGIS. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o ArcGIS Online, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).