---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Citrix Netscaler (autenticação baseada em Kerberos) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Citrix Netscaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75e825f55a890be49000e209859670caa2c1c875
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431361"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-citrix-netscaler-kerberos-based-authentication"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Citrix Netscaler (autenticação baseada em Kerberos)

Neste tutorial, você aprenderá a integrar o Citrix Netscaler ao Azure Active Directory (Azure AD). Ao integrar o Citrix Netscaler ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Citrix Netscaler.
* Habilite seus usuários a entrar automaticamente no Citrix Netscaler com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Citrix netdimension.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Citrix netdimensioner dá suporte ao SSO iniciado pelo **SP**

* O Citrix Netscaler dá suporte ao provisionamento **de usuário just in time**

- [Configurar o logon único do Citrix netdimensioner para autenticação baseada em Kerberos](#configure-citrix-netscaler-single-sign-on-for-kerberos-based-authentication)

- [Configurar o logon único do Citrix netdimensioner para autenticação baseada em cabeçalho](header-citrix-netscaler-tutorial.md)

## <a name="adding-citrix-netscaler-from-the-gallery"></a>Adicionando o Citrix Netscaler da Galeria

Para configurar a integração do Citrix Netscaler ao Azure AD, você precisa adicionar o Citrix Netscaler da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Citrix Netscaler** na caixa de pesquisa.
1. Selecione **Citrix Netscaler** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Configurar e testar o logon único do Azure AD para o Citrix Netscaler

Configure e teste o SSO do Azure AD com o Citrix Netscaler usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Citrix Netscaler.

Para configurar e testar o SSO do Azure AD com o Citrix Netscaler, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o Citrix Netscaler SSO](#configure-citrix-netscaler-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Citrix Netscaler](#create-citrix-netscaler-test-user)** – para ter um equivalente de B. Simon no Citrix Netscaler que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Citrix Netscaler** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://<<Your FQDN>>`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de logon, o identificador e a URL de resposta reais. Contate a [equipe de suporte ao cliente do Citrix Netscaler](https://www.citrix.com/contact/technical-support.html) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

    > [!NOTE]
    > Para que o SSO funcione, essas URLs devem ser acessíveis de sites públicos. Você precisa habilitar o firewall ou outras configurações de segurança no lado do Netscaler para enble o Azure AD para postar o token na URL do ACS configurada.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize a **URL de metadados de Federação do aplicativo**, copie a URL e salve-a no bloco de notas.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar o Citrix Netescalar** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao Citrix Netscaler.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Citrix Netscaler**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-citrix-netscaler-sso"></a>Configurar o SSO do Citrix netscaleer

- [Configurar o logon único do Citrix netdimensioner para autenticação baseada em Kerberos](#configure-citrix-netscaler-single-sign-on-for-kerberos-based-authentication)

- [Configurar o logon único do Citrix netdimensioner para autenticação baseada em cabeçalho](header-citrix-netscaler-tutorial.md)

### <a name="publishing-web-server"></a>Publicando o servidor Web 

1. Crie um **servidor virtual**.

    a. Vá para **Gerenciamento de tráfego > balanceamento de carga > serviços**.
    
    b. Clique em **Adicionar**.

    ![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/web01.png)

    c. Especifique os detalhes do servidor Web que está executando os aplicativos abaixo:
    * **Nome do serviço**
    * **IP do servidor/servidor existente**
    * **Protocolo**
    * **Porta**

     ![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/web01.png)

### <a name="configuring-load-balancer"></a>Configurando Load Balancer

1. Para configurar Load Balancer, execute as seguintes etapas:

    ![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/load01.png)

    a. Vá para **Gerenciamento de tráfego > balanceamento de carga > servidores virtuais**.

    b. Clique em **Adicionar**.

    c. Especifique os detalhes abaixo:

    * **Nome**
    * **Protocolo**
    * **Endereço IP**
    * **Porta**
    * Clique em **OK**

### <a name="bind-virtual-server"></a>Associar servidor virtual

Associe o Load Balancer ao servidor virtual criado anteriormente.

![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/bind01.png)

![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/bind02.png)

### <a name="bind-certificate"></a>Associar certificado

Como iremos publicar esse serviço como SSL, associe o certificado do servidor e teste seu aplicativo.

![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/bind03.png)

![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Perfil SAML do Citrix ADC

### <a name="create-authentication-policy"></a>Criar política de autenticação

1. Vá para **segurança > AAA – tráfego de aplicativo > políticas > autenticação > políticas de autenticação**.

2. Clique em **Adicionar** e especifique os detalhes.

    ![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/policy01.png)

    a. Nome da **política de autenticação**.

    b. Expressão: **true**.

    c. Tipo de ação **SAML**.

    d. Ação = clique em **Adicionar** (siga o assistente para criar autenticação do servidor SAML).
    
    e. Clique em criar na **política de autenticação**.

### <a name="create-authentication-saml-server"></a>Criar servidor SAML de autenticação

1. Execute as seguintes etapas:

    ![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/server01.png)

    a. Especifique o **nome**.

    b. Importar metadados (especifique a URL de metadados de Federação da interface do usuário do SAML do Azure que você copiou acima).
    
    c. Especifique o **nome do emissor**.

    d. Clique em **criar**.

### <a name="create-authentication-virtual-server"></a>Criar servidor virtual de autenticação

1.  Vá para **segurança > AAA-tráfego de aplicativo > > servidores virtuais de autenticação**.

2.  Clique em **Adicionar** e execute as seguintes etapas:

    ![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/server02.png)

    a.  Fornecer um **nome**.

    b.  Escolha **não endereçável**.

    c.  Protocolo **SSL**.

    d.  Clique em **OK**.

    e.  Clique em **Continue** (Continuar).

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Configurar o servidor virtual de autenticação para usar o Azure AD

Será necessário modificar as 2 seções do servidor virtual de autenticação.

1.  **Políticas de autenticação avançada**

    ![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/virtual01.png)

    a. Selecione a **política de autenticação** que você criou anteriormente.

    b. Clique em **associar**.

      ![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/virtual02.png)

2. **Servidores virtuais baseados em formulário**

    ![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/virtual03.png)

    a.  Você precisará fornecer um **FQDN** desde seu imposto pela interface do usuário.

    b.  Escolha o **Load Balancer do Virtual Server** que você gostaria de proteger com a autenticação do Azure AD.

    c.  Clique em **associar**.

    ![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/virtual04.png)

    >[!NOTE]
    >Certifique-se de clicar em **concluído** na página configuração do servidor virtual de autenticação também.

3. Verifique as alterações. Navegue até a URL do aplicativo. Você deve ver sua página de logon de locatário em vez de acesso não autenticado anteriormente.

    ![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-single-sign-on-for-kerberos-based-authentication"></a>Configurar o logon único do Citrix netdimensioner para autenticação baseada em Kerberos

### <a name="create-a-kerberos-delegation-account-for-citrix-adc"></a>Criar uma conta de delegação Kerberos para o Citrix ADC

1. Crie uma conta de usuário (neste exemplo AppDelegation).

    ![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/kerberos01.png)

2. Configure um SPN de HOST nessas contas.

    * SetSPN-S HOST/AppDelegation. IDENTt. identt\appdelegation de trabalho
    
        No exemplo acima

        a. IDENT. trabalho (FQDN do domínio)

        b. Idente (nome NetBIOS do domínio)

        c. AppDelegation (nome da conta de usuário de delegação)

3. Configurar a delegação para o servidor da 
 
    ![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/kerberos02.png)

    >[!NOTE]
    >No exemplo acima, o nome interno do webserver executando o site WIA é cweb2

### <a name="citrix-aaa-kcd--kerberos-delegation-accounts"></a>Citrix AAA KCD (contas de delegação de Kerberos)

1.  Vá para **contas do gateway do Citrix > AAA KCD (delegação restrita de Kerberos)** .

2.  Clique em Adicionar e especifique os detalhes abaixo:

    a.  Especifique o **nome**.

    b.  **Realm**.

    c.  `http/<host/fqdn>@DOMAIN.COM`do **SPN do serviço** .
    
    >[!NOTE]
    >@DOMAIN.com é obrigatório e em letras maiúsculas.

    d.  Especifique a **conta de usuário delegado**.

    e.  Verifique a senha do usuário delegado e especifique a **senha**.

    f.  Clique em **OK**.
 
    ![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/kerberos03.png)

### <a name="citrix-traffic-policy-and-traffic-profile"></a>Política de tráfego de Citrix e perfil de tráfego

1.  Vá para **segurança > AAA-tráfego de aplicativo > políticas > políticas de tráfego, perfis e formulários ProfilesTraffic de SSO**.

2.  Selecione **perfis de tráfego**.

3.  Clique em **Adicionar**.

4.  Configurar o perfil de tráfego.

    a.  Especifique o **nome**.

    b.  Especifique o **logon único**.

    c.  Especifique a **conta do KCD** criada em uma etapa anterior na lista suspensa.

    d.  Clique em **OK**.

    ![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/kerberos04.png)
 
5.  Selecione a **política de tráfego**.

6.  Clique em **Adicionar**.

7.  Configurar a política de tráfego.

    a.  Especifique o **nome**.

    b.  Escolha o perfil de **tráfego** criado anteriormente na lista suspensa.

    c.  Defina expressão como **true**.

    d.  Clique em **OK**.

    ![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/kerberos05.png)

### <a name="citrix-bind-traffic-policy-to-virtual-servers"></a>Política de tráfego de associação Citrix para servidores virtuais

Para associar uma política de tráfego a um servidor virtual específico usando a GUI.

* Navegue até **Gerenciamento de tráfego > balanceamento de carga > servidores virtuais**.

* Na lista painel de detalhes de servidores virtuais, selecione o **servidor virtual** ao qual você deseja associar a política de regravação e clique em **abrir**.

* Na caixa de diálogo Configurar servidor virtual (balanceamento de carga), selecione a **guia políticas**. Todas as políticas configuradas em seu netescalar aparecem na lista.
 
    ![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/kerberos06.png)

    ![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/kerberos07.png)

1.  Marque a **caixa de seleção** ao lado do nome da política que você deseja associar a este servidor virtual.
 
    ![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/kerberos08.png)

    ![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/kerberos09.png)

1. Somente a política está associada, clique em **concluído**.
 
    ![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/kerberos10.png)

1. Teste usando o site integrado do Windows.

    ![Configuração do Citrix Netscaler](./media/citrix-netscaler-tutorial/kerberos11.png)    

### <a name="create-citrix-netscaler-test-user"></a>Criar um usuário de teste do Citrix netscaleer

Nesta seção, um usuário chamado B. Simon é criado no Citrix Netscaler. O Citrix Netscaler dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no Citrix Netscaler, um novo será criado após a autenticação.

> [!NOTE]
> Se precisar criar um usuário manualmente, entre em contato com a equipe de [suporte ao cliente Citrix Netscaler](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco Citrix Netscaler no painel de acesso, você deverá entrar automaticamente no Citrix Netscaler para o qual você configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Citrix Netscaler com o Azure AD](https://aad.portal.azure.com/)

- [Configurar o logon único do Citrix netdimensioner para autenticação baseada em cabeçalho](header-citrix-netscaler-tutorial.md)
