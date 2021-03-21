---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a Salesforce | Microsoft Docs'
description: Saiba como configurar o único sign-on entre o Azure Ative Directory e o Salesforce.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: jeedes
ms.openlocfilehash: 0f800d2d42d0d8815021f1582b04750d87aa5abc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651448"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>Tutorial: Azure Ative Directory integração única (SSO) com a Salesforce

Neste tutorial, você vai aprender a integrar a Salesforce com o Azure Ative Directory (Azure AD). Quando integrar a Salesforce com a AZure AD, pode:

* Controlo na Azure AD que tem acesso à Salesforce.
* Permita que os seus utilizadores sejam automaticamente inscritos na Salesforce com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição única de subscrição (SSO) ativada pela Salesforce.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Salesforce suporta SSO iniciado **SP.**

* A Salesforce suporta [o fornecimento e desprovisionamento **automatizados** do utilizador](salesforce-provisioning-tutorial.md) (recomendado).

* Salesforce suporta o fornecimento do utilizador **Just In Time.**

* A aplicação Salesforce Mobile pode agora ser configurada com Azure AD para permitir sSO. Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

## <a name="adding-salesforce-from-the-gallery"></a>Adicionar Salesforce da galeria

Para configurar a integração da Salesforce no Azure AD, é necessário adicionar a Salesforce da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Salesforce** na caixa de pesquisa.
1. Selecione **Salesforce** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-salesforce"></a>Configure e teste Azure AD SSO para a Salesforce

Configure e teste Azure AD SSO com a Salesforce utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Salesforce.

