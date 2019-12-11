---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com Akamai | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Akamai.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1b7e0d7a-e78f-43a5-af93-b626186e2376
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 042dd242285081001ca48c9f17e4d42c2294c0ff
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979596"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Akamai

Neste tutorial, você aprenderá a integrar o Akamai ao Azure Active Directory (Azure AD). Ao integrar o Akamai ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Akamai.
* Habilite seus usuários a serem automaticamente conectados ao Akamai com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) da Akamai.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

- A margem de atraso dá suporte ao SSO iniciado pelo IDP

## <a name="adding-akamai-from-the-gallery"></a>Adicionando o Akamai da Galeria

Para configurar a integração do Akamai ao Azure AD, você precisará adicionar o Akamai da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Akamai** na caixa de pesquisa.
1. Selecione **Akamai** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-akamai"></a>Configurar e testar o logon único do Azure AD para o Akamai

Configure e teste o SSO do Azure AD com o Akamai usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Akamai.

Para configurar e testar o SSO do Azure AD com o Akamai, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o AKAMAI SSO](#configure-akamai-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do Akamai](#create-akamai-test-user)** – para ter um equivalente de B. Simon no Akamai que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Akamai** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://<Yourapp>.login.go.akamai-access.com/sp/response`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https:// <Yourapp>.login.go.akamai-access.com/sp/response`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador e a URL de resposta reais. Contate a [equipe de suporte ao cliente do Akamai](https://www.akamai.com/us/en/contact-us/) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar o Akamai** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao Akamai.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Akamai**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-akamai-sso"></a>Configurar o Akamai SSO

### <a name="setting-up-idp"></a>Configurando o IDP

1. Entre no **Akamai Enterprise Application Access** console.
1. No **console do AKAMAI EAA**, selecione **identidade** > **provedores de identidade**.

    ![Configurando a Akamai](./media/header-akamai-tutorial/configure01.png)

1. Clique em **Adicionar provedor de identidade**.

    ![Configurando a Akamai](./media/header-akamai-tutorial/configure02.png)

    a. Especifique o **nome exclusivo**.
    
    b. Escolha **SAML** de terceiros e clique em **criar provedor de identidade e configurar**.

### <a name="general-settings"></a>Definições Gerais

1. **Interceptação de identidade** – especifique o nome do (URL base do SP – será usado para a configuração do Azure AD)

    > [!NOTE]
    > Você pode optar por ter seu próprio domínio personalizado (exigirá uma entrada DNS e um certificado). Neste exemplo, vamos usar o domínio Akamai.

1. **Zona da nuvem Akamai** – selecione a zona de nuvem apropriada.
1. **Validação de certificado** -Verifique a documentação do Akamai (opcional)

    ![Configurando a Akamai](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>Configuração de autenticação

1. URL – especifique a URL igual à sua interceptação de identidade (é aqui que os usuários são redirecionados após a autenticação).
2. URL de logout: Atualize a URL de logout.
3. Assinar solicitação SAML: padrão desmarcado.
4. Para o arquivo de metadados IDP, adicione o aplicativo no console do AD do Azure.

    ![Configurando a Akamai](./media/header-akamai-tutorial/configure04.png)

### <a name="header-based-authentication"></a>Autenticação baseada em cabeçalho

Autenticação baseada em cabeçalho Akamai

1. Escolha o formulário **http personalizado** no Assistente para adicionar aplicativos.

    ![Configurando a Akamai](./media/header-akamai-tutorial/configure05.png)

    ![Configurando a Akamai](./media/header-akamai-tutorial/configure06.png)

    ![Configurando a Akamai](./media/header-akamai-tutorial/configure07.png)

    ![Configurando a Akamai](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>Autenticação

![Configurando a Akamai](./media/header-akamai-tutorial/configure09.png)

![Configurando a Akamai](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>Serviços

1. Clique em salvar e vá para autenticação.

![Configurando a Akamai](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>Definições Avançadas

1. Nos **cabeçalhos HTTP do cliente**, especifique o atributo **CustomerHeader** e **SAML**.

    ![Configurando a Akamai](./media/header-akamai-tutorial/configure12.png)

1. Clique **no botão salvar e ir para implantação** .

    ![Configurando a Akamai](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>Implantar o aplicativo

1. Clique no botão **implantar aplicativo** .

    ![Configurando a Akamai](./media/header-akamai-tutorial/configure14.png)

1. Verifique se o aplicativo foi implantado com êxito.

    ![Configurando a Akamai](./media/header-akamai-tutorial/configure15.png)

### <a name="kerberos-authentication"></a>Autenticação Kerberos

#### <a name="remote-desktop"></a>Ambiente de Trabalho Remoto

1. Escolha **RDP** no Assistente para adicionar aplicativos.

    ![Configurando a Akamai](./media/header-akamai-tutorial/configure16.png)

    ![Configurando a Akamai](./media/header-akamai-tutorial/configure17.png)

    ![Configurando a Akamai](./media/header-akamai-tutorial/configure18.png)

1. Especifique o conector que atenderá a isso.

    ![Configurando a Akamai](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>Autenticação

Clique em **salvar e vá para serviços**.

![Configurando a Akamai](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>Serviços

Clique em **salvar e vá para configurações avançadas**.

![Configurando a Akamai](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>Definições Avançadas

Clique em **salvar e vá para implantação**.

![Configurando a Akamai](./media/header-akamai-tutorial/configure22.png)

![Configurando a Akamai](./media/header-akamai-tutorial/configure23.png)

![Configurando a Akamai](./media/header-akamai-tutorial/configure24.png)

### <a name="deployment"></a>Implementação

#### <a name="ssh"></a>SSH

1. Vá para adicionar aplicativos, escolha **SSH**.

    ![Configurando a Akamai](./media/header-akamai-tutorial/configure25.png)

    ![Configurando a Akamai](./media/header-akamai-tutorial/configure26.png)

1. Configurar a identidade do aplicativo.

    ![Configurando a Akamai](./media/header-akamai-tutorial/configure27.png)

    a. Especifique o nome/descrição.

    b. Especifique o IP/FQDN e a porta do servidor de aplicativos para SSH.

    c. Especifique o nome de usuário/senha SSH * Verifique Akamai EAA.

    d. Especifique o nome do host externo.

    e. Especifique o local para o conector e escolha o conector.

#### <a name="authentication"></a>Autenticação

Clique em **salvar e vá para serviços**.

![Configurando a Akamai](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>Serviços

Clique em **salvar e vá para configurações avançadas**.

![Configurando a Akamai](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>Definições Avançadas

Clique em salvar e ir para a implantação

![Configurando a Akamai](./media/header-akamai-tutorial/configure30.png)

![Configurando a Akamai](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>Implementação

Clique em **implantar aplicativo**.

![Configurando a Akamai](./media/header-akamai-tutorial/configure32.png)

### <a name="kerberos-applications"></a>Aplicativos Kerberos

#### <a name="adding-directory"></a>Adicionando diretório

![Configurando a Akamai](./media/header-akamai-tutorial/configure33.png)

![Configurando a Akamai](./media/header-akamai-tutorial/configure34.png)

![Configurando a Akamai](./media/header-akamai-tutorial/configure35.png)

![Configurando a Akamai](./media/header-akamai-tutorial/configure36.png)

### <a name="create-akamai-test-user"></a>Criar usuário de teste da Akamai

Nesta seção, você criará um usuário chamado B. Simon no Akamai. Trabalhe com a [equipe de suporte ao cliente do Akamai](https://www.akamai.com/us/en/contact-us/) para adicionar os usuários na plataforma Akamai. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco Akamai no painel de acesso, você deverá ser conectado automaticamente à Akamai para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Akamai com o Azure AD](https://aad.portal.azure.com/)
