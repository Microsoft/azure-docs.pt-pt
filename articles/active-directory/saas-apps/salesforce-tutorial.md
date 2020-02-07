---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Salesforce | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 564e8dd8e6d7c4c0e3747469875e030be30b1a9b
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046703"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Salesforce

Neste tutorial, você aprenderá a integrar o Salesforce ao Azure Active Directory (Azure AD). Ao integrar o Salesforce ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Salesforce.
* Habilite seus usuários a serem conectados automaticamente ao Salesforce com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura habilitada para SSO (logon único) do Salesforce.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Salesforce apoia **SP** iniciado SSO

* A Salesforce suporta [o fornecimento e o deprovisionamento **automatizado** ](salesforce-provisioning-tutorial.md) de utilizadores (recomendado)

* Salesforce suporta o provisionamento de utilizadores **justo no tempo**

* Agora, o aplicativo móvel do Salesforce pode ser configurado com o Azure AD para habilitar o SSO. Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.
* Assim que configurar a Salesforce, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Controlo de Sessão estende-se a partir de Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-from-the-gallery"></a>Adicionando o Salesforce da Galeria

Para configurar a integração do Salesforce ao Azure AD, você precisará adicionar o Salesforce da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Salesforce** na caixa de pesquisa.
1. Selecione **Salesforce** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce"></a>Configurar e testar o logon único do Azure AD para o Salesforce

Configure e teste Azure AD SSO com Salesforce utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Salesforce.

Para configurar e testar o SSO do Azure AD com o Salesforce, conclua os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure salesforce SSO](#configure-salesforce-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * Criar o utilizador de **[teste Salesforce](#create-salesforce-test-user)** - para ter uma contrapartida da B.Simon na Salesforce que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Salesforce,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **smS sign-on** URL, escreva o valor utilizando o seguinte padrão:

    Conta empresarial: `https://<subdomain>.my.salesforce.com`

    Conta de desenvolvimento: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. Na caixa de texto **identificador,** digite o valor utilizando o seguinte padrão:

    Conta empresarial: `https://<subdomain>.my.salesforce.com`

    Conta de desenvolvimento: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contacte a equipa de suporte ao [Cliente salesforce](https://help.salesforce.com/support) para obter estes valores.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na secção **"set up Salesforce",** copie os URL(s) adequados com base no seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **de nome do utilizador,** introduza o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao Salesforce.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Salesforce**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![O link Adicionar usuário](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-salesforce-sso"></a>Configurar SSO do Salesforce

1. Para automatizar a configuração dentro do Salesforce, precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar a Salesforce** irá direcioná-lo para a aplicação Salesforce Single Sign-On. A partir daí, forneça as credenciais de administrador para entrar no logon único do Salesforce. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-13.

    ![Configuração da instalação](common/setup-sso.png)

1. Se você quiser configurar o Salesforce manualmente, abra uma nova janela do navegador da Web e entre no site da sua empresa do Salesforce como administrador e execute as seguintes etapas:

1. Clique no ícone **configuração** no **canto** superior direito da página.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/configure1.png)

1. Desloque-se até às **DEFINIÇÕES** no painel de navegação, clique em **Identidade** para expandir a secção relacionada. Em seguida, clique em **Definições de Sinal Único**.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-admin-sso.png)

1. Na página **De definições de sinal único,** clique no botão **Editar.**

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Se não conseguir ativar as definições de Sign-On single para a sua conta Salesforce, poderá ter de contactar a equipa de suporte ao [Cliente da Salesforce](https://help.salesforce.com/support).

1. Selecione **SAML Ativado**, e, em seguida, clique em **Guardar**.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-enable-saml.png)

1. Para configurar as definições de inscrição única sAML, clique em **Novo ficheiro de Metadados**.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-admin-sso-new.png)

1. Clique **em Escolher Ficheiro** para carregar o ficheiro XML de metadados que descarregou a partir do portal Azure e clique em **Criar**.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/xmlchoose.png)

1. Na página **De Definições de Sinais Únicos SAML,** os campos povoam-se automaticamente e clicam em guardar.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/salesforcexml.png)

1. No painel de navegação à esquerda no Salesforce, clique em **Definições da Empresa** para expandir a secção relacionada e, em seguida, clique no **Meu Domínio**.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-my-domain.png)

1. Desloque-se até à secção de **Configuração de Autenticação** e clique no botão **Editar.**

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-edit-auth-config.png)

1. Na secção de **Configuração de Autenticação,** verifique o **AzureSSO** como Serviço de **Autenticação** da sua configuração SAML SSO e, em seguida, clique em **Guardar**.

    ![Configurar o início de sessão único](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Se mais de um serviço de autenticação estiver selecionado, os usuários receberão uma solicitação para selecionar a qual serviço de autenticação ele deseja entrar enquanto inicia o logon único no ambiente do Salesforce. Se não quer que aconteça, então deve deixar todos os outros serviços de **autenticação sem controlo.**

### <a name="create-salesforce-test-user"></a>Criar usuário de teste do Salesforce

Nesta seção, um usuário chamado B. Simon é criado no Salesforce. O Salesforce dá suporte ao provisionamento just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no Salesforce, um novo será criado quando você tentar acessar o Salesforce. A Salesforce também suporta o fornecimento automático de utilizadores, pode encontrar mais detalhes [aqui](salesforce-provisioning-tutorial.md) sobre como configurar o fornecimento automático de utilizadores.

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Salesforce no painel de acesso, você deverá ser conectado automaticamente ao Salesforce para o qual você configurou o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="test-sso-for-salesforce-mobile"></a>Testar SSO para Salesforce (móvel)

1. Abra o aplicativo móvel do Salesforce. Na página de sinal de inscrição, clique em **Use Custom Domain**.

    ![Aplicativo móvel do Salesforce](media/salesforce-tutorial/mobile-app1.png)

1. Na caixa de texto **de domínio personalizado,** introduza o nome de domínio personalizado registado e clique em **Continuar**.

    ![Aplicativo móvel do Salesforce](media/salesforce-tutorial/mobile-app2.png)

1. Insira as suas credenciais De DA Azure para iniciar sessão na aplicação Salesforce e clique **em Next**.

    ![Aplicativo móvel do Salesforce](media/salesforce-tutorial/mobile-app3.png)

1. Na página **Permitir o Acesso,** como mostrado abaixo, clique **em Permitir** dar acesso à aplicação Salesforce.

    ![Aplicativo móvel do Salesforce](media/salesforce-tutorial/mobile-app4.png)

1. Por fim, após entrar com êxito, a home page do aplicativo será exibida.

    aplicação móvel ![Salesforce](media/salesforce-tutorial/mobile-app5.png) ![](media/salesforce-tutorial/mobile-app6.png) de aplicação móvel da Salesforce

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar o fornecimento de utilizadores](salesforce-provisioning-tutorial.md)

- [Experimente a Salesforce com a Azure AD](https://aad.portal.azure.com)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a Salesforce com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/protect-salesforce)