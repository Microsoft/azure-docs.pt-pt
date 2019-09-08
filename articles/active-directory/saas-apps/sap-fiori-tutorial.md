---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com o SAP Fiori | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAP Fiori.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 77ad13bf-e56b-4063-97d0-c82a19da9d56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50d1875ce2529222e8ff7472c48bf6d4dd878667
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772876"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-fiori"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com o SAP Fiori

Neste tutorial, você aprenderá a integrar o SAP Fiori com o Azure Active Directory (Azure AD). Ao integrar o SAP Fiori ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao SAP Fiori.
* Habilite seus usuários a serem conectados automaticamente ao SAP Fiori com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do SAP Fiori.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SAP Fiori dá suporte ao SSO iniciado por **SP**

> [!NOTE]
> Para a autenticação de iFrame iniciada pelo SAP Fiori, é recomendável usar o parâmetro **Ispassal** no AuthnRequest SAML para autenticação silenciosa. Para obter mais detalhes sobre o parâmetro **Ispassal** , consulte informações de [logon único do SAML do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol)

## <a name="adding-sap-fiori-from-the-gallery"></a>Adicionando o SAP Fiori da Galeria

Para configurar a integração do SAP Fiori ao Azure AD, você precisará adicionar o SAP Fiori da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **SAP Fiori** na caixa de pesquisa.
1. Selecione **SAP Fiori** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-fiori"></a>Configurar e testar o logon único do Azure AD para SAP Fiori

Configure e teste o SSO do Azure AD com o SAP Fiori usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SAP Fiori.