Para configurar e testar a Azure AD SSO com a Salesforce, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure salesforce SSO](#configure-salesforce-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Salesforce test user](#create-salesforce-test-user)** - para ter uma contraparte de B.Simon na Salesforce que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Salesforce,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:
    
    a. Na caixa de texto **identifier,** digite o valor utilizando o seguinte padrão:

    Conta empresarial: `https://<subdomain>.my.salesforce.com`

    Conta do programador: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. Na caixa de texto **URL de resposta,** digite o valor utilizando o seguinte padrão:

    Conta empresarial: `https://<subdomain>.my.salesforce.com`

    Conta do programador: `https://<subdomain>-dev-ed.my.salesforce.com`

    c. Na caixa de texto **URL de entrada de sinais,** digite o valor utilizando o seguinte padrão:

    Conta empresarial: `https://<subdomain>.my.salesforce.com`

    Conta do programador: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte a [equipa de suporte do Cliente Salesforce](https://help.salesforce.com/support) para obter estes valores.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar a Salesforce,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Salesforce.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Salesforce**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-salesforce-sso"></a>Configurar salesforce SSO

1. Para automatizar a configuração dentro do Salesforce, é necessário instalar a extensão do **navegador 'As aplicações' Secure's,** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar a Salesforce** irá direcioná-lo para a aplicação Sign-On Única Salesforce. A partir daí, forneça as credenciais de administração para assinar no Salesforce Single Sign-On. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-13.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar o Salesforce manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Salesforce como administrador e execute os seguintes passos:

1. Clique na **Configuração** no **ícone de definições** no canto superior direito da página.

    ![Configurar ícone de configurações de Sign-On únicas](./media/salesforce-tutorial/configure1.png)

1. Desloque-se até às **DEFINIÇÕES** no painel de navegação, clique em **Identidade** para expandir a secção relacionada. Em seguida, clique em **Definições Sign-On únicas**.

    ![Configurar configurar configurações de Sign-On únicas](./media/salesforce-tutorial/sf-admin-sso.png)

1. Na página **'Definições Sign-On única',** clique no botão **Editar.**

    ![Configure a edição de Sign-On única](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Se não conseguir ativar as definições de Sign-On única para a sua conta Salesforce, poderá ter de contactar a equipa de [suporte do Cliente Salesforce.](https://help.salesforce.com/support)

1. Selecione **SAML Ativado** e, em seguida, clique em **Guardar**.

    ![Configurar único Sign-On SAML habilitado](./media/salesforce-tutorial/sf-enable-saml.png)

1. Para configurar as definições de inscrição única do SAML, clique em **Novo A partir do Ficheiro de Metadados**.

    ![Configurar Sign-On Novo novo a partir do arquivo de metadados](./media/salesforce-tutorial/sf-admin-sso-new.png)

1. Clique **em Escolher Ficheiro** para carregar o ficheiro XML dos metadados que descarregou a partir do portal Azure e clique em **Criar**.

    ![Configurar o ficheiro de escolha de Sign-On individuais](./media/salesforce-tutorial/xmlchoose.png)

1. Na página **de Definições de Sign-On Única SAML,** os campos povoam-se automaticamente, se pretender utilizar o SAML JIT, selecione o **Utilizador A provisionamento ativado** e selecione **O Tipo de Identidade SAML** como **Afirmação contém o ID da Federação do objeto do Utilizador** de outra forma, desmarque o Dispositivo de Fornecimento de Utilizador **Ativado** e selecione **O Tipo de Identidade SAML** como **Afirmação contém o nome de utilizador da Salesforce do utilizador**. Clique em **Guardar**.

    ![Configure o provisionamento único Sign-On do utilizador habilitado](./media/salesforce-tutorial/salesforcexml.png)

1. No painel de navegação à esquerda no Salesforce, clique em **Definições da Empresa** para expandir a secção relacionada e, em seguida, clique em **My Domain**.

    ![Configurar single Sign-On Meu Domínio](./media/salesforce-tutorial/sf-my-domain.png)

1. Desloque-se para baixo para a secção **de Configuração de Autenticação** e clique no botão **Editar.**

    ![Configuração de autenticação única Sign-On única](./media/salesforce-tutorial/sf-edit-auth-config.png)

1. Na secção configuração de **autenticação,** verifique a **página** de login e o  **AzureSSO** como Serviço de **Autenticação** da sua configuração SSO SAML e, em seguida, clique em **Guardar**.

    ![Configure serviço único de autenticação Sign-On](./media/salesforce-tutorial/authentication.png)

    > [!NOTE]
    > Se for selecionado mais de um serviço de autenticação, os utilizadores são solicitados a selecionar em que serviço de autenticação gostam de iniciar a súmia enquanto iniciam uma única sação no seu ambiente Salesforce. Se não quer que aconteça, então deve **deixar todos os outros serviços de autenticação descontrolados.**

### <a name="create-salesforce-test-user"></a>Criar utilizador de teste Salesforce

Nesta secção, um utilizador chamado B.Simon é criado na Salesforce. A Salesforce suporta o provisionamento just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Salesforce, um novo é criado quando tenta aceder à Salesforce. A Salesforce também suporta o fornecimento automático de utilizadores, podendo encontrar mais detalhes [aqui](salesforce-provisioning-tutorial.md) sobre como configurar o fornecimento automático do utilizador.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de inscrição da Salesforce, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição da Salesforce e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo salesforce nas Minhas Apps, deverá ser automaticamente inscrito na Salesforce para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="test-sso-for-salesforce-mobile"></a>Teste SSO para Salesforce (Móvel)

1. Aplicação móvel Open Salesforce. Na placa na página, clique em **Utilizar o Domínio Personalizado.**

    ![App móvel Salesforce Use Domínio Personalizado](media/salesforce-tutorial/mobile-app1.png)

1. Na caixa de texto **de domínio personalizado,** insira o nome de domínio personalizado registado e clique em **Continuar**.

    ![Salesforce aplicativo móvel Domínio personalizado](media/salesforce-tutorial/mobile-app2.png)

1. Introduza as suas credenciais AD AZure para iniciar seduca na aplicação Salesforce e clique em **Seguinte**.

    ![Salesforce aplicativo móvel Credenciais Azure AD](media/salesforce-tutorial/mobile-app3.png)

1. Na página **'Permitir acesso'** como mostrado abaixo, clique em **Permitir** dar acesso à aplicação Salesforce.

    ![App móvel Salesforce Permite Acesso](media/salesforce-tutorial/mobile-app4.png)

1. Finalmente, após o início do sôm.

    ![Página inicial de aplicativos móveis ](media/salesforce-tutorial/mobile-app5.png) ![ Salesforce](media/salesforce-tutorial/mobile-app6.png)

## <a name="next-steps"></a>Passos Seguintes

Uma vez configurado salesforce, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O Controlo de Sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)