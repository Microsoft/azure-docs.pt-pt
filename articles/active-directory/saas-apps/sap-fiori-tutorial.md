---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com sap Fiori [ Integração de diretório sativos) com a SAP Fiori [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SAP Fiori.
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
ms.openlocfilehash: 917ba9274276fec5d01a40bdf7219e8d4fee1395
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78897766"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-fiori"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com sAP Fiori

Neste tutorial, você vai aprender a integrar o SAP Fiori com o Azure Ative Directory (Azure AD). Quando integrar o SAP Fiori com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à SAP Fiori.
* Permita que os seus utilizadores sejam automaticamente inscritos no SAP Fiori com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* SAP Fiori única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* SAP Fiori apoia **SP** iniciado SSO

> [!NOTE]
> Para a Autenticação iFrame iniciada pela SAP Fiori, recomendamos a utilização do parâmetro **IsPassive** no AuthnRequest SAML para autenticação silenciosa. Para mais detalhes sobre o parâmetro **IsPassive** consulte a Informação de [assinatura única da AD Azure SAML](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol)

## <a name="adding-sap-fiori-from-the-gallery"></a>Adicionando SAP Fiori da galeria

Para configurar a integração do SAP Fiori no Azure AD, é necessário adicionar o SAP Fiori da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **SAP Fiori** na caixa de pesquisa.
1. Selecione **SAP Fiori** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-fiori"></a>Configure e teste Azure AD único signo para SAP Fiori

Configure e teste Azure AD SSO com SAP Fiori utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no SAP Fiori.

Para configurar e testar o Azure AD SSO com o SAP Fiori, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o SAP Fiori SSO](#configure-sap-fiori-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie um utilizador de teste SAP Fiori](#create-sap-fiori-test-user)** - para ter uma contrapartida de B.Simon no SAP Fiori que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. Abra uma nova janela do navegador web e inscreva-se no site da sua empresa SAP Fiori como administrador.

1. Certifique-se de que os serviços **http** e **https** estão ativos e que as portas relevantes são atribuídas ao código de transação **SMICM**.

1. Inscreva-se no SAP Business Client para o sistema **SAP T01,** onde é necessário um único login. Em seguida, ativar HTTP Security Session Management.

    1. Vá para o código de transação **SICF_SESSIONS.** Todos os parâmetros de perfil relevantes com valores atuais são mostrados. Parecem o seguinte exemplo:

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

        ![Os Valores Atuais dos Parâmetros de Perfil Relevantes na página SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. Ativar os seguintes serviços SICF:

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Vá ao código de transação **SAML2** no Business Client para o sistema SAP [**T01/122**]. A configuração UI abre numa nova janela do navegador. Neste exemplo, utilizamos o Cliente De Negócios para o sistema SAP 122.

    ![Página de login do Cliente Comercial SAP Fiori](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Introduza o seu nome de utilizador e palavra-passe e, em seguida, selecione **Iniciar sessão**.

    ![A página SAML 2.0 Configuração do Sistema ABAP T01/122 no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. Na caixa **'Nome** fornecedora', substitua **o T01122** por **http:\//T01122**, e, em seguida, selecione **Guardar**.

    > [!NOTE]
    > Por predefinição, o nome \<do \<fornecedor encontra-se no formato sid>cliente>. A Azure AD espera \<que o\<nome no protocolo de formato> nome>. Recomendamos que mantenha o nome\://\<do \<fornecedor como https sid>cliente> para que possa configurar vários motores SAP Fiori ABAP em Azure AD.

    ![O nome do fornecedor atualizado na página SAML 2.0 Configuração do Sistema ABAP T01/122 no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Selecione Dados**de** **dados do separador** > Local fornecedor .

1. Na caixa de diálogo **SAML 2.0 Metadata,** descarregue o ficheiro XML de metadados gerados e guarde-o no seu computador.

    ![Ligação de dados de descarregamento na caixa de diálogo SAP SAML 2.0 Metadados](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **SAP Fiori,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** se tiver ficheiro de **metadados do Prestador**de Serviços, execute os seguintes passos:

    a. Clique no **ficheiro de metadados de upload**.

    ![Upload ficheiro de metadados](common/upload-metadata.png)

    b. Clique no logotipo da **pasta** para selecionar o ficheiro de metadados e clicar em **Carregar**.

    ![escolher ficheiro de metadados](common/browse-upload-metadata.png)

    c. Quando o ficheiro de metadados é carregado com sucesso, os valores de URL do **Identificador** e **da Resposta** são automaticamente povoados no painel **de configuração SAML básico.** Na caixa **de URL Sign on,** introduza `https:\//\<your company instance of SAP Fiori\>`um URL com o seguinte padrão: .

    > [!NOTE]
    > Alguns clientes relatam erros relacionados com valores de URL de **resposta** incorretamente configurados. Se vir este erro, pode utilizar o seguinte script PowerShell para definir o URL de resposta correto para a sua instância:
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > Pode definir `ServicePrincipal` o id do objeto antes de executar o script, ou pode passá-lo aqui.

1. A aplicação SAP Fiori espera que as afirmações do SAML estejam num formato específico. Configure as seguintes reclamações para esta aplicação. Para gerir estes valores de atributo, no **set up Single Sign-On com painel SAML,** selecione **Editar**.

    ![O painel de atributos do Utilizador](common/edit-attribute.png)

1. No painel **de atributos do utilizador & Reclamações,** configure os atributos de ficha SAML como mostrado na imagem anterior. Em seguida, complete os seguintes passos:

    1. Selecione **Editar** para abrir o painel de **reclamações do utilizador Gerir.**

    1. Na lista **de Transformação,** selecione **ExtractMailPrefix()**.

    1. Na lista **parameter 1,** selecione **user.userprincipalname**.

    1. Selecione **Guardar**.

       ![O painel de reclamações do utilizador gerir](./media/sapfiori-tutorial/nameidattribute.png)

       ![A secção de transformação no painel de reivindicações do utilizador gerir](./media/sapfiori-tutorial/nameidattribute1.png)
    
1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção **SAP Fiori, copie** os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao SAP Fiori.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **SAP Fiori**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-sap-fiori-sso"></a>Configure SAP Fiori SSO

1. Inscreva-se no sistema SAP e vá para o código de transação **SAML2**. Uma nova janela do navegador abre com a página de configuração SAML.

1. Para configurar pontos finais para um fornecedor de identidade fidedigno (Azure AD), selecione o separador **Fornecedores Fidedignos.**

    ![O separador Fornecedores fidedignos no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. **Selecione Adicionar**, e, em seguida, selecione **Upload Metadata File** a partir do menu de contexto.

    ![As opções de Ficheiros de Dados de Adicionar e Upload no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Faça upload do ficheiro de metadados que descarregou no portal Azure. Selecione **Next**.

    ![Selecione o ficheiro de metadados para carregar no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. Na página seguinte, na caixa de **pseudónimos,** insira o nome do pseudónimo. Por exemplo, **adsts.** Selecione **Next**.

    ![A caixa de pseudónimos em SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Certifique-se de que o valor na caixa **de algoritmos digestiva** é **SHA-256**. Selecione **Next**.

    ![Verifique o valor do Algoritmo digestiva em SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. Em **pontos finais de assinatura simples,** selecione **HTTP POST**, e, em seguida, selecione **Next**.

    ![Opções de pontos finais de inscrição única sap](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. Em **pontos finais de logout simples,** selecione **HTTP Redirect**, e, em seguida, selecione **Next**.

    ![Opções single Logout Endpoints em SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. Em **Artifact Endpoints,** selecione **Next** para continuar.

    ![Opções de Pontos Finais de Artefactos em SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. Em acordo com **os requisitos de autenticação,** selecione **Terminar**.

    ![Opções de Requisitos de Autenticação e a opção Acabamento em SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. Selecione **Trusted Provider** > **Identity Federation** (na parte inferior da página). Selecione **Editar**.

    ![Os separadores trust Provider e Identity Federation em SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. Selecione **Adicionar**.

    ![A opção Adicionar no separador Da Federação de Identidade](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. Na caixa de diálogo de **formatos NameID suportados,** selecione **Não Especificado**. Selecione **OK**.

    ![A caixa de diálogo e opções de dialog formatos NameID suportados no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    Os valores para o modo de **identificação** de id do **utilizador** e do utilizador determinam a ligação entre o utilizador SAP e a reivindicação da AD Azure.  

    **Cenário 1**: Utilizador SAP para mapeamento de utilizador da AD Azure

    1. No SAP, em **detalhes do formato NameID "Não especificado",** note os detalhes:

        ![Os detalhes da caixa de diálogo "Não Especificado" do Formato NameID no SAP](./media/sapfiori-tutorial/nameiddetails.png)

    1. No portal Azure, no âmbito dos **Atributos de Utilizador & Reclamações,** note as reclamações exigidas pela Azure AD.

        ![O utilizador atribui & caixa de diálogo Claims no portal Azure](./media/sapfiori-tutorial/claimsaad1.png)

    **Cenário 2**: Selecione o ID do utilizador SAP com base no endereço de e-mail configurado em SU01. Neste caso, o ID de e-mail deve ser configurado em SU01 para cada utilizador que necessite de SSO.

    1.  No SAP, em **detalhes do formato NameID "Não especificado",** note os detalhes:

        ![Os detalhes da caixa de diálogo "Não Especificado" do Formato NameID no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. No portal Azure, no âmbito dos **Atributos de Utilizador & Reclamações,** note as reclamações exigidas pela Azure AD.

       ![O utilizador atribui & caixa de diálogo Claims no portal Azure](./media/sapfiori-tutorial/claimsaad2.png)

1. Selecione **Guardar**e, em seguida, selecione **Ativar** para ativar o fornecedor de identidade.

    ![As opções de poupar e ativar em SAP](./media/sapfiori-tutorial/configuration1.png)

1. Selecione **OK** quando solicitado.

    ![A opção OK na caixa de diálogo de configuração SAML 2.0 no SAP](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-sap-fiori-test-user"></a>Criar o utilizador de teste SAP Fiori

Nesta secção, cria-se uma utilizadora chamada Britta Simon no SAP Fiori. Trabalhe com a sua equipa interna de especialistas da SAP ou com o seu parceiro SAP para adicionar o utilizador na plataforma SAP Fiori.

## <a name="test-sso"></a>Teste SSO

1. Depois de o fornecedor de identidade Azure AD ser ativado no SAP Fiori, tente aceder a um dos seguintes URLs para testar um único sinal (não deve ser solicitado para um nome de utilizador e senha):

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > Substitua o *sapurl* pelo nome atual do hospedeiro SAP.

1. O URL de teste deve levá-lo à seguinte página de aplicação de teste no SAP. Se a página abrir, o azure AD único sign-on é configurado com sucesso.

    ![A página padrão de aplicação de teste no SAP](./media/sapfiori-tutorial/testingsso.png)

1. Se for solicitado um nome de utilizador e uma senha, ative o rastreio para ajudar a diagnosticar o problema. Utilize o seguinte URL para\//\<o rastreio:\>https: sapurl /sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#.

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente sAP Fiori com Azure AD](https://aad.portal.azure.com/)
