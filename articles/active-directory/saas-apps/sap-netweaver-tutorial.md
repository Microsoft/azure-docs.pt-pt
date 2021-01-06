---
title: 'Tutorial: Tutorial: Azure Ative Directory Single sign-on (SSO) integração com SAP NetWeaver / Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o SAP NetWeaver.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/11/2020
ms.author: jeedes
ms.openlocfilehash: a282e1be019c697ad15d86df56ad191043ab4ae9
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935851"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-netweaver"></a>Tutorial: Azure Ative Directory Single sign-on (SSO) integração com SAP NetWeaver

Neste tutorial, você vai aprender a integrar o SAP NetWeaver com o Azure Ative Directory (Azure AD). Quando integrar o SAP NetWeaver com Azure AD, pode:

* Controlo em Azure AD que tem acesso a SAP NetWeaver.
* Permita que os seus utilizadores sejam automaticamente inscritos no SAP NetWeaver com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada no SAP NetWeaver (SSO).
* SAP NetWeaver V7.20 necessários em meio

## <a name="scenario-description"></a>Descrição do cenário

* O SAP NetWeaver suporta tanto **o SAML** **(SP iniciado SSO)** **como o OAuth**. Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. 

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

> [!NOTE]
> Configure a aplicação em SAML ou em OAuth de acordo com o seu requisito organizacional. 

## <a name="adding-sap-netweaver-from-the-gallery"></a>Adicionando SAP NetWeaver da galeria

Para configurar a integração do SAP NetWeaver no AZure AD, é necessário adicionar o SAP NetWeaver da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite SAP NetWeaver** na caixa de pesquisa.
1. Selecione **SAP NetWeaver** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-sap-netweaver"></a>Configure e teste Azure AD SSO para SAP NetWeaver

Configure e teste Azure AD SSO com SAP NetWeaver usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no SAP NetWeaver.

