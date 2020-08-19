---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com a Salesforce Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Salesforce.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2020
ms.author: jeedes
ms.openlocfilehash: 7228f4fbf348b8112654ece91aa5e9e831ac1201
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543572"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>Tutorial: Azure Ative Directory integração única (SSO) com a Salesforce

Neste tutorial, você vai aprender a integrar a Salesforce com o Azure Ative Directory (Azure AD). Quando integrar a Salesforce com a AZure AD, pode:

* Controlo na Azure AD que tem acesso à Salesforce.
* Permita que os seus utilizadores sejam automaticamente inscritos na Salesforce com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição única de subscrição (SSO) ativada pela Salesforce.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Salesforce suporta **SSO** iniciado sp

* Salesforce suporta [fornecimento e desprovisionamento **automatizados** de utilizadores](salesforce-provisioning-tutorial.md) (recomendado)

* Salesforce suporta **provisão de** utilizadores just in time

* A aplicação Salesforce Mobile pode agora ser configurada com Azure AD para permitir sSO. Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.
* Uma vez configurado salesforce, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O Controlo de Sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-from-the-gallery"></a>Adicionar Salesforce da galeria

Para configurar a integração da Salesforce no Azure AD, é necessário adicionar a Salesforce da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Salesforce** na caixa de pesquisa.
1. Selecione **Salesforce** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce"></a>Configurar e testar Azure AD único sign-on para Salesforce

Configure e teste Azure AD SSO com a Salesforce utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Salesforce.

Para configurar e testar a Azure AD SSO com a Salesforce, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure salesforce SSO](#configure-salesforce-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Salesforce test user](#create-salesforce-test-user)** - para ter uma contraparte de B.Simon na Salesforce que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Salesforce,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de entrada de sinais,** digite o valor utilizando o seguinte padrão:

    Conta empresarial: `https://<subdomain>.my.salesforce.com`

    Conta do programador: `https://<subdomain>-dev-ed.my.salesforce.com`
    
    b. Na caixa de texto **URL de resposta,** digite o valor utilizando o seguinte padrão:

    Conta empresarial: `https://<subdomain>.my.salesforce.com`

    Conta do programador: `https://<subdomain>-dev-ed.my.salesforce.com`

    c. Na caixa de texto **identifier,** digite o valor utilizando o seguinte padrão:

    Conta empresarial: `https://<subdomain>.my.salesforce.com`

    Conta do programador: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL e o identificador de inscrição real. Contacte a [equipa de suporte do Cliente Salesforce](https://help.salesforce.com/support) para obter estes valores.

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

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Salesforce**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-salesforce-sso"></a>Configurar salesforce SSO

1. Para automatizar a configuração dentro do Salesforce, é necessário instalar a extensão do **navegador 'As aplicações' Secure's,** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar o Salesforce** irá direcioná-lo para a aplicação Salesforce Single Sign-On. A partir daí, forneça as credenciais de administração para assinar no Salesforce Single Sign-On. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-13.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar o Salesforce manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Salesforce como administrador e execute os seguintes passos:

1. Clique na **Configuração** no **ícone de definições** no canto superior direito da página.

    ![Configurar um único sign-on](./media/salesforce-tutorial/configure1.png)

1. Desloque-se até às **DEFINIÇÕES** no painel de navegação, clique em **Identidade** para expandir a secção relacionada. Em seguida, clique em **Definições de Inscrição únicas**.

    ![Configurar um único sign-on](./media/salesforce-tutorial/sf-admin-sso.png)

1. Na página **'Definições de 'Sessão',** clique no botão **Editar.**

    ![Configurar um único sign-on](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Se não conseguir ativar as definições de 'Sign-On' único para a sua conta Salesforce, poderá ter de contactar a equipa de [suporte do Cliente Salesforce.](https://help.salesforce.com/support)

1. Selecione **SAML Ativado**e, em seguida, clique em **Guardar**.

    ![Configurar um único sign-on](./media/salesforce-tutorial/sf-enable-saml.png)

1. Para configurar as definições de inscrição única do SAML, clique em **Novo A partir do Ficheiro de Metadados**.

    ![Configurar um único sign-on](./media/salesforce-tutorial/sf-admin-sso-new.png)

1. Clique **em Escolher Ficheiro** para carregar o ficheiro XML dos metadados que descarregou a partir do portal Azure e clique em **Criar**.

    ![Configurar um único sign-on](./media/salesforce-tutorial/xmlchoose.png)

1. Na página **de Definições únicas de sinais de SDOL,** os campos povoam-se automaticamente, selecionam o Provisionamento do **Utilizador Ativado** e, em seguida, clique em **Guardar**.

    ![Configurar um único sign-on](./media/salesforce-tutorial/salesforcexml.png)

1. No painel de navegação à esquerda no Salesforce, clique em **Definições da Empresa** para expandir a secção relacionada e, em seguida, clique em **My Domain**.

    ![Configurar um único sign-on](./media/salesforce-tutorial/sf-my-domain.png)

1. Desloque-se para baixo para a secção **de Configuração de Autenticação** e clique no botão **Editar.**

    ![Configurar um único sign-on](./media/salesforce-tutorial/sf-edit-auth-config.png)

1. Na secção Configuração de **Autenticação,** Verifique o Serviço de **Autenticação** **AzureSSO** da sua configuração SSO SAML e, em seguida, clique em **Guardar**.

    ![Configurar um único sign-on](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Se for selecionado mais de um serviço de autenticação, os utilizadores são solicitados a selecionar em que serviço de autenticação gostam de iniciar a súmia enquanto iniciam uma única sação no seu ambiente Salesforce. Se não quer que aconteça, então deve **deixar todos os outros serviços de autenticação descontrolados.**

### <a name="create-salesforce-test-user"></a>Criar utilizador de teste Salesforce

Nesta secção, um utilizador chamado B.Simon é criado na Salesforce. A Salesforce suporta o provisionamento just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Salesforce, um novo é criado quando tenta aceder à Salesforce. A Salesforce também suporta o fornecimento automático de utilizadores, podendo encontrar mais detalhes [aqui](salesforce-provisioning-tutorial.md) sobre como configurar o fornecimento automático do utilizador.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo salesforce no Painel de Acesso, deverá ser automaticamente inscrito na Salesforce para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="test-sso-for-salesforce-mobile"></a>Teste SSO para Salesforce (Móvel)

1. Aplicação móvel Open Salesforce. Na placa na página, clique em **Utilizar o Domínio Personalizado.**

    ![App móvel Salesforce](media/salesforce-tutorial/mobile-app1.png)

1. Na caixa de texto **de domínio personalizado,** insira o nome de domínio personalizado registado e clique em **Continuar**.

    ![App móvel Salesforce](media/salesforce-tutorial/mobile-app2.png)

1. Introduza as suas credenciais AD AZure para iniciar seduca na aplicação Salesforce e clique em **Seguinte**.

    ![App móvel Salesforce](media/salesforce-tutorial/mobile-app3.png)

1. Na página **'Permitir acesso'** como mostrado abaixo, clique em **Permitir** dar acesso à aplicação Salesforce.

    ![App móvel Salesforce](media/salesforce-tutorial/mobile-app4.png)

1. Finalmente, após o início do sôm.

    ![App móvel Salesforce ](media/salesforce-tutorial/mobile-app5.png) ![ Salesforce](media/salesforce-tutorial/mobile-app6.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar o Provisionamento do Utilizador](salesforce-provisioning-tutorial.md)

- [Experimente a Salesforce com a Azure AD](https://aad.portal.azure.com)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a Salesforce com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/protect-salesforce)