Para configurar e testar o SSO do Azure AD com o SAP Fiori, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do SAP Fiori](#configure-sap-fiori-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do SAP Fiori](#create-sap-fiori-test-user)** – para ter um equivalente de B. Simon no SAP Fiori que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Abra uma nova janela do navegador da Web e entre no site da empresa do SAP Fiori como um administrador.

1. Verifique se os serviços **http** e **https** estão ativos e se as portas relevantes estão atribuídas ao código de transação **SMICM**.

1. Entre no SAP Business Client para SAP System **T01**, onde o logon único é necessário. Em seguida, ative o gerenciamento de sessão de segurança HTTP.

    1. Vá para o código de transação **SICF_SESSIONS**. Todos os parâmetros de perfil relevantes com valores atuais são mostrados. Eles são semelhantes ao exemplo a seguir:

        ```
        login/create_sso2_ticket = 2
        login/accept_sso2_ticket = 1
        login/ticketcache_entries_max = 1000
        login/ticketcache_off = 0  login/ticket_only_by_https = 0 
        icf/set_HTTPonly_flag_on_cookies = 3
        icf/user_recheck = 0  http/security_session_timeout = 1800
        http/security_context_cache_size = 2500
        rdisp/plugin_auto_logout = 1800
        rdisp/autothtime = 60
        ```

        >[!NOTE]
        > Ajuste os parâmetros com base nos requisitos da sua organização. Os parâmetros anteriores são fornecidos apenas como um exemplo.

    1. Se necessário, ajuste os parâmetros no perfil da instância (padrão) do sistema SAP e reinicie o sistema SAP.

    1. Clique duas vezes no cliente relevante para habilitar uma sessão de segurança HTTP.

        ![Os valores atuais da página de parâmetros de perfil relevante no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. Ative os seguintes serviços SICFs:

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Vá para código de transação **SAML2** no Business Client para sistema SAP [**T01/122**]. A interface do usuário de configuração é aberta em uma nova janela do navegador. Neste exemplo, usamos o Business Client para o sistema SAP 122.

    ![Página de entrada do SAP Fiori Business Client](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Insira seu nome de usuário e senha e, em seguida, selecione **fazer logon**.

    ![A configuração SAML 2,0 da página T01/122 do sistema ABAP no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. Na caixa **nome do provedor** , substitua **T01122** por **http:\//T01122**e, em seguida, selecione **salvar**.

    > [!NOTE]
    > Por padrão, o nome do provedor está no formato \<Sid >\<cliente >. O Azure ad espera o nome no protocolo \<de formato >:/\</nome >. Recomendamos que você mantenha o nome do provedor como\:um SID\<de HTTPS//\<> cliente > para poder configurar vários mecanismos do SAP Fiori ABAP no Azure AD.

    ![O nome do provedor atualizado na configuração SAML 2,0 da página ABAP System T01/122 no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Selecione**metadados**da >  **guia provedor local**.

1. Na caixa de diálogo **metadados SAML 2,0** , baixe o arquivo XML de metadados gerado e salve-o em seu computador.

    ![O link baixar metadados na caixa de diálogo metadados do SAP SAML 2,0](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SAP Fiori** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:

    a. Clique em **carregamento de ficheiro de metadados**.

    ![Carregar ficheiro de metadados](common/upload-metadata.png)

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    ![escolher arquivo de metadados](common/browse-upload-metadata.png)

    c. Quando o arquivo de metadados é carregado com êxito, os valores do **identificador** e da **URL de resposta** são preenchidos automaticamente no painel de **configuração básica do SAML** . Na caixa **URL de logon** , insira uma URL que tenha o seguinte padrão: `https:\//\<your company instance of SAP Fiori\>`.

    > [!NOTE]
    > Alguns clientes relatam erros relacionados a valores de **URL de resposta** configurados incorretamente. Se você vir esse erro, poderá usar o seguinte script do PowerShell para definir a URL de resposta correta para sua instância:
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > Você pode definir a `ServicePrincipal` ID do objeto por conta própria antes de executar o script ou pode passá-la aqui.

1. O aplicativo SAP Fiori espera que as asserções SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Para gerenciar esses valores de atributo, no painel **Configurar logon único com SAML** , selecione **Editar**.

    ![O painel atributos de usuário](common/edit-attribute.png)

1. No painel **atributos de usuário & declarações** , configure os atributos de token SAML, conforme mostrado na imagem anterior. Em seguida, conclua as seguintes etapas:

    1. Selecione **Editar** para abrir o painel **gerenciar declarações do usuário** .

    1. Na lista **transformação** , selecione **ExtractMailPrefix ()** .

    1. Na lista **parâmetro 1** , selecione **User. userprinicipalname**.

    1. Selecione **Guardar**.

       ![O painel gerenciar declarações do usuário](./media/sapfiori-tutorial/nameidattribute.png)

       ![A seção transformação no painel gerenciar declarações do usuário](./media/sapfiori-tutorial/nameidattribute1.png)
    
1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar o SAP Fiori** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao SAP Fiori.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **SAP Fiori**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-sap-fiori-sso"></a>Configurar o SSO do SAP Fiori

1. Entre no sistema SAP e vá para o código de transação **SAML2**. Uma nova janela do navegador é aberta com a página de configuração do SAML.

1. Para configurar pontos de extremidade para um provedor de identidade confiável (AD do Azure), selecione a guia **provedores confiáveis** .

    ![A guia provedores confiáveis no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. Selecione **Adicionar**e, em seguida, selecione **carregar arquivo de metadados** no menu de contexto.

    ![As opções Adicionar e carregar arquivo de metadados no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Carregue o arquivo de metadados que você baixou no portal do Azure. Selecione **Seguinte**.

    ![Selecione o arquivo de metadados a ser carregado no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. Na página seguinte, na caixa **alias** , insira o nome do alias. Por exemplo, **aadsts**. Selecione **Seguinte**.

    ![A caixa alias no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Verifique se o valor na caixa **algoritmo de resumo** é **SHA-256**. Selecione **Seguinte**.

    ![Verificar o valor do algoritmo Digest no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. Em **pontos de extremidade de logon único**, selecione **http post**e, em seguida, selecione **Avançar**.

    ![Opções de pontos de extremidade de logon único no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. Em **pontos de extremidade de logoff único**, selecione **redirecionamento http**e, em seguida, selecione **Avançar**.

    ![Opções de pontos de extremidade de logout único no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. Em **pontos de extremidade de artefato**, selecione **Avançar** para continuar.

    ![Opções de pontos de extremidade de artefato no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. Em **requisitos de autenticação**, selecione **concluir**.

    ![Opções de requisitos de autenticação e a opção concluir no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. Selecione**Federação de identidade** de **provedor** > confiável (na parte inferior da página). Selecione **Editar**.

    ![As guias provedor confiável e Federação de identidade no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. Selecione **Adicionar**.

    ![A opção Adicionar na guia Federação de identidades](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. Na caixa de diálogo **formatos NameID com suporte** , selecione **não especificado**. Selecione **OK**.

    ![A caixa de diálogo formatos NameID com suporte e opções no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    Os valores de **origem da ID de usuário** e **modo de mapeamento de ID de usuário** determinam o vínculo entre o usuário SAP e a declaração do Azure AD.  

    **Cenário 1**: Usuário SAP para mapeamento de usuário do Azure AD

    1. No SAP, em **detalhes do formato NameID "não especificado"** , observe os detalhes:

        ![Os detalhes da caixa de diálogo "não especificado" do formato NameID no SAP](./media/sapfiori-tutorial/nameiddetails.png)

    1. No portal do Azure, em **atributos de usuário & declarações**, observe as declarações necessárias do Azure AD.

        ![A caixa de diálogo atributos de usuário & declarações no portal do Azure](./media/sapfiori-tutorial/claimsaad1.png)

    **Cenário 2**: Selecione a ID de usuário do SAP com base no endereço de email configurado em SU01. Nesse caso, a ID de email deve ser configurada em SU01 para cada usuário que requer SSO.

    1.  No SAP, em **detalhes do formato NameID "não especificado"** , observe os detalhes:

        ![Os detalhes da caixa de diálogo "não especificado" do formato NameID no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. No portal do Azure, em **atributos de usuário & declarações**, observe as declarações necessárias do Azure AD.

       ![A caixa de diálogo atributos de usuário & declarações no portal do Azure](./media/sapfiori-tutorial/claimsaad2.png)

1. Selecione **salvar**e, em seguida, selecione **habilitar** para habilitar o provedor de identidade.

    ![As opções salvar e habilitar no SAP](./media/sapfiori-tutorial/configuration1.png)

1. Selecione **OK** quando solicitado.

    ![A opção OK na caixa de diálogo configuração do SAML 2,0 no SAP](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-sap-fiori-test-user"></a>Criar usuário de teste do SAP Fiori

Nesta seção, você criará um usuário chamado Brenda Simon no SAP Fiori. Trabalhe com sua equipe SAP interna de especialistas ou com o parceiro SAP da sua organização para adicionar o usuário à plataforma SAP Fiori.

## <a name="test-sso"></a>Testar SSO

1. Depois que o provedor de identidade do Azure AD for ativado no SAP Fiori, tente acessar uma das URLs a seguir para testar o logon único (você não deverá ser solicitado a fornecer um nome de usuário e uma senha):

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > Substitua *sapurl* pelo nome do host SAP real.

1. A URL de teste deve levar você para a seguinte página de teste do aplicativo no SAP. Se a página for aberta, o logon único do Azure AD será configurado com êxito.

    ![A página de aplicativo de teste padrão no SAP](./media/sapfiori-tutorial/testingsso.png)

1. Se você for solicitado a fornecer um nome de usuário e uma senha, habilite o rastreamento para ajudar a diagnosticar o problema. Use a seguinte URL para o rastreamento: https:\//\<sapurl\>/SAP/BC/Webdynpro/SAP/sec_diag_tool? SAP-Client = 122 & SAP-language = en #.

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o SAP Fiori com o Azure AD](https://aad.portal.azure.com/)