Para configurar e testar o Azure AD SSO com o SAP NetWeaver, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com B.Simon.
    1. **[Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que a B.Simon utilize um único sinal de Ad AD.
1. **[Configure o SAP NetWeaver utilizando](#configure-sap-netweaver-using-saml)** o SAML para configurar as definições SSO no lado da aplicação.
    1. Crie o utilizador de **[teste SAP NetWeaver](#create-sap-netweaver-test-user)** para ter uma contraparte de B.Simon no SAP NetWeaver que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.
1. **[Configure o SAP NetWeaver para a OAuth](#configure-sap-netweaver-for-oauth)** para configurar as definições de OAuth no lado da aplicação.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o SAP NetWeaver, execute os seguintes passos:

1. Abra uma nova janela do navegador web e inscreva-se no site da empresa SAP NetWeaver como administrador

1. Certifique-se de que os serviços **de https** e **https** estão ativos e as portas apropriadas são atribuídas no Código T **da SMICM.**

1. Inscreva-se no cliente empresarial do SAP System (T01), onde o SSO é necessário e ativar a Gestão da sessão de segurança HTTP.

    a. Vá ao código de transação **SICF_SESSIONS**. Apresenta todos os parâmetros de perfil relevantes com os valores atuais. Parecem abaixo: -
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
    > Ajuste acima dos parâmetros de acordo com os requisitos da sua organização, os parâmetros acima são dados aqui apenas como indicação.

    b. Se necessário, ajuste os parâmetros, no perfil de caso/predefinição do sistema SAP e reinicie o sistema SAP.

    c. Clique duas vezes no cliente relevante para ativar a sessão de segurança HTTP.

    ![A sessão de segurança HTTP ](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Ativar abaixo os serviços da SICF:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
1. Vá ao código de transação **SAML2** no cliente comercial do sistema SAP [T01/122]. Abrirá uma interface de utilizador num browser. Neste exemplo, assumimos 122 como cliente comercial da SAP.

    ![Código de transação](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

1. Forneça o seu nome de utilizador e senha para introduzir na interface do utilizador e clique em **Editar.**

    ![nome de utilizador e senha](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

1. Substitua **o nome** do fornecedor de T01122 para e clique `http://T01122` em **Guardar**.

    > [!NOTE]
    > Por defeito, o nome do fornecedor vem como `<sid><client>` formato, mas a Azure AD espera nome no formato de, recomendando manter o nome do `<protocol>://<name>` fornecedor de modo a permitir que `https://<sid><client>` vários motores SAP NetWeaver ABAP se confirem em Azure AD.

    ![Os múltiplos motores SAP NetWeaver ABAP](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

1. **Gerar Metadados do Fornecedor de Serviços**:- Uma vez que terminamos com a configuração das definições **do Fornecedor Local** e dos **Fornecedores Fidedignos** na Interface de Utilizador SAML 2.0, o próximo passo seria gerar o ficheiro de metadados do prestador de serviços (que conteria todas as configurações, contextos de autenticação e outras configurações no SAP). Uma vez gerado este ficheiro, temos de carregar isto em Azure AD.

    ![Metadados geradores de fornecedores de serviços](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Aceda ao **separador Fornecedor Local.**

    b. Clique em **Metadados.**

    c. Guarde o **ficheiro XML de metadados** gerado no seu computador e carregue-o na secção **de Configuração SAML Básica** para autopovoar os valores de URL de **identificação** e **resposta** no portal Azure.

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **SAP NetWeaver,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute o seguinte passo:

    a. Clique **em Carregar o ficheiro de metadados** para carregar o ficheiro de **metadados do Fornecedor de Serviços,** que obteve anteriormente.

    b. Clique no **logotipo da pasta** para selecionar o ficheiro de metadados e clique em **Upload**.

    c. Após o carregamento com sucesso do ficheiro de metadados, os valores de URL **de identificação** e **resposta** são preenchidos automaticamente na caixa de texto da secção **de configuração DE SAML Básica,** conforme mostrado abaixo:

    d. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<your company instance of SAP NetWeaver>`

    > [!NOTE]
    > Temos visto poucos clientes reportarem um erro de URL de resposta incorreto configurado para o seu exemplo. Se receber tal erro, pode utilizar o seguinte script PowerShell como um trabalho para definir o URL de resposta correto para o seu exemplo.:
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > O ID do Objeto ServicePrincipal deve ser definido por si mesmo primeiro ou pode passar isso também aqui.

1. A aplicação SAP NetWeaver espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique **em Editar** o ícone para abrir o diálogo dos Atributos do Utilizador.

    ![editar attribue](common/edit-attribute.png)

1. Na secção **'Reclamações** de Utilizador' no diálogo **'Atributos do Utilizador',** configurar o atributo de ficha SAML como mostrado na imagem acima e executar os seguintes passos:

    a. Clique **em Editar o ícone** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![editar ícone](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. Na lista **de Transformação,** selecione **ExtractMailPrefix()**.

    c. Na lista **do Parâmetro 1,** selecione **user.userprincipalname**.

    d. Clique em **Guardar**.

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar o SAP NetWeaver,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao SAP NetWeaver.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **SAP NetWeaver**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-sap-netweaver-using-saml"></a>Configure o SAP NetWeaver usando o SAML

1. Inscreva-se no sistema SAP e vá para o código de transação SAML2. Abre uma nova janela de navegador com ecrã de configuração SAML.

2. Para configurar pontos de fim para um fornecedor de identidade fidedigno (Azure AD) aceda ao **separador Fornecedores Fidedignos.**

    ![Configure fornecedores únicos Sign-On fidedignos](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. Prima **Adicionar** e selecionar **upload Metadata File** a partir do menu de contexto.

    ![Configurar single Sign-On 2](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Faça upload do ficheiro metadados, que descarregou a partir do portal Azure.

    ![Configurar Sign-On único 3](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. No ecrã seguinte digite o nome Alias. Por exemplo, aadsts e pressione **Next** para continuar.

    ![Configurar single Sign-On 4](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. Certifique-se de que o seu **Algoritmo De Digestão** deve ser **SHA-256** e não requer alterações e prima **Em seguida**.

    ![Configurar Sign-On único 5](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. Em **Single Sign-On Endpoints**, use **HTTP POST** e clique em **Seguinte** para continuar.

    ![Configurar single Sign-On 6](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. Em **Pontos finais de logout único** selecione **HTTPRedirect** e clique **em Seguinte** para continuar.

    ![Configurar single Sign-On 7](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. Em **Artifact Endpoints,** prima **Next** para continuar.

    ![Configurar single Sign-On 8](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. Nos **Requisitos de Autenticação,** clique **em Terminar**.

    ![Configurar single Sign-On 9](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Vá ao separador Federação de Identidade **do Fornecedor Fidedigno**  >   (a partir da parte inferior do ecrã). Clique em **Editar**.

    ![Configurar single Sign-On 10](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. Clique no **separador 'Adicionar'** ao separador **Federação de Identidade** (janela inferior).

    ![Configurar single Sign-On 11](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. A partir da janela pop-up, **selecione Não especificado** dos **formatos NameID suportados** e clique em OK.

    ![Configurar single Sign-On 12](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

1. Dê ao **Utilizador valor de origem** como **Atributo de Afirmação,** valor **do modo de mapeamento do utilizador** como **Email** e **Assertion Attribute Name** como `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    ![Configurar Sign-On Individuais ](./media/sapnetweaver-tutorial/nameid-format.png)

14. Note que os valores do modo de mapeamento **de ID Source** e **User ID** determinam a ligação entre o utilizador SAP e a alegação AD do Azure.

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Cenário: Cartmão de utilizador SAP para Azure AD.

    a. Imagem de imagem de detalhes nameID da SAP.

    ![Configurar single Sign-On 13](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Screenshot mencionando reclamações necessárias da Azure AD.

    ![Configurar Sign-On 14](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Cenário: Selecione O ID do utilizador SAP com base no endereço de e-mail configurado em SU01. Neste caso, o ID de e-mail deve ser configurado em su01 para cada utilizador que necessite de SSO.

    a.  Imagem de imagem de detalhes nameID da SAP.

    ![Configurar single Sign-On 15](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. screenshot mencionando reclamações necessárias de Azure AD.

    ![Configurar single Sign-On 16](./media/sapnetweaver-tutorial/claimsaad2.png)

15. Clique **em Guardar** e, em seguida, clique em **Ativar** para ativar o fornecedor de identidade.

    ![Configurar single Sign-On 17](./media/sapnetweaver-tutorial/configuration1.png)

16. Clique **em OK** uma vez solicitado.

    ![Configurar single Sign-On 18](./media/sapnetweaver-tutorial/configuration2.png)

    ### <a name="create-sap-netweaver-test-user"></a>Criar utilizador de teste SAP NetWeaver

    Nesta secção, cria-se um utilizador chamado B.simon in SAP NetWeaver. Por favor, trabalhe na sua equipa de peritos da SAP em casa ou trabalhe com o seu parceiro SAP da organização para adicionar os utilizadores na plataforma SAP NetWeaver.

## <a name="test-sso"></a>Teste SSO

1. Uma vez ativado o fornecedor de identidade Azure AD, tente aceder abaixo do URL para verificar SSO (não haverá solicitação para o nome de utilizador & senha)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (ou) usar o URL abaixo

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Substitua o sapurl pelo nome de hospedeiro SAP real.

2. O URL acima deve levá-lo para o ecrã abaixo mencionado. Se conseguir chegar à página abaixo, a configuração Azure AD SSO é feita com sucesso.

    ![testar Sign-On individuais](./media/sapnetweaver-tutorial/testingsso.png)

3. Se ocorrer o nome de utilizador & pedido de senha, por favor diagnostice o problema, ativando o traço utilizando abaixo URL

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="configure-sap-netweaver-for-oauth"></a>Configure SAP NetWeaver para OAuth

1. O processo DOCUMENTADO SAP está disponível no local: [Serviço NetWeaver Gateway Habilitação e Criação de Âmbito OAuth 2.0](https://wiki.scn.sap.com/wiki/display/Security/NetWeaver+Gateway+Service+Enabling+and+OAuth+2.0+Scope+Creation)

2. Vá ao SPRO e encontre **os serviços De ativação e manutenção.**

    ![Ativar e Manter serviços](./media/sapnetweaver-tutorial/oauth01.png)

3. Neste exemplo queremos ligar o serviço OData: `DAAG_MNGGRP` com o OAuth ao Azure AD SSO. Utilize o nome de serviço técnico procurar o serviço `DAAG_MNGGRP` e ativar-se se ainda não estiver ativo (procure `green` o estado no separador nó de nó ICF). Certifique-se de que o pseudónimo do sistema (o sistema de backend ligado, onde o serviço está realmente em funcionamento) está correto.

    ![Serviço OData](./media/sapnetweaver-tutorial/oauth02.png)

    * Em seguida, clique no botão **OAuth** na barra de botão superior e atribua `scope` (mantenha o nome predefinido como oferecido).

4. Para o nosso exemplo, o âmbito é `DAAG_MNGGRP_001` gerado a partir do nome de serviço adicionando automaticamente um número. O relatório `/IWFND/R_OAUTH_SCOPES` pode ser usado para alterar o nome do âmbito ou criar manualmente.

    ![Configure OAuth](./media/sapnetweaver-tutorial/oauth03.png)

    > [!NOTE]
    > Mensagem `soft state status is not supported` – pode ser ignorada, como nenhum problema. Para mais detalhes, consulte [aqui](https://help.sap.com/doc/saphelp_nw74/7.4.16/1e/c60c33be784846aad62716b4a1df39/content.htm?no_cache=true)

### <a name="create-a-service-user-for-the-oauth-20-client"></a>Criar um utilizador de serviço para o Cliente OAuth 2.0

1. A OAuth2 usa a `service ID` para obter o token de acesso para o utilizador final em seu nome. Restrição importante por design OAuth: o `OAuth 2.0 Client ID` deve ser idêntico ao que o cliente `username` OAuth 2.0 usa para login ao solicitar um Token de acesso. Assim, pelo nosso exemplo, vamos registar um cliente OAuth 2.0 com o nome CLIENTE1, e como pré-requisito um utilizador com o mesmo nome (CLIENTE1) deve existir no sistema SAP e esse utilizador vamos configurar para ser usado pela referida aplicação. 

2. Ao registar um Cliente OAuth utilizamos o `SAML Bearer Grant type` .

    >[!NOTE]
    >Para mais detalhes, consulte o OAuth 2.0 Registo do Cliente para o Tipo de Concessão do Portador SAML [aqui](https://wiki.scn.sap.com/wiki/display/Security/OAuth+2.0+Client+Registration+for+the+SAML+Bearer+Grant+Type)

3. tcod: SU01 / criar o utilizador CLIENT1 como `System type` e atribuir senha, guarde-a como necessário para fornecer a credencial ao programador API, que deve queimá-la com o nome de utilizador para o código de chamada. Nenhum perfil ou função deve ser atribuído.

### <a name="register-the-new-oauth-20-client-id-with-the-creation-wizard"></a>Registe o novo ID do cliente OAuth 2.0 com o assistente de criação

1. Para registar um novo **cliente OAuth 2.0** iniciar a transação **SOAUTH2**. A transação apresentará uma visão geral sobre os clientes OAuth 2.0 que já estavam registados. Escolha **Criar** para iniciar o assistente para o novo cliente OAuth nomeado como CLIENTE1 neste exemplo.

2. Vá ao Código T: **SOAUTH2** e forneça a descrição e clique **em seguida.**

    ![SOAUTH2](./media/sapnetweaver-tutorial/oauth04.png)

    ![ID do cliente OAuth 2.0](./media/sapnetweaver-tutorial/oauth05.png)

3. Selecione o já adicionado **SAML2 IdP – AD Azure** da lista de dropdown e guarde.

    ![SAML2 IdP – Azure AD 1](./media/sapnetweaver-tutorial/oauth06.png)

    ![SAML2 IdP – Azure AD 2](./media/sapnetweaver-tutorial/oauth07.png)

    ![SAML2 IdP – Azure AD 3](./media/sapnetweaver-tutorial/oauth08.png)

4. Clique em **Adicionar** na atribuição de âmbito para adicionar o âmbito previamente criado: `DAAG_MNGGRP_001`

    ![Âmbito](./media/sapnetweaver-tutorial/oauth09.png)

    ![atribuição de âmbito](./media/sapnetweaver-tutorial/oauth10.png)

5. Clique **em acabamento**.

## <a name="next-steps"></a>Passos Seguintes

Assim que configurar o Azure AD SAP NetWeaver, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O Controlo de Sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
