---
title: 'Tutorial: Integração do Azure Active Directory com o SAP NetWeaver | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAP NetWeaver.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6cce23194025a68eec055fe45b806c3d28434a1
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880565"
---
# <a name="tutorial-integrate-sap-netweaver-with-azure-active-directory"></a>Tutorial: Integre o SAP NetWeaver ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o SAP NetWeaver ao Azure Active Directory (Azure AD). Ao integrar o SAP NetWeaver ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao SAP NetWeaver.
* Habilite seus usuários a serem conectados automaticamente ao SAP NetWeaver com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do SAP NetWeaver.
* SAP NetWeaver V 7,20 exigido pelo menos

## <a name="scenario-description"></a>Descrição do cenário

O SAP NetWeaver dá suporte ao **SAML** (**SSO iniciado por SP**) e ao **OAuth**. Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. 

> [!NOTE]
> Configure o aplicativo no SAML ou no OAuth de acordo com seu requisito organizacional. 

## <a name="adding-sap-netweaver-from-the-gallery"></a>Adicionando o SAP NetWeaver da Galeria

Para configurar a integração do SAP NetWeaver ao Azure AD, você precisará adicionar o SAP NetWeaver da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **SAP NetWeaver** na caixa de pesquisa.
1. Selecione **SAP NetWeaver** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-netweaver"></a>Configurar e testar o logon único do Azure AD para SAP NetWeaver

Configure e teste o SSO do Azure AD com o SAP NetWeaver usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SAP NetWeaver.

