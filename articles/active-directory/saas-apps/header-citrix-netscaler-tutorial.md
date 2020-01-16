---
title: 'Tutorial: integração de logon único do Azure Active Directory com o Citrix Netscaler (autenticação baseada em cabeçalho) | Microsoft Docs'
description: Saiba como configurar o SSO (logon único) entre o Azure Active Directory e o Citrix netdimensioner usando a autenticação baseada em cabeçalho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b0adc7bf-696d-44c9-a57a-f9e9471b8710
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87f1c884edf6841b65495cd31ed4c7d6e63aedd9
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977922"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-netscaler-header-based-authentication"></a>Tutorial: integração de logon único do Azure Active Directory com o Citrix Netscaler (autenticação baseada em cabeçalho)

Neste tutorial, você aprenderá a integrar o Citrix Netscaler ao Azure Active Directory (Azure AD). Ao integrar o Citrix Netscaler ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Citrix Netscaler.
* Habilite seus usuários a entrar automaticamente no Citrix Netscaler com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS (software como serviço) com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Citrix netdimension.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O tutorial inclui estes cenários:

* **Iniciado por SP** SSO para Citrix Netscaler

* Provisionamento de usuário **just in time** para Citrix Netscaler

* [Autenticação baseada em cabeçalho para Citrix Netscaler](#publish-the-web-server)

* [Autenticação baseada em Kerberos para Citrix Netscaler](citrix-netscaler-tutorial.md#publish-the-web-server)

## <a name="add-citrix-netscaler-from-the-gallery"></a>Adicionar o Citrix Netscaler da Galeria

Para integrar o Citrix Netscaler ao Azure AD, primeiro adicione o Citrix Netscaler à sua lista de aplicativos SaaS gerenciados da Galeria:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.

1. No menu à esquerda, selecione **Azure Active Directory**.

1. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.

1. Na seção **Adicionar da Galeria** , insira **Citrix Netscaler** na caixa de pesquisa.

1. Nos resultados, selecione **Citrix Netscaler**e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Configurar e testar o logon único do Azure AD para o Citrix Netscaler

Configure e teste o SSO do Azure AD com o Citrix netdimensioner usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Citrix Netscaler.

Para configurar e testar o SSO do Azure AD com o Citrix Netscaler, conclua os seguintes blocos de construção:

1. [Configurar o SSO do Azure ad](#configure-azure-ad-sso) – para permitir que os usuários usem esse recurso.

    1. [Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user) – para testar o SSO do Azure AD com B. Simon.

    1. [Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user) – para habilitar B. Simon para usar o SSO do Azure AD.

1. [Configurar o Citrix Netscaler SSO](#configure-citrix-netscaler-sso) – para definir as configurações de SSO no lado do aplicativo.

    * [Criar um usuário de teste do Citrix Netscaler](#create-a-citrix-netscaler-test-user) – para ter um equivalente de B. Simon no Citrix Netscaler que esteja vinculado à representação do usuário no Azure AD.

1. [Testar SSO](#test-sso) – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Para habilitar o SSO do Azure AD usando o portal do Azure, conclua estas etapas:

1. No [portal do Azure](https://portal.azure.com/), no painel de integração de aplicativos do **Citrix Netscaler** , em **gerenciar**, selecione **logon único**.

1. No painel **selecionar um método de logon único** , selecione **SAML**.

1. No painel **Configurar logon único com SAML** , selecione o ícone de **edição** de caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , para configurar o aplicativo no modo **iniciado pelo IDP** :

    1. Na caixa de texto **identificador** , insira uma URL que tenha o seguinte padrão: `https://<Your FQDN>`

    1. Na caixa de texto **URL de resposta** , insira uma URL que tenha o seguinte padrão: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

1. Para configurar o aplicativo no modo **iniciado pelo SP** , selecione **definir URLs adicionais** e conclua a seguinte etapa:

    * Na caixa de texto **URL de logon** , insira uma URL que tenha o seguinte padrão: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * As URLs que são usadas nesta seção não são valores reais. Atualize esses valores com os valores reais para identificador, URL de resposta e URL de logon. Contate a [equipe de suporte ao cliente do Citrix Netscaler](https://www.citrix.com/contact/technical-support.html) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.
    > * Para configurar o SSO, as URLs devem ser acessíveis de sites públicos. Você deve habilitar o firewall ou outras configurações de segurança no lado do Citrix Netscaler para enble o Azure AD para postar o token na URL configurada.

1. No painel **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , para URL de metadados de **Federação de aplicativo**, copie a URL e salve-a no bloco de notas.

    ![O link de download de certificado](common/certificatebase64.png)

1. O aplicativo Citrix Netscaler espera que as asserções SAML estejam em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Selecione o ícone **Editar** e altere os mapeamentos de atributo.

    ![Editar o mapeamento de atributo SAML](common/edit-attribute.png)

1. O aplicativo Citrix Netscaler também espera que mais alguns atributos sejam passados de volta na resposta SAML. Na caixa de diálogo **atributos de usuário** , em **declarações de usuário**, conclua as seguintes etapas para adicionar os atributos de token SAML, conforme mostrado na tabela:

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | mysecretoid  | user.userprincipalname |
    
    1. Selecione **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    1. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    1. Deixe o **namespace** em branco.

    1. Para **atributo**, selecione **origem**.

    1. Na lista **atributo de origem** , insira o valor do atributo que é mostrado para essa linha.

    1. Selecione **OK**.

    1. Selecione **Guardar**.

1. Na seção **Configurar o Citrix Netescalar** , copie as URLs relevantes com base em seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No menu à esquerda na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.

1. Selecione **novo usuário** na parte superior do painel.

1. Em Propriedades do **usuário** , conclua estas etapas:

   1. Para **nome**, insira `B.Simon`.  

   1. Para **nome de usuário**, digite _username@companydomain.extension_ . Por exemplo, `B.Simon@contoso.com`.

   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote ou copie o valor que é exibido em **senha**.

   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará o usuário B. Simon para usar o SSO do Azure, concedendo acesso ao usuário ao Citrix Netscaler.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

1. Na lista de aplicativos, selecione **Citrix Netscaler**.

1. Na visão geral do aplicativo, em **gerenciar**, selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Em seguida, na caixa de diálogo **Adicionar atribuição** , selecione **usuários e grupos**.

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista de **usuários** . Escolha **Selecionar**.

1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função relevante para o usuário na lista e escolha **selecionar**.

1. Na caixa de diálogo **Adicionar atribuição** , selecione **atribuir**.

## <a name="configure-citrix-netscaler-sso"></a>Configurar o SSO do Citrix netscaleer

Selecione um link para as etapas para o tipo de autenticação que você deseja configurar:

- [Configurar o SSO do Citrix netscaleer para autenticação baseada em cabeçalho](#publish-the-web-server)

- [Configurar o SSO do Citrix Netscaler para autenticação baseada em Kerberos](citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>Publicar o servidor Web 

Para criar um servidor virtual:

1. Selecione **Gerenciamento de tráfego** > **balanceamento de carga** > **Serviços**.
    
1. Selecione **Adicionar**.

    ![Configuração do Citrix Netscaler – painel serviços](./media/header-citrix-netscaler-tutorial/web01.png)

1. Defina os seguintes valores para o servidor Web que está executando os aplicativos:

   * **Nome do serviço**
   * **IP do servidor/servidor existente**
   * **Protocolo**
   * **Porta**

     ![Painel de configuração do Citrix Netscaler](./media/header-citrix-netscaler-tutorial/web01.png)

### <a name="configure-the-load-balancer"></a>Configurar o balanceador de carga

Para configurar o balanceador de carga:

1. Vá para **Gerenciamento de tráfego** > **balanceamento de carga** > **servidores virtuais**.

1. Selecione **Adicionar**.

1. Defina os valores a seguir, conforme descrito na seguinte captura de tela:

    * **Nome**
    * **Protocolo**
    * **Endereço IP**
    * **Porta**

1. Selecione **OK**.

    ![Configuração do Citrix Netscaler – painel configurações básicas](./media/header-citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>Associar o servidor virtual

Para associar o balanceador de carga ao servidor virtual:

1. No painel **serviços e grupos** de serviços, selecione **nenhuma associação de serviço do servidor virtual de balanceamento de carga**.

   ![Configuração do Citrix Netscaler-painel de associação de serviço do servidor virtual de balanceamento de carga](./media/header-citrix-netscaler-tutorial/bind01.png)

1. Verifique as configurações conforme mostrado na captura de tela a seguir e selecione **fechar**.

   ![Configuração do Citrix netdimensioner – verificar a associação de serviços do Virtual Server](./media/header-citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Associar o certificado

Para publicar esse serviço como SSL, associe o certificado do servidor e teste seu aplicativo:

1. Em **certificado**, selecione **nenhum certificado do servidor**.

   ![Configuração do Citrix Netscaler – painel certificado do servidor](./media/header-citrix-netscaler-tutorial/bind03.png)

1. Verifique as configurações conforme mostrado na captura de tela a seguir e selecione **fechar**.

   ![Configuração do Citrix netdimensioner – verificar o certificado](./media/header-citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Perfil SAML do Citrix ADC

Para configurar o perfil SAML do Citrix ADC, conclua as seguintes seções:

### <a name="create-an-authentication-policy"></a>Criar uma política de autenticação

Para criar uma política de autenticação:

1. Vá para **segurança** > **AAA – tráfego de aplicativo** > **políticas** > **autenticação** > **políticas de autenticação**.

1. Selecione **Adicionar**.

1. No painel **criar política de autenticação** , insira ou selecione os seguintes valores:

    * **Nome**: Insira um nome para a política de autenticação.
    * **Ação**: insira **SAML**e, em seguida, selecione **Adicionar**.
    * **Expressão**: insira **true**.     
    
    ![Configuração do Citrix Netscaler – criar política de autenticação](./media/header-citrix-netscaler-tutorial/policy01.png)

1. Selecione **Criar**.

### <a name="create-an-authentication-saml-server"></a>Criar um servidor SAML de autenticação

Para criar um servidor SAML de autenticação, vá para o painel **criar autenticação servidor SAML** e, em seguida, conclua as seguintes etapas:

1. Para **nome**, insira um nome para o servidor SAML de autenticação.

1. Em **Exportar metadados SAML**:

   1. Marque a caixa de seleção **importar metadados** .

   1. Insira a URL de metadados de Federação da interface do usuário SAML do Azure que você copiou anteriormente.
    
1. Para **nome do emissor**, insira a URL relevante.

1. Selecione **Criar**.

![Configuração do Citrix netdimensioner – criar autenticação painel do servidor SAML](./media/header-citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Criar um servidor virtual de autenticação

Para criar um servidor virtual de autenticação:

1.  Vá para **segurança** > **AAA-tráfego de aplicativo** > **políticas** > **autenticação** > **servidores virtuais de autenticação**.

1.  Selecione **Adicionar**e, em seguida, conclua as seguintes etapas:

    1. Para **nome**, insira um nome para o servidor virtual de autenticação.

    1. Marque a caixa de seleção **não endereçável** .

    1. Para **protocolo**, selecione **SSL**.

    1. Selecione **OK**.

    ![Configuração do Citrix Netscaler – painel do servidor virtual de autenticação](./media/header-citrix-netscaler-tutorial/server02.png)
    
### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Configurar o servidor virtual de autenticação para usar o Azure AD

Modifique duas seções para o servidor virtual de autenticação:

1.  No painel **políticas de autenticação avançadas** , selecione **nenhuma política de autenticação**.

    ![Configuração do Citrix Netscaler-painel políticas de autenticação avançadas](./media/header-citrix-netscaler-tutorial/virtual01.png)

1. No painel **Associação de política** , selecione a política de autenticação e, em seguida, selecione **associar**.

    ![Configuração do Citrix Netscaler – painel de associação de política](./media/header-citrix-netscaler-tutorial/virtual02.png)

1. No painel **servidores virtuais baseados em formulário** , selecione **nenhum servidor virtual de balanceamento de carga**.

    ![Configuração do Citrix Netscaler – painel de servidores virtuais baseados em formulário](./media/header-citrix-netscaler-tutorial/virtual03.png)

1. Para **FQDN de autenticação**, insira um FQDN (nome de domínio totalmente qualificado) (obrigatório).

1. Selecione o servidor virtual de balanceamento de carga que você deseja proteger com a autenticação do Azure AD.

1. Selecione **associar**.

    ![Configuração do Citrix Netscaler-painel de associação do servidor virtual de balanceamento de carga](./media/header-citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Certifique-se de selecionar **concluído** no painel de **configuração do servidor virtual de autenticação** .

1. Para verificar as alterações, em um navegador, vá para a URL do aplicativo. Você deve ver sua página de entrada de locatário em vez do acesso não autenticado que você viu anteriormente.

    ![Configuração do Citrix netdimensioner – uma página de entrada em um navegador da Web](./media/header-citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-sso-for-header-based-authentication"></a>Configurar o SSO do Citrix netscaleer para autenticação baseada em cabeçalho

### <a name="configure-citrix-adc"></a>Configurar o Citrix ADC

Para configurar o Citrix ADC para autenticação baseada em cabeçalho, conclua as seções a seguir.

#### <a name="create-a-rewrite-action"></a>Criar uma ação de regravação

1. Vá para **AppExpert** > **reescrever** > **ações de regravação**.
 
    ![Configuração do Citrix Netscaler-painel Ações de regravação](./media/header-citrix-netscaler-tutorial/header01.png)

1.  Selecione **Adicionar**e, em seguida, conclua as seguintes etapas:

    1. Para **nome**, insira um nome para a ação de regravação.

    1. Para **tipo**, digite **INSERT_HTTP_HEADER**.

    1. Para **nome do cabeçalho**, insira um nome de cabeçalho (neste exemplo, usamos a _secretaid_).

    1. Para **expressão**, insira **AAA. Usuário. ATRIBUTO ("mysecretoid")** , em que **mysecretoid** é a declaração SAML do Azure ad enviada ao Citrix ADC.

    1. Selecione **Criar**.

    ![Configuração do Citrix netdimensioner – criar painel de ação de regravação](./media/header-citrix-netscaler-tutorial/header02.png)
 
#### <a name="create-a-rewrite-policy"></a>Criar uma política de regravação

1.  Acesse **AppExpert** > **reescrever** > **políticas de regravação**.
 
    ![Configuração do Citrix Netscaler – painel de políticas de regravação](./media/header-citrix-netscaler-tutorial/header03.png)

1.  Selecione **Adicionar**e, em seguida, conclua as seguintes etapas:

    1. Para **nome**, insira um nome para a política de regravação.

    1. Para **ação**, selecione a ação de regravação criada na seção anterior.

    1. Para **expressão**, digite **true**.

    1. Selecione **Criar**.

    ![Configuração do Citrix netdimensioner – criar painel de política de reescrita](./media/header-citrix-netscaler-tutorial/header04.png)

### <a name="bind-a-rewrite-policy-to-a-virtual-server"></a>Associar uma política de regravação a um servidor virtual

Para associar uma política de regravação a um servidor virtual usando a GUI:

1. Vá para **Gerenciamento de tráfego** > **balanceamento de carga** > **servidores virtuais**.

1. Na lista de servidores virtuais, selecione o servidor virtual ao qual você deseja associar a política de regravação e, em seguida, selecione **abrir**.

1. No painel **servidor virtual de balanceamento de carga** , em **Configurações avançadas**, selecione **políticas**. Todas as políticas que são configuradas para sua instância de netescalar aparecem na lista.
 
    ![Configuração do Citrix Netscaler-painel do servidor virtual de balanceamento de carga](./media/header-citrix-netscaler-tutorial/header05.png)

    ![Configuração do Citrix Netscaler-painel do servidor virtual de balanceamento de carga](./media/header-citrix-netscaler-tutorial/header06.png)

1.  Marque a caixa de seleção ao lado do nome da política que você deseja associar a este servidor virtual.
 
    ![Configuração de balanceamento de carga da política de tráfego do servidor virtual do Citrix Netscaler](./media/header-citrix-netscaler-tutorial/header08.png)

1. Na caixa de diálogo **escolher tipo** :

    1. Para **escolher política**, selecione **tráfego**.

    1. Para **escolher tipo**, selecione **solicitação**.

    ![Caixa de diálogo configuração do Citrix Netscaler – políticas](./media/header-citrix-netscaler-tutorial/header07.png)

1.  Selecione **OK**. Uma mensagem na barra de status indica que a política foi configurada com êxito.

### <a name="modify-the-saml-server-to-extract-attributes-from-a-claim"></a>Modificar o servidor SAML para extrair atributos de uma declaração

1.  Vá para **segurança** > **AAA-tráfego de aplicativo** > **políticas** > **autenticação** > **políticas avançadas** > **ações** > **servidores**.

1.  Selecione o servidor SAML de autenticação apropriado para o aplicativo.
 
    ![Configuração do Citrix netdimensioner-configurar autenticação painel do servidor SAML](./media/header-citrix-netscaler-tutorial/header09.png)

1. Na dificuldade dos **atributos** , insira os atributos SAML que você deseja extrair, separados por vírgulas. Em nosso exemplo, inserimos o atributo `mySecretID`.
 
    ![Configuração do Citrix Netscaler – painel atributos](./media/header-citrix-netscaler-tutorial/header10.png)

1. Para verificar o acesso, na URL em um navegador, procure o atributo SAML na **coleção Headers**.

    ![Configuração do Citrix Netscaler-coleção de cabeçalhos na URL](./media/header-citrix-netscaler-tutorial/header11.png)

### <a name="create-a-citrix-netscaler-test-user"></a>Criar um usuário de teste do Citrix Netscaler

Nesta seção, um usuário chamado B. Simon é criado no Citrix Netscaler. O Citrix Netscaler dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhuma ação a ser adotada nesta seção. Se um usuário ainda não existir no Citrix Netscaler, um novo será criado após a autenticação.

> [!NOTE]
> Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte ao cliente Citrix Netscaler](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Testar SSO 

Nesta seção, você testará sua configuração de SSO do Azure AD usando o painel de acesso.

Ao selecionar o bloco Citrix Netscaler no painel de acesso, você deve entrar automaticamente no Citrix Netscaler para o qual você configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Citrix Netscaler com o Azure AD](https://aad.portal.azure.com/)

- [Configurar o logon único do Citrix netdimensioner para autenticação baseada em Kerberos](citrix-netscaler-tutorial.md)
