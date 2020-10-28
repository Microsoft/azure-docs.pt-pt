---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com SAP Fiori Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SAP Fiori.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.openlocfilehash: 35bb05eb019a1284a8a418df0197960130e8d013
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897301"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-fiori"></a>Tutorial: Azure Ative Directory integração única (SSO) com SAP Fiori

Neste tutorial, você vai aprender a integrar SAP Fiori com Azure Ative Directory (Azure AD). Quando integrar o SAP Fiori com Azure AD, pode:

* Controlo em Azure AD que tem acesso à SAP Fiori.
* Permita que os seus utilizadores sejam automaticamente inscritos na SAP Fiori com as suas contas AD AZure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada pela SAP Fiori (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* SAP Fiori apoia SSO iniciado **pela SP**

> [!NOTE]
> Para a autenticação iniciada pelo CABO SAP Fiori, recomendamos a utilização do parâmetro **IsPassive** no AuthnRequest SAML para autenticação silenciosa. Para mais detalhes sobre o parâmetro **IsPassive** consulte a [Azure AD SAML informações únicas de súprização](../develop/single-sign-on-saml-protocol.md)

## <a name="adding-sap-fiori-from-the-gallery"></a>Adicionando SAP Fiori da galeria

Para configurar a integração do SAP Fiori no Azure AD, é necessário adicionar o SAP Fiori da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações** .
1. Para adicionar nova aplicação, selecione **Nova aplicação** .
1. Na secção Adicionar a partir da secção **da galeria,** escreva **SAP Fiori** na caixa de pesquisa.
1. Selecione **SAP Fiori** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-fiori"></a>Configurar e testar Azure AD único sinal para SAP Fiori

Configure e teste Azure AD SSO com SAP Fiori usando um utilizador de teste chamado **B.Simon** . Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no SAP Fiori.

Para configurar e testar o Azure AD SSO com o SAP Fiori, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o SAP Fiori SSO](#configure-sap-fiori-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create SAP Fiori test user](#create-sap-fiori-test-user)** - para ter uma contraparte de B.Simon em SAP Fiori que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. Abra uma nova janela do navegador web e inscreva-se no seu site da empresa SAP Fiori como administrador.

1. Certifique-se de que os serviços **de https** e **https** estão ativos e que as portas relevantes são atribuídas ao código de transação **SMICM** .

1. Inscreva-se no SAP Business Client para o sistema SAP **T01,** onde é necessário um único login. Em seguida, ative a gestão da sessão de segurança HTTP.

    1. Vá ao código de transação **SICF_SESSIONS** . Todos os parâmetros de perfil relevantes com os valores atuais são mostrados. Parecem o seguinte exemplo:

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
        > Ajuste os parâmetros com base nos requisitos da sua organização. Os parâmetros anteriores são dados apenas como exemplo.

    1. Se necessário, ajuste os parâmetros no perfil (predefinido) do sistema SAP e reinicie o sistema SAP.

    1. Clique duas vezes no cliente relevante para ativar uma sessão de segurança HTTP.

        ![Os valores atuais da página de parâmetros de perfil relevante no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. Ativar os seguintes serviços SICF:

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Vá ao código de transação **SAML2** em Cliente Comercial para sistema SAP [ **T01/122** ]. A configuração UI abre numa nova janela do navegador. Neste exemplo, utilizamos o Business Client para o sistema SAP 122.

    ![A página de login do cliente comercial SAP Fiori](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Introduza o seu nome de utilizador e senha e, em seguida, **selecione Iniciar sessão** .

    ![A configuração SAML 2.0 do Sistema ABAP T01/122 página em SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. Na caixa **'Nome provedor',** substitua **o T01122** por **http: \/ /T01122** , e, em seguida, selecione **Guardar** .

    > [!NOTE]
    > Por predefinição, o nome do fornecedor está no formato \<sid> \<client> . A Azure AD espera o nome no formato \<protocol> . \<name> Recomendamos que mantenha o nome do fornecedor como https \: // \<sid> \<client> para que possa configurar vários motores SAP Fiori ABAP em Azure AD.

    ![O nome do fornecedor atualizado na configuração SAML 2.0 do Sistema ABAP T01/122 página em SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Selecione **Os**  >  **metadados** do fornecedor local.

1. Na caixa de diálogo de **metadados SAML 2.0,** descarregue o ficheiro XML de metadados gerado e guarde-o no seu computador.

    ![A ligação de metadados de descarregamento na caixa de diálogo de metadados SAP SAML 2.0](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **SAP Fiori,** encontre a secção **Gerir** e selecione **um único sinal de sação** .
1. Na página de método **de inscrição** única, selecione **SAML** .
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** se tiver **um ficheiro de metadados do Fornecedor de Serviços,** execute os seguintes passos:

    a. Clique **em Carregar o ficheiro de metadados** .

    ![Carregar ficheiro de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o ficheiro de metadados e clique em **Upload** .

    ![escolher arquivo de metadados](common/browse-upload-metadata.png)

    c. Quando o ficheiro de metadados é carregado com sucesso, os valores de URL **de identificação** e **resposta** são automaticamente povoados no painel **de configuração de SAML básico.** Na placa na caixa **URL,** introduza um URL com o seguinte padrão: `https:\//\<your company instance of SAP Fiori\>` .

    > [!NOTE]
    > Alguns clientes relatam erros relacionados com valores **de URL de resposta** configurados incorretamente. Se vir este erro, pode utilizar o seguinte script PowerShell para definir o URL de resposta correto para o seu exemplo:
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > Pode definir o `ServicePrincipal` objeto iD antes de executar o script, ou pode passá-lo aqui.

1. A aplicação SAP Fiori espera que as afirmações da SAML estejam num formato específico. Configure os seguintes pedidos para este pedido. Para gerir estes valores de atributo, na configuração single Sign-On com painel **SAML,** selecione **Editar** .

    ![O Painel de Atributos do Utilizador](common/edit-attribute.png)

1. No painel **"Atributos & Reclamações** do Utilizador", configurar os atributos de token SAML, tal como mostrado na imagem anterior. Em seguida, complete os seguintes passos:

    1. **Selecione Editar** para abrir o painel de reclamações do **utilizador Gerir.**

    1. Na lista **de Transformação,** selecione **ExtractMailPrefix()** .

    1. Na lista **do Parâmetro 1,** selecione **user.userprincipalname** .

    1. Selecione **Guardar** .

       ![O painel de reclamações do utilizador Manage](./media/sapfiori-tutorial/nameidattribute.png)

       ![A secção de transformação no painel de reclamações do utilizador Gerir](./media/sapfiori-tutorial/nameidattribute1.png)
    
1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar SAP Fiori,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory** , selecione **Utilizadores** , e, em seguida, selecione **Todos os utilizadores** .
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome** , introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao SAP Fiori.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações** .
1. Na lista de candidaturas, selecione **SAP Fiori** .
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos** .

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-sap-fiori-sso"></a>Configurar SAP Fiori SSO

1. Inscreva-se no sistema SAP e vá para o código de transação **SAML2** . Uma nova janela do navegador abre com a página de configuração SAML.

1. Para configurar pontos finais para um fornecedor de identidade fidedigno (Azure AD), selecione o **separador Fornecedores Fidedignos.**

    ![O separador De Fornecedores Fidedignos em SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. Selecione **Adicionar** e, em seguida, selecione **Upload Metadata File** a partir do menu de contexto.

    ![As opções de Ficheiro de Metadados Add e Upload em SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Faça o upload do ficheiro de metadados que descarregou no portal Azure. Selecione **Seguinte** .

    ![Selecione o ficheiro de metadados para carregar em SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. Na página seguinte, na caixa **de Alias, insira** o nome do pseudónimo. Por exemplo, **aadsts.** Selecione **Seguinte** .

    ![A caixa de Alias em SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Certifique-se de que o valor na caixa **de algoritmos Digest** é **SHA-256** . Selecione **Seguinte** .

    ![Verifique o valor do algoritmo Digest em SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. Em **Pontos finais de Sign-On,** selecione **HTTP POST** e, em seguida, selecione **Next** .

    ![Opções de pontos finais de Sign-On única em SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. Em **pontos finais únicos do logoto** , selecione **HTTP Redirect** , e, em seguida, selecione **Next** .

    ![Opções de pontos finais de logout único em SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. Em **Pontos Finais de Artefactos,** selecione **Seguinte** para continuar.

    ![Opções de pontos finais de artefactos em SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. Nos **requisitos de autenticação** , selecione **Acabamento** .

    ![Opções de Requisitos de Autenticação e a opção Acabamento em SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. Selecione Federação de Identidade **de Fornecedor fidedignos**  >  **Identity Federation** (na parte inferior da página). Selecione **Editar** .

    ![Os separadores de Provedor e Identidade fidedignos no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. Selecione **Adicionar** .

    ![A opção Adicionar no separador Federação de Identidade](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. Na caixa de diálogo **de formatos NameID suportados,** selecione **Não Especificado** . Selecione **OK** .

    ![A caixa de diálogo de formatos NameID suportados e opções em SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    Os valores para **o Modo de ID do utilizador** e para o modo de **mapeamento do ID do utilizador** determinam a ligação entre o utilizador SAP e a reclamação Azure AD.  

    **Cenário 1** : Utilizador SAP para mapeamento de utilizador Azure AD

    1. No SAP, em **Detalhes do Formato NameID "Não Especificado",** note os detalhes:

        ![Screenshot que mostra a caixa de diálogo "Detalhes do Formato NameID "Não Especificado" em S A P.](./media/sapfiori-tutorial/nameiddetails.png)

    1. No portal Azure, nos **termos dos Atributos do Utilizador & Reclamações,** note as reclamações exigidas da Azure AD.

        ![Screenshot que mostra a caixa de diálogo "User Atributos & Claims".](./media/sapfiori-tutorial/claimsaad1.png)

    **Cenário 2** : Selecione o ID do utilizador SAP com base no endereço de e-mail configurado em SU01. Neste caso, o ID de e-mail deve ser configurado em SU01 para cada utilizador que necessite de SSO.

    1.  No SAP, em **Detalhes do Formato NameID "Não Especificado",** note os detalhes:

        ![Os detalhes da caixa de diálogo "Não Especificado" do Formato NameID em SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. No portal Azure, nos **termos dos Atributos do Utilizador & Reclamações,** note as reclamações exigidas da Azure AD.

       ![A caixa de diálogo & Claims do Utilizador no portal Azure](./media/sapfiori-tutorial/claimsaad2.png)

1. **Selecione Guardar** e, em seguida, selecione **Ative** para ativar o fornecedor de identidade.

    ![As opções De Salvar e Ativar em SAP](./media/sapfiori-tutorial/configuration1.png)

1. Selecione **OK** quando solicitado.

    ![A opção OK na caixa de diálogo de configuração SAML 2.0 em SAP](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-sap-fiori-test-user"></a>Criar utilizador de teste SAP Fiori

Nesta secção, cria-se um utilizador chamado Britta Simon na SAP Fiori. Trabalhe com a sua equipa interna de especialistas SAP ou com o parceiro SAP da sua organização para adicionar o utilizador na plataforma SAP Fiori.

## <a name="test-sso"></a>Teste SSO

1. Depois de o fornecedor de identidade Azure AD ser ativado no SAP Fiori, tente aceder a um dos urLs seguintes para testar um único sign-on (não deve ser solicitado para um nome de utilizador e senha):

    * https: \/ / \<sapurl\> /sap/bc/bsp/sap/it00/default.htm
    * https: \/ / \<sapurl\> /sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > Substitua *o sapurl* pelo nome de hospedeiro SAP.

1. O URL de teste deve levá-lo à seguinte página de aplicação de teste em SAP. Se a página abrir, o Azure AD é configurado com sucesso.

    ![A página padrão de aplicação de teste em SAP](./media/sapfiori-tutorial/testingsso.png)

1. Se for solicitado um nome de utilizador e uma palavra-passe, ative o rastreio para ajudar a diagnosticar o problema. Utilize o seguinte URL para o rastreio: https: \/ / \<sapurl\> /sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#.

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente SAP Fiori com Azure AD](https://aad.portal.azure.com/)