Para configurar e testar o SSO do Azure AD com o SAP NetWeaver, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure ad](#configure-azure-ad-sso)** para permitir que seus usuários usem esse recurso.
    1. **[Crie um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Simon.
    1. **[Atribua o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configure o SAP NetWeaver usando SAML](#configure-sap-netweaver-using-saml)** para definir as configurações de SSO no lado do aplicativo.
    1. **[Crie um usuário de teste do SAP NetWeaver](#create-sap-netweaver-test-user)** para ter um equivalente de B. Simon no SAP NetWeaver que esteja vinculado à representação do usuário no Azure AD.
1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.
1. **[Configure o SAP NetWeaver para OAuth](#configure-sap-netweaver-for-oauth)** para definir as configurações do OAuth no lado do aplicativo.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o SAP NetWeaver, execute as seguintes etapas:

1. Abra uma nova janela do navegador da Web e entre no site da empresa do SAP NetWeaver como administrador

2. Verifique se os serviços **http** e **https** estão ativos e se as portas apropriadas estão atribuídas no código T **SMICM** .

3. Faça logon no cliente comercial do sistema SAP (T01), em que o SSO é necessário e ative o gerenciamento de sessão de segurança HTTP.

    a. Vá para o código de transação **SICF_SESSIONS**. Ele exibe todos os parâmetros de perfil relevantes com os valores atuais. Eles se parecem com o seguinte:-
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
    > Ajuste os parâmetros acima de acordo com os requisitos da sua organização, os parâmetros acima são fornecidos aqui somente como indicação.

    b. Se necessário, ajuste os parâmetros, no perfil de instância/padrão do sistema SAP e reinicie o sistema SAP.

    c. Clique duas vezes em cliente relevante para habilitar a sessão de segurança HTTP.

    ![O link de download de certificado](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Ative os serviços SICFs abaixo:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
4. Acesse o código de transação **SAML2** no cliente de negócios do sistema SAP [T01/122]. Ele abrirá uma interface do usuário em um navegador. Neste exemplo, presumimos 122 como SAP Business Client.

    ![O link de download de certificado](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

5. Forneça seu nome de usuário e senha para inserir na interface do usuário e clique em **Editar**.

    ![O link de download de certificado](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

6. Substitua **nome do provedor** de T01122 `http://T01122` para e clique em **salvar**.

    > [!NOTE]
    > Por padrão, o nome do `<sid><client>` provedor é fornecido como formato, mas o Azure ad `<protocol>://<name>`espera um nome no formato de, recomendando manter o nome do provedor como `https://<sid><client>` para permitir que vários mecanismos do SAP NetWeaver ABAP sejam configurados no Azure AD.

    ![O link de download de certificado](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

7. **Gerando metadados do provedor de serviços**:-depois que terminarmos de configurar o **provedor local** e as configurações de **provedores confiáveis** na interface do usuário do SAML 2,0, a próxima etapa será gerar o arquivo de metadados do provedor de serviços (que contém todas as configurações, contextos de autenticação e outras configurações no SAP). Depois que esse arquivo é gerado, precisamos carregá-lo no Azure AD.

    ![O link de download de certificado](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Vá para a **guia provedor local**.

    b. Clique em **metadados**.

    c. Salve o **arquivo XML de metadados** gerado no seu computador e carregue-o na seção **configuração básica do SAML** para preencher automaticamente os valores de URL de **identificação** e de **resposta** no portal do Azure.

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SAP NetWeaver** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , execute a seguinte etapa:

    a. Clique em **carregar arquivo de metadados** para carregar o **arquivo de metadados do provedor de serviços**, que você obteve anteriormente.

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    c. Depois que o arquivo de metadados for carregado com êxito, os valores do **identificador** e da **URL de resposta** serão preenchidos automaticamente na caixa de texto seção de **configuração básica do SAML** , conforme mostrado abaixo:

    d. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://<your company instance of SAP NetWeaver>`

    > [!NOTE]
    > Vimos alguns clientes que relataram um erro de URL de resposta incorreta configurada para sua instância. Se você receber qualquer erro, poderá usar o seguinte script do PowerShell como uma solução alternativa para definir a URL de resposta correta para sua instância.:
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > A ID do objeto de entidade de segurança deve ser definida por conta própria primeiro ou você pode passá-la também aqui.

1. O aplicativo SAP NetWeaver espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo atributos de usuário.

    ![image](common/edit-attribute.png)

13. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , configure o atributo de token SAML, conforme mostrado na imagem acima, e execute as seguintes etapas:

    a. Clique no **ícone Editar** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![image](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. Na lista **transformação** , selecione **ExtractMailPrefix ()** .

    c. Na lista **parâmetro 1** , selecione **User. userprinicipalname**.

    d. Clique em **Guardar**.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

   ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar o SAP NetWeaver** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao SAP NetWeaver.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **SAP NetWeaver**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-sap-netweaver-using-saml"></a>Configurar o SAP NetWeaver usando SAML

1. Entre no sistema SAP e vá para o código de transação SAML2. Ele abre uma nova janela do navegador com a tela de configuração do SAML.

2. Para configurar pontos de extremidade para o provedor de identidade confiável (Azure AD), acesse a guia **provedores confiáveis** .

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. Pressione **Adicionar** e selecione **carregar arquivo de metadados** no menu de contexto.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Carregue o arquivo de metadados, que você baixou do portal do Azure.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. Na próxima tela, digite o nome do alias. Por exemplo, aadsts e pressione **Avançar** para continuar.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. Certifique-se de que o **algoritmo Digest** deve ser **SHA-256** e não exija nenhuma alteração e pressione **Avançar**.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. Em **pontos de extremidade de logon único**, use **http post** e clique em **Avançar** para continuar.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. Em **pontos de extremidade de logoff único** , selecione **HTTPRedirect** e clique em **Avançar** para continuar.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. Em **pontos de extremidade de artefato**, pressione **Avançar** para continuar.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. Em **requisitos de autenticação**, clique em **concluir**.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Vá para guia**Federação de identidade** do **provedor** > confiável (na parte inferior da tela). Clique em **Editar**.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. Clique em **Adicionar** na guia **Federação de identidade** (janela inferior).

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. Na janela pop-up, selecione **não especificado** nos **formatos NameID com suporte** e clique em OK.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. Observe que os valores do **modo de mapeamento de ID** de usuário e de origem da **ID** de usuários determinam o vínculo entre o usuário SAP e a declaração do Azure AD.  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Cenário: Usuário SAP para mapeamento de usuário do Azure AD.

    a. NameID detalha a captura de tela do SAP.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Captura de tela mencionando as declarações necessárias do Azure AD.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Cenário: Selecione a ID de usuário do SAP com base no endereço de email configurado em SU01. Nesse caso, a ID de email deve ser configurada em su01 para cada usuário que requer SSO.

    a.  NameID detalha a captura de tela do SAP.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. captura de tela mencionando as declarações necessárias do Azure AD.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/claimsaad2.png)

15. Clique em **salvar** e em **habilitar** para habilitar o provedor de identidade.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/configuration1.png)

16. Clique em **OK** quando solicitado.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/configuration2.png)

    ### <a name="create-sap-netweaver-test-user"></a>Criar usuário de teste do SAP NetWeaver

    Nesta seção, você criará um usuário chamado B. Simon no SAP NetWeaver. Trabalhe com sua equipe de especialistas do SAP na casa ou trabalhe com o parceiro SAP da sua organização para adicionar os usuários na plataforma SAP NetWeaver.

## <a name="test-sso"></a>Testar SSO

1. Depois que o provedor de identidade do Azure AD foi ativado, tente acessar a URL abaixo para verificar o SSO (não haverá solicitação para o nome de usuário & senha)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (ou) Use a URL abaixo

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Substitua sapurl pelo nome de host SAP real.

2. A URL acima deve levar você para a tela abaixo mencionada. Se você conseguir acessar a página abaixo, a instalação do SSO do Azure AD será feita com êxito.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/testingsso.png)

3. Se o prompt de nome de usuário & senha ocorrer, diagnostique o problema habilitando o rastreamento usando a URL abaixo

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="configure-sap-netweaver-for-oauth"></a>Configurar o SAP NetWeaver para OAuth

1. O processo SAP documentado está disponível no local: [Habilitação do serviço de gateway NetWeaver e criação de escopo do OAuth 2,0](https://wiki.scn.sap.com/wiki/display/Security/NetWeaver+Gateway+Service+Enabling+and+OAuth+2.0+Scope+Creation)

2. Acesse SPRO e localize **ativar e manter serviços**.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/oauth01.png)

3. Neste exemplo, queremos conectar o serviço OData: `DAAG_MNGGRP` com OAuth ao SSO do Azure AD. Use o nome do serviço técnico Pesquisar o serviço `DAAG_MNGGRP` e ative-o se ainda não estiver ativo, já `green` (procure o status na guia nós do ICF). Verifique se o alias do sistema (o sistema back-end conectado, onde o serviço realmente em execução) está correto.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/oauth02.png)

    * Em seguida, clique em botão de pressão **OAuth** na barra `scope` de botões superior e atribua (Mantenha o nome padrão como oferecido).

4. Para nosso exemplo, o escopo `DAAG_MNGGRP_001`é gerado a partir do nome do serviço adicionando automaticamente um número. O `/IWFND/R_OAUTH_SCOPES` relatório pode ser usado para alterar o nome do escopo ou criar manualmente.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/oauth03.png)

    > [!NOTE]
    > Mensagem `soft state status is not supported` – pode ser ignorada, sem problemas. Para obter mais detalhes, consulte [aqui](https://help.sap.com/doc/saphelp_nw74/7.4.16/1e/c60c33be784846aad62716b4a1df39/content.htm?no_cache=true)

### <a name="create-a-service-user-for-the-oauth-20-client"></a>Criar um usuário de serviço para o cliente OAuth 2,0

1. OAuth2 usa um `service ID` para obter o token de acesso para o usuário final em seu nome. Restrição importante por design do OAuth: `OAuth 2.0 Client ID` o deve ser idêntico com `username` o cliente OAuth 2,0 usado para logon ao solicitar um token de acesso. Portanto, para nosso exemplo, vamos registrar um cliente OAuth 2,0 com o nome CLIENT1 e, como pré-requisito, um usuário com o mesmo nome (CLIENT1) deve existir no sistema SAP e esse usuário será configurado para ser usado pelo aplicativo referenciado. 

2. Ao registrar um cliente OAuth, usamos o `SAML Bearer Grant type`.

    >[!NOTE]
    >Para obter mais detalhes, consulte o registro de cliente OAuth 2,0 para o tipo de concessão de portador SAML [aqui](https://wiki.scn.sap.com/wiki/display/Security/OAuth+2.0+Client+Registration+for+the+SAML+Bearer+Grant+Type)

3. tcod: SU01/Create User CLIENT1 `System type` as e atribuir senha, salve-o como necessário para fornecer a credencial ao programador de API, que deve gravá-lo com o nome de usuário para o código de chamada. Nenhum perfil ou função deve ser atribuído.

### <a name="register-the-new-oauth-20-client-id-with-the-creation-wizard"></a>Registrar a nova ID de cliente OAuth 2,0 com o assistente de criação

1. Para registrar um novo **cliente OAuth 2,0** , inicie a transação **SOAUTH2**. A transação exibirá uma visão geral sobre os clientes OAuth 2,0 que já foram registrados. Escolha **criar** para iniciar o assistente para o novo cliente OAuth chamado como CLIENT1 neste exemplo.

2. Vá para o código T: **SOAUTH2** e forneça a descrição e clique em **Avançar**.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/oauth04.png)

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/oauth05.png)

3. Selecione o IdP do **SAML2 já adicionado – Azure ad** na lista suspensa e salve.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/oauth06.png)

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/oauth07.png)

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/oauth08.png)

4. Clique em **Adicionar** em atribuição de escopo para adicionar o escopo criado anteriormente:`DAAG_MNGGRP_001`

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/oauth09.png)

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/oauth10.png)

5. Clique em **concluir**.

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)