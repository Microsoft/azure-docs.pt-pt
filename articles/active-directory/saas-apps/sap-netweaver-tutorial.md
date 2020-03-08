---
title: 'Tutorial: Tutorial: Azure Ative Directory Single sign-on (SSO) integração com SAP NetWeaver  Integração de Diretório Ativo Azure) com sap NetWeaver  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SAP NetWeaver.
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
ms.date: 08/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 700f2ca4d46b3483531fa0784cb78699befb20ca
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897742"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-netweaver"></a>Tutorial: Azure Ative Directory Single sign-on (SSO) integração com SAP NetWeaver

Neste tutorial, você vai aprender a integrar sAP NetWeaver com O Diretório Ativo Azure (Azure AD). Quando integrar o SAP NetWeaver com a Azure AD, pode:

* Controlo em Azure AD que tem acesso ao SAP NetWeaver.
* Ative que os seus utilizadores sejam automaticamente inscritos no SAP NetWeaver com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* SAP NetWeaver única subscrição ativada (SSO).
* SAP NetWeaver V7.20 exigido pelo menos

## <a name="scenario-description"></a>Descrição do cenário

A SAP NetWeaver suporta tanto a **SAML** **(SP iniciado SSO)** como **a OAuth**. Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. 

> [!NOTE]
> Configure a aplicação quer na SAML quer na OAuth de acordo com o seu requisito organizacional. 

## <a name="adding-sap-netweaver-from-the-gallery"></a>Adicionando SAP NetWeaver da galeria

Para configurar a integração do SAP NetWeaver em Azure AD, precisa adicionar sAP NetWeaver da galeria à sua lista de aplicações saaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **SAP NetWeaver** na caixa de pesquisa.
1. Selecione **SAP NetWeaver** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-netweaver"></a>Configure e teste Azure AD single sign-on para SAP NetWeaver

Configure e teste Azure AD SSO com SAP NetWeaver utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no SAP NetWeaver.

Para configurar e testar o Azure AD SSO com o SAP NetWeaver, complete os seguintes blocos de construção:

1. **[Configure O SSO AD Azure](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de Azure AD com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o SAP NetWeaver utilizando o SAML](#configure-sap-netweaver-using-saml)** para configurar as definições sso no lado da aplicação.
    1. Crie um utilizador de **[teste SAP NetWeaver](#create-sap-netweaver-test-user)** para ter uma contraparte de B.Simon no SAP NetWeaver que está ligada à representação do utilizador da AD Azure.
1. **[Teste sSO](#test-sso)** para verificar se a configuração funciona.
1. **[Configure o SAP NetWeaver para a OAuth](#configure-sap-netweaver-for-oauth)** configurar as definições de OAuth no lado da aplicação.

## <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com o SAP NetWeaver, execute os seguintes passos:

1. Abra uma nova janela do navegador web e inscreva-se no site da sua empresa SAP NetWeaver como administrador

1. Certifique-se de que os serviços **http** e **https** estão ativos e as portas apropriadas são atribuídas no Código T **SMICM.**

1. Inscreva-se no cliente comercial do Sistema SAP (T01), onde o SSO é necessário e ative a GEStão da sessão de segurança HTTP.

    a. Vá ao código de transações **SICF_SESSIONS.** Apresenta todos os parâmetros de perfil relevantes com valores atuais. Parecem abaixo: -
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
    > Ajuste os parâmetros acima dos parâmetros de acordo com os requisitos da sua organização, os parâmetros acima são indicados apenas.

    b. Se necessário, ajuste os parâmetros, no perfil de exemplo/padrão do sistema SAP e reiniciar o sistema SAP.

    c. Clique duas vezes no cliente relevante para ativar a sessão de segurança HTTP.

    ![O link de download de certificado](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Ativar abaixo os serviços do SICF:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
1. Aceda ao código de transação **SAML2** no cliente comercial do sistema SAP [T01/122]. Abrirá uma interface de utilizador num browser. Neste exemplo, assumimos 122 como cliente comercial da SAP.

    ![O link de download de certificado](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

1. Forneça o seu nome de utilizador e palavra-passe para introduzir na interface do utilizador e clique em **Editar**.

    ![O link de download de certificado](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

1. Substitua o nome do **fornecedor** de T01122 para `http://T01122` e clique em **Guardar**.

    > [!NOTE]
    > Por predefinição, o nome do fornecedor surge como formato `<sid><client>` mas a Azure AD espera que o nome no formato de `<protocol>://<name>`, recomendando manter o nome do fornecedor como `https://<sid><client>` para permitir que vários motores SAP NetWeaver ABAP configuram em Azure AD.

    ![O link de download de certificado](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

1. **Gerando metadados**do fornecedor de serviços :- Assim que terminarmos a configuração das definições de **Fornecedor Local** e **Fornecedores Fidedignos** na Interface de Utilizador SAML 2.0, o próximo passo seria gerar o ficheiro de metadados do prestador de serviços (que conteria todas as definições, contextos de autenticação e outras configurações no SAP). Uma vez gerado este ficheiro, precisamos de carregar isto em Azure AD.

    ![O link de download de certificado](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Vá ao **separador Fornecedor Local**.

    b. Clique em **Metadados**.

    c. Guarde o **ficheiro Metadados XML** gerado no seu computador e carregue-o na secção **de Configuração Básica SAML** para autopovoar os valores **de URL** do **Identificador** e resposta no portal Azure.

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **SAP NetWeaver,** encontre a secção **Gerir** e selecione **um único sinal .**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado **idp,** execute o seguinte passo:

    a. Clique no **ficheiro de metadados do Upload** para carregar o ficheiro de metadados do Fornecedor de **Serviços**, que obteve anteriormente.

    b. Clique no logotipo da **pasta** para selecionar o ficheiro de metadados e clicar em **Carregar**.

    c. Após o ficheiro de metadados ser carregado com sucesso, os valores de URL do **Identificador** e **da Resposta** ficam povoados automaticamente na caixa de texto da secção de **configuração Básica SAML,** como mostrado abaixo:

    d. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão: `https://<your company instance of SAP NetWeaver>`

    > [!NOTE]
    > Temos visto poucos clientes a reportarum erro de URL de resposta incorreto configurado para a sua instância. Se receber qualquer erro deste tipo, pode utilizar o seguinte script PowerShell como um trabalho para definir o URL de resposta correto para a sua instância.:
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > ServicePrincipal Object ID deve ser definido por si mesmo primeiro ou pode passar isso também aqui.

1. A aplicação SAP NetWeaver espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique no ícone **Editar** para abrir o diálogo de Atributos do Utilizador.

    ![image](common/edit-attribute.png)

1. Na secção **Reivindicações** do Utilizador no diálogo **de Atributos** do Utilizador, configure o atributo token SAML como mostrado na imagem acima e execute os seguintes passos:

    a. Clique no **ícone Editar** para abrir o diálogo de reivindicações do **utilizador Gerir.**

    ![image](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. Na lista **de Transformação,** selecione **ExtractMailPrefix()** .

    c. Na lista **parametómetro 1,** selecione **user.userprincipalname**.

    d. Clique em **Guardar**.

1. Na configuração de um único sign-on com a página **SAML,** na secção certificado de **assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de download de certificado](common/metadataxml.png)

1. Na secção **Configurar sAP NetWeaver,** copie os URL(s) adequados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
    1. No campo **Nome**, introduza `B.Simon`.  
    1. No campo **de nome do utilizador,** introduza o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
    1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
    1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao SAP NetWeaver.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **SAP NetWeaver**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-sap-netweaver-using-saml"></a>Configure SAP NetWeaver usando o SAML

1. Inscreva-se no sistema SAP e vá para o código de transação SAML2. Abre a nova janela do navegador com ecrã de configuração SAML.

2. Para configurar pontos finais para fornecedor de identidade fidedigno (Azure AD) vá ao separador **Trust Providers.**

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. Pressione **Adicionar** e selecione **Upload Metadata File** a partir do menu de contexto.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Faça upload do ficheiro de metadados, que descarregou a partir do portal Azure.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. No próximo ecrã, escreva o nome Alias. Por exemplo, adsts e pressione **Next** para continuar.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. Certifique-se de que o seu **Algoritmo Digestdeve** ser **SHA-256** e não necessitar de alterações e prima **A seguir**.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. Em **pontos finais de sinal único,** utilize **HTTP POST** e clique em **Next** para continuar.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. Em **pontos finais de logout simples,** selecione **HTTPRedirect** e clique **em Next** para continuar.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. Em **Artifact Endpoints**, pressione **Next** para continuar.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. Nos **Requisitos de Autenticação,** clique em **Terminar**.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Vá para aba Trusted **Provider** > **Identity Federation** (a partir da parte inferior do ecrã). Clique em **Editar**.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. Clique em **Adicionar** sob o separador **Da Federação de Identidade** (janela inferior).

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. A partir da janela pop-up, selecione **Não Especificado** a partir dos **formatos NameID suportados** e clique em OK.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. Note que os valores do modo de **mapeamento** ID do **utilizador** e do utilizador determinam a ligação entre o utilizador SAP e a alegação da AD Azure.  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Cenário: Utilizador SAP para mapeamento de utilizador da AD Azure.

    a. NameID detalha a imagem do SAP.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Screenshot mencionando as reivindicações necessárias da AD Azure.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Cenário: Selecione ID do utilizador SAP com base no endereço de e-mail configurado em SU01. Neste caso, o ID de e-mail deve ser configurado em su01 para cada utilizador que necessite de SSO.

    a.  NameID detalha a imagem do SAP.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. Screenshot mencionando as reivindicações necessárias da AD Azure.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/claimsaad2.png)

15. Clique em **Guardar** e, em seguida, clique em **Ativar** o fornecedor de identidade.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/configuration1.png)

16. Clique **OK** uma vez solicitado.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/configuration2.png)

    ### <a name="create-sap-netweaver-test-user"></a>Criar o utilizador de teste SAP NetWeaver

    Nesta secção, cria-se um utilizador chamado B.simon no SAP NetWeaver. Por favor, trabalhe na sua equipa de especialistas da SAP ou trabalhe com o seu parceiro SAP da organização para adicionar os utilizadores na plataforma SAP NetWeaver.

## <a name="test-sso"></a>Teste SSO

1. Assim que o fornecedor de identidade Azure AD foi ativado, tente aceder abaixo do URL para verificar o SSO (não haverá qualquer pedido para o nome de utilizador e palavra-passe)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (ou) utilizar o URL abaixo

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Substitua o sapurl pelo nome de anfitrião SAP real.

2. O URL acima deve levá-lo para o ecrã abaixo mencionado. Se conseguir chegar à página abaixo, a configuração Azure AD SSO é feita com sucesso.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/testingsso.png)

3. Se ocorrer um pedido de username e password, por favor diagnostice o problema ativando o rastreio usando url abaixo

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="configure-sap-netweaver-for-oauth"></a>Configure SAP NetWeaver para OAuth

1. O processo documentado SAP está disponível no local: [NetWeaver Gateway Service Enableing e OAuth 2.0 Scope Creation](https://wiki.scn.sap.com/wiki/display/Security/NetWeaver+Gateway+Service+Enabling+and+OAuth+2.0+Scope+Creation)

2. Vá à SPRO e encontre **serviços Activate e Keep.**

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/oauth01.png)

3. Neste exemplo queremos ligar o serviço OData: `DAAG_MNGGRP` com o OAuth ao Azure AD SSO. Utilize a procura de nome de serviço técnico para o serviço `DAAG_MNGGRP` e ative se ainda não estiver ativo, já (procure `green` estado sob o separador de nós do ICF). Certifique-se de que o pseudónimo do sistema (o sistema de backend ligado, onde o serviço realmente funciona) está correto.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/oauth02.png)

    * Em seguida, clique no botão **OAuth** na barra de botões superior e atribua `scope` (mantenha o nome predefinido como oferecido).

4. Por exemplo, o âmbito é `DAAG_MNGGRP_001`, é gerado a partir do nome do serviço adicionando automaticamente um número. O relatório `/IWFND/R_OAUTH_SCOPES` pode ser usado para alterar o nome do âmbito ou criar manualmente.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/oauth03.png)

    > [!NOTE]
    > Mensagem `soft state status is not supported` – pode ser ignorada, como nenhum problema. Para mais detalhes, consulte [aqui](https://help.sap.com/doc/saphelp_nw74/7.4.16/1e/c60c33be784846aad62716b4a1df39/content.htm?no_cache=true)

### <a name="create-a-service-user-for-the-oauth-20-client"></a>Criar um utilizador de serviço para o Cliente OAuth 2.0

1. A OAuth2 usa um `service ID` para obter o sinal de acesso para o utilizador final em seu nome. Restrição importante por parte da OAuth: o `OAuth 2.0 Client ID` deve ser idêntico ao `username` o cliente OAuth 2.0 usa para iniciar sessão ao solicitar um Access Token. Por isso, por exemplo, vamos registar um cliente OAuth 2.0 com o nome CLIENT1, e como pré-requisito um utilizador com o mesmo nome (CLIENTE1) deve existir no sistema SAP e esse utilizador que configurarpara ser utilizado pela referida aplicação. 

2. Ao registar um Cliente OAuth usamos o `SAML Bearer Grant type`.

    >[!NOTE]
    >Para mais detalhes, consulte o Registo de Clientes OAuth 2.0 para o Tipo de Subvenção ao Portador SAML [aqui](https://wiki.scn.sap.com/wiki/display/Security/OAuth+2.0+Client+Registration+for+the+SAML+Bearer+Grant+Type)

3. tcod: SU01 / criar cliente 1 utilizador como `System type` e atribuir senha, guarde-a como necessidade de fornecer a credencial ao programador API, que deve queimá-lo com o nome de utilizador para o código de chamada. Nenhum perfil ou papel deve ser atribuído.

### <a name="register-the-new-oauth-20-client-id-with-the-creation-wizard"></a>Registe o novo Id do Cliente OAuth 2.0 com o assistente de criação

1. Para registar uma nova operação de início de início de **cliente OAuth 2.0** **SOAUTH2**. A transação apresentará uma visão geral sobre os clientes OAuth 2.0 que já estavam registados. Escolha **criar** para iniciar o assistente para o novo cliente OAuth nomeado como CLIENTE1 neste exemplo.

2. Vá ao Código T: **SOAUTH2** e forneça a descrição e, em seguida, clique em **seguida**.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/oauth04.png)

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/oauth05.png)

3. Selecione o Já adicionado **IdP – Azure AD** da lista de dropdown e poupe.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/oauth06.png)

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/oauth07.png)

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/oauth08.png)

4. Clique em **Adicionar** a atribuição de âmbito para adicionar o âmbito previamente criado: `DAAG_MNGGRP_001`

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/oauth09.png)

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/oauth10.png)

5. Clique no **acabamento**.

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente sAP NetWeaver com Azure AD](https://aad.portal.azure.com/)