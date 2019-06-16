---
title: 'Tutorial: A integração do Azure Active Directory com SAP Fiori | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e no SAP Fiori.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 77ad13bf-e56b-4063-97d0-c82a19da9d56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: jeedes
ms.openlocfilehash: 897685eb967e03cfd30182eec6b237e27386496c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092156"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-fiori"></a>Tutorial: Integração do Azure Active Directory com SAP Fiori

Neste tutorial, saiba como integrar no SAP Fiori com o Azure Active Directory (Azure AD).

Integrar no SAP Fiori no Azure AD oferece as seguintes vantagens:

* Pode utilizar o Azure AD para controlar quem tem acesso ao SAP Fiori.
* Os utilizadores podem ser automaticamente sessão iniciados no SAP Fiori com as suas contas do Azure AD (início de sessão único).
* Pode gerir as suas contas num local central, o portal do Azure.

Para obter mais informações sobre o software como uma integração de aplicação de serviço (SaaS) com o Azure AD, consulte [início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com no SAP Fiori, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição do Azure AD, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma subscrição no SAP Fiori com início de sessão único ativada.
* No SAP Fiori 7.20 ou posterior é necessário.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste e integrar no SAP Fiori no Azure AD.

No SAP Fiori suporta as seguintes funcionalidades:

* **Iniciado por SP início de sessão único**

## <a name="add-sap-fiori-in-the-azure-portal"></a>Adicionar no SAP Fiori no portal do Azure

Para integrar no SAP Fiori com o Azure AD, tem de adicionar no SAP Fiori à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu da esquerda, selecione **do Azure Active Directory**.

    ![A opção do Azure Active Directory](common/select-azuread.png)

1. Selecione **aplicações empresariais** > **todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

1. Para adicionar uma aplicação, selecione **nova aplicação**.

    ![A nova opção de aplicação](common/add-new-app.png)

1. Na caixa de pesquisa, introduza **no SAP Fiori**. Nos resultados da pesquisa, selecione **no SAP Fiori**e, em seguida, selecione **Add**.

    ![No SAP Fiori na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com no SAP Fiori com base num utilizador de teste com o nome **Eduarda Almeida**. Para início de sessão único funcione, tem de estabelecer uma relação ligada entre um utilizador do Azure AD e o utilizador relacionado no SAP Fiori.

Para configurar e testar o Azure AD início de sessão único no SAP Fiori, tem de concluir os seguintes blocos de construção:

| Tarefa | Descrição |
| --- | --- |
| **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)** | Permite que os utilizadores utilizar esta funcionalidade. |
| **[Configurar no SAP Fiori início de sessão único](#configure-sap-fiori-single-sign-on)** | Configura as definições de início de sessão únicas no aplicativo. |
| **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)** | Testes do Azure AD início de sessão único para um utilizador com o nome Eduarda Almeida. |
| **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)** | Permite a Eduarda Almeida utilizar o Azure AD início de sessão único. |
| **[Criar um utilizador de teste no SAP Fiori](#create-an-sap-fiori-test-user)** | Cria um equivalente da Eduarda Almeida no SAP Fiori, que está ligado a representação do Azure AD do utilizador. |
| **[Testar início de sessão único](#test-single-sign-on)** | Verifica-se de que a configuração funciona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, configura Azure AD início de sessão único com no SAP Fiori no portal do Azure.

1. Abra uma nova janela de browser e inicie sessão no site da sua empresa no SAP Fiori como administrador.

1. Certifique-se de que **http** e **https** serviços estão ativos e que as portas relevantes são atribuídas ao código de transação **SMICM**.

1. Iniciar sessão no cliente do SAP Business do sistema SAP **T01**, em que o início de sessão único é necessário. Em seguida, ative a gestão de sessões de segurança de HTTP.

    1. Aceda ao código da transação **SICF_SESSIONS**. Todos os parâmetros de perfil relevantes com os valores atuais são apresentados. Eles ter um aspeto semelhante ao seguinte exemplo:

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
        > Ajuste os parâmetros com base nos seus requisitos de organização. Os parâmetros anteriores são fornecidos apenas como exemplo.

    1. Se necessário, ajuste os parâmetros no perfil de instância (predefinição) do sistema SAP e reiniciar o sistema SAP.

    1. Clique duas vezes o cliente relevante para ativar uma sessão de segurança HTTP.

        ![A página atual valores de perfil parâmetros relevantes no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. Ative os serviços SICF seguintes:

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Aceda ao código da transação **SAML2** no cliente de negócios para o sistema SAP [**T01/122**]. A configuração de interface do Usuário é aberta numa nova janela do browser. Neste exemplo, utilizamos empresas do cliente para sistema SAP 122.

    ![A página de início de sessão do SAP Fiori empresas do cliente](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Introduza o seu nome de utilizador e palavra-passe e, em seguida, selecione **logon**.

    ![A página de SAML 2.0 configuração de ABAP sistema T01/122 no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. Na **nome do fornecedor** caixa, substitua **T01122** com **http:\//T01122**e, em seguida, selecione **guardar**.

    > [!NOTE]
    > Por predefinição, o nome do fornecedor está no formato \<sid >\<cliente >. O Azure AD espera que o nome no formato \<protocolo > ://\<nome >. Recomendamos que mantenha o nome do fornecedor como https\://\<sid >\<cliente > para que pode configurar vários mecanismos de SAP Fiori ABAP no Azure AD.

    ![O nome de fornecedor atualizado na página de SAML 2.0 configuração de ABAP sistema T01/122 no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Selecione **separador de fornecedor Local** > **metadados**.

1. Na **SAML 2.0 metadados** diálogo caixa, transfira o ficheiro XML de metadados gerados e guarde-o no seu computador.

    ![A ligação de transferir metadados na caixa de diálogo de metadados do SAP SAML 2.0](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. Na [portal do Azure](https://portal.azure.com/), na **no SAP Fiori** painel de integração de aplicações, selecione **início de sessão único**.

    ![A opção de início de sessão única](common/select-sso.png)

1. Na **selecionar um método de início de sessão único** painel, selecione **SAML** ou **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

1. Na **definir a segurança de início de sessão único com o SAML** painel, selecione **editar** (o ícone de lápis) para abrir o **configuração básica de SAML** painel.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Na **configuração básica de SAML** secção, conclua os seguintes passos:

    1. Selecione **carregamento de ficheiro de metadados**.

        ![A opção de ficheiro de metadados de carregamento](common/upload-metadata.png)

   1. Para selecionar o ficheiro de metadados, selecione o ícone de pasta e, em seguida, selecione **carregar**.

       ![Selecione o ficheiro de metadados e, em seguida, selecione o botão de carregamento](common/browse-upload-metadata.png)

1. Quando o ficheiro de metadados é carregado com êxito, o **identificador** e **URL de resposta** valores são preenchidos automaticamente na **configuração básica de SAML** painel. Na **iniciar sessão no URL** , introduza um URL com o seguinte padrão: https:\//\<sua instância de empresa do SAP Fiori\>.

    ![Informações de início de sessão de único SAP Fiori domínio e URLs](common/sp-identifier-reply.png)

    > [!NOTE]
    > Alguns erros de relatório de clientes relacionados com o configurado incorretamente **URL de resposta** valores. Se vir este erro, pode utilizar o seguinte script do PowerShell para definir o URL de resposta correta para a sua instância:
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > Pode definir o `ServicePrincipal` ID de objeto por conta própria antes de executar o script, ou pode passá-lo aqui.

1. O aplicativo no SAP Fiori espera que as asserções SAML para estar num formato específico. Configure as seguintes declarações para esta aplicação. Para gerir estes valores de atributo, o **definido no início de sessão único com o SAML** painel, selecione **editar**.

    ![O painel de atributos de utilizador](common/edit-attribute.png)

1. Na **atributos de utilizador e afirmações** painel, configure os atributos de token SAML, conforme mostrado na imagem anterior. Em seguida, conclua os seguintes passos:

    1. Selecione **edite** para abrir o **afirmações de utilizador de gerir** painel.

    1. Na **transformação** lista, selecione **ExtractMailPrefix()** .

    1. Na **parâmetro 1** lista, selecione **user.userprinicipalname**.

    1. Selecione **Guardar**.

       ![O painel de afirmações de utilizador de gerir](./media/sapfiori-tutorial/nameidattribute.png)

       ![A secção de transformação no painel de afirmações de utilizador de gerir](./media/sapfiori-tutorial/nameidattribute1.png)


1. Na **definir a segurança de início de sessão único com o SAML** painel, na **certificado de assinatura SAML** secção, selecione **transferir** junto a **XML de metadados de Federação**. Selecione uma opção de download com base nos seus requisitos. Guarde o certificado no seu computador.

    ![A opção de download de certificado](common/metadataxml.png)

1. Na **configurado no SAP Fiori** secção, copie os seguintes URLs com base nos seus requisitos:

    * URL de início de sessão
    * Identificador do Azure AD
    * URL de fim de sessão

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-sap-fiori-single-sign-on"></a>Configurar no SAP Fiori início de sessão único

1. Inicie sessão no sistema SAP e aceda ao código da transação **SAML2**. Uma nova janela do browser abre-se com a página de configuração de SAML.

1. Para configurar pontos de extremidade de um fornecedor de identidade fidedignos (Azure AD), selecione o **fornecedores fidedignos** separador.

    ![O separador de fornecedores fidedignos no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. Selecione **Add**e, em seguida, selecione **carregar o ficheiro de metadados** no menu de contexto.

    ![As opções Add e carregar o ficheiro de metadados no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Carregue o ficheiro de metadados que transferiu no portal do Azure. Selecione **Seguinte**.

    ![Selecione o ficheiro de metadados para carregar no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. Na página seguinte, no **Alias** , introduza o nome de alias. Por exemplo, **aadsts**. Selecione **Seguinte**.

    ![A caixa de Alias no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Certifique-se de que o valor a **algoritmo de texto implícita** caixa está **SHA-256**. Selecione **Seguinte**.

    ![Verifique se o valor de algoritmo de resumo no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. Sob **pontos finais de início de sessão único**, selecione **HTTP POST**e, em seguida, selecione **seguinte**.

    ![Opções de pontos finais de início de sessão único no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. Sob **pontos de extremidade de fim de sessão único**, selecione **redirecionamento de HTTP**e, em seguida, selecione **seguinte**.

    ![Opções de pontos finais de fim de sessão único no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. Sob **pontos de extremidade do artefacto**, selecione **próxima** para continuar.

    ![Opções de pontos finais de artefacto no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. Sob **requisitos de autenticação**, selecione **concluir**.

    ![Opções de requisitos de autenticação e a opção de concluir no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. Selecione **Trusted Provider** > **Federação de identidades** (na parte inferior da página). Selecione **Editar**.

    ![Os separadores de fornecedor confiável e Federação de identidades no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. Selecione **Adicionar**.

    ![A opção de adicionar no separador de Federação de identidades](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. Na **suportado NameID formatos** caixa de diálogo, selecione **não especificado**. Selecione **OK**.

    ![As opções no SAP e a caixa de diálogo suportado NameID formatos](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    Os valores para **origem do ID de utilizador** e **modo de mapeamento de ID de utilizador** determinar a ligação entre o utilizador SAP e a declaração do Azure AD.  

    **Cenário 1**: Utilizador SAP para o mapeamento de utilizador do Azure AD

    1. No SAP, sob **detalhes do formato NameID "Não especificado"** , tenha em atenção os detalhes:

        ![A caixa de diálogo "Não especificado" Detalhes do formato NameID caixa no SAP](./media/sapfiori-tutorial/nameiddetails.png)

    1. No portal do Azure, em **atributos de utilizador e afirmações**, tenha em atenção as declarações necessárias do Azure AD.

        ![A caixa de diálogo de atributos de utilizador e afirmações no portal do Azure](./media/sapfiori-tutorial/claimsaad1.png)

    **Cenário 2**: Selecione o ID de utilizador SAP com base no endereço de e-mail configurado na SU01. Neste caso, o ID de e-mail deve ser configurado no SU01 para cada usuário que requer o SSO.

    1.  No SAP, sob **detalhes do formato NameID "Não especificado"** , tenha em atenção os detalhes:

        ![A caixa de diálogo "Não especificado" Detalhes do formato NameID caixa no SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. No portal do Azure, em **atributos de utilizador e afirmações**, tenha em atenção as declarações necessárias do Azure AD.

       ![A caixa de diálogo de atributos de utilizador e afirmações no portal do Azure](./media/sapfiori-tutorial/claimsaad2.png)

1. Selecione **salvar**e, em seguida, selecione **ativar** para permitir que o fornecedor de identidade.

    ![A guardar e ativar as opções no SAP](./media/sapfiori-tutorial/configuration1.png)

1. Selecione **OK** quando lhe for pedido.

    ![A opção OK na caixa de diálogo de configuração do SAML 2.0 no SAP](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, vai criar um utilizador de teste com o nome Eduarda Almeida no portal do Azure.

1. No portal do Azure, selecione **do Azure Active Directory** > **utilizadores** > **todos os utilizadores**.

    ![Os utilizadores e todas as opções de utilizadores](common/users.png)

1. Selecione **novo utilizador**.

    ![A nova opção de utilizador](common/new-user.png)

1. Na **utilizador** painel, conclua os seguintes passos:

    1. Na **Name** , introduza **BrittaSimon**.
  
    1. Na **nome de utilizador** , introduza **brittasimon\@\<seu domínio da empresa >.\< extensão >** . Por exemplo, **brittasimon\@contoso.com**.

    1. Selecione o **palavra-passe de Show** caixa de verificação. Anote o valor que é apresentado no **palavra-passe** caixa.

    1. Selecione **Criar**.

    ![O painel do utilizador](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, concede acesso de Eduarda Almeida a no SAP Fiori, de modo que pode utilizar o Azure início de sessão único.

1. No portal do Azure, selecione **aplicações empresariais** > **todas as aplicações** > **no SAP Fiori**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

1. Na lista de aplicações, selecione **no SAP Fiori**.

    ![No SAP Fiori na lista de aplicações](common/all-applications.png)

1. No menu, selecione **utilizadores e grupos**.

    ![A opção de utilizadores e grupos](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**. Em seguida, na **adicionar atribuição** painel, selecione **utilizadores e grupos**.

    ![O painel de atribuição de adicionar](common/add-assign-user.png)

1. Na **utilizadores e grupos** painel, selecione **Eduarda Almeida** na lista de utilizadores. Escolha **Selecionar**.

1. Se estiver à espera um valor de função na asserção de SAML, no **selecionar função** painel, selecione a função relevante para o utilizador a partir da lista. Escolha **Selecionar**.

1. Na **adicionar atribuição** painel, selecione **atribuir**.

### <a name="create-an-sap-fiori-test-user"></a>Criar um utilizador de teste no SAP Fiori

Nesta secção, vai criar um utilizador com o nome Eduarda Almeida no SAP Fiori. Trabalhar com a sua equipa SAP interna de especialistas ou seu parceiro SAP da organização para adicionar o utilizador na plataforma no SAP Fiori.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

1. Depois do fornecedor de identidade do Azure AD é ativado no SAP Fiori, tentam aceder a um dos seguintes URLs para testar o início de sessão único (não deve ser-lhe pedido para um nome de utilizador e palavra-passe):

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > Substitua *sapurl* com o nome de anfitrião real do SAP.

1. O URL de teste deverá demorar para a seguinte página de aplicativo de teste no SAP. Se a página é aberta, do Azure AD início de sessão único está configurado com êxito.

    ![Página de aplicativo de teste de padrão no SAP](./media/sapfiori-tutorial/testingsso.png)

1. Se lhe for pedido um nome de utilizador e palavra-passe, ative o rastreio para o ajudar a diagnosticar o problema. Utilize o seguinte URL para o rastreio: https:\//\<sapurl\>/sap/bc/webdynpro/sap/sec_diag_tool? sap cliente = 122 e de idioma sap = EN #.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte estes artigos:

- [Lista de tutoriais de integração de aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
