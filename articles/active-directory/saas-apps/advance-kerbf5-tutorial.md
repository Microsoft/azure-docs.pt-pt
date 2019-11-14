---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com F5 | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9c5fb47a-1c5d-437a-b4c1-dbf739eaf5e3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 984fd0c7946a50922315269c87e08b1c35b74348
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074763"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com F5

Neste tutorial, você aprenderá a integrar o F5 ao Azure Active Directory (Azure AD). Ao integrar o F5 ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao F5.
* Habilite seus usuários a serem automaticamente conectados ao F5 com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* F5 assinatura habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O F5 dá suporte a **SP** iniciado pelo
* O SSO do F5 pode ser configurado de três maneiras diferentes.

- [Configurar o logon único F5 para o aplicativo Kerberos avançado](#configure-f5-single-sign-on-for-advanced-kerberos-application)

- [Configurar o logon único F5 para o aplicativo baseado em cabeçalho](headerf5-tutorial.md)

- [Configurar o logon único F5 para o aplicativo Kerberos](kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>Adicionando F5 da Galeria

Para configurar a integração do F5 ao Azure AD, você precisa adicionar o F5 da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **F5** na caixa de pesquisa.
1. Selecione **F5** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Configurar e testar o logon único do Azure AD para o F5

Configure e teste o SSO do Azure AD com F5 usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em F5.

Para configurar e testar o SSO do Azure AD com o F5, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar F5-SSO](#configure-f5-sso)** -para configurar as configurações de logon único no lado do aplicativo.
    1. **[Create F5 Test User](#create-f5-test-user)** – para ter um equivalente de B. Simon em F5 que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos **F5** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Contate a [equipe de suporte ao cliente do F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar F5** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao F5.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **F5**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-f5-sso"></a>Configurar o SSO do F5

- [Configurar o logon único F5 para o aplicativo baseado em cabeçalho](headerf5-tutorial.md)

- [Configurar o logon único F5 para o aplicativo Kerberos](kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-advanced-kerberos-application"></a>Configurar o logon único F5 para o aplicativo Kerberos avançado

1. Abra uma nova janela do navegador da Web e entre no site da sua empresa do F5 (avançado Kerberos) como administrador e execute as seguintes etapas:

1. Você precisa importar o certificado de metadados para o F5 (Kerberos avançado) que será usado posteriormente no processo de instalação. Vá para **sistema > gerenciamento de certificados > tráfego gerenciamento de certificado > > lista de certificados SSL**. Clique em **importar** do canto direito.

    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure01.png)
 
1. Para configurar o IDP do SAML, acesse **acessar > federação > provedor de serviços SAML > criar > de metadados**.

    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure02.png)

    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure03.png)
 
    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure04.png)

    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure05.png)
 
1. Especificar o certificado carregado da tarefa 3

    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure06.png)

    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure07.png)

 1. Para configurar o SP do SAML, acesse **acessar > federação > Federação do serviço SAML > serviços do SP Local > criar**.

    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure08.png)
 
1. Clique em **OK**.

1. Selecione a configuração do SP e clique em **associar/desassociar conectores IDP**.

     ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure09.png)
 
 
1. Clique em **Adicionar nova linha** e selecione o **conector IDP externo** criado na etapa anterior.

    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure10.png)
 
1. Para configurar o SSO do Kerberos, **acesse > logon único > Kerberos**

    >[!Note]
    >Você precisará da conta de delegação Kerberos a ser criada e especificada. Consulte a seção KCD (consulte o apêndice para referências de variáveis)

    • Fonte de nome de usuário `session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    • Origem do realm do usuário `session.logon.last.domain`

    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure11.png)

1. Para configurar o perfil de acesso, **acesse perfis de >/políticas > perfil de acesso (por políticas de sessão)** .

    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure12.png)

    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure13.png)

    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure14.png)

    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure15.png)

    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure16.png)
 
    * sessão. logon. Last. usernameUPN expr {[mcget {Session. SAML. Last. Identity}]}

    * sessão. ad. lastactualdomain de texto superdemo. Live

    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure17.png)

    * (userPrincipalName =% {Session. logon. Last. usernameUPN})

    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure18.png)

    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure19.png)

    * Session. logon. Last. username expr {"[mcget {Session. ad. Last. attr. sAMAccountName}]"}

    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure20.png)

    * mcget {Session. logon. Last. username}
    * mcget {Session. logon. Last. Password}

1. Para adicionar um novo nó, vá para **tráfego Local > nós > lista de nós > +** .

    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure21.png)
 
1. Para criar um novo pool, vá para **tráfego Local > pools > lista de pools > criar**.

     ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure22.png)

 1. Para criar um novo servidor virtual, vá para **tráfego Local > servidores virtuais > lista de servidores virtuais > +** .

    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure23.png)

1. Especifique o perfil de acesso criado na etapa anterior.

    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure24.png) 

### <a name="setting-up-kerberos-delegation"></a>Configurando a delegação Kerberos 

>[!Note]
>Para obter mais detalhes, consulte [aqui](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

* **Etapa 1: criar uma conta de delegação**

    * Exemplo
    ```
    Domain Name : superdemo.live
    Sam Account Name : big-ipuser

    New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")
    ```

* **Etapa 2: definir o SPN (na conta de delegação do APM)**

    *  Exemplo
    ```
    setspn –A host/big-ipuser.superdemo.live big-ipuser
    ```

* **Etapa 3: Delegação de SPN (para a conta de serviço de aplicativo)**

    * Configure a delegação apropriada para a conta de delegação F5.
    * No exemplo a seguir, a conta de delegação do APM está sendo configurada para KCD para o aplicativo FRP-App1. superdemo. Live.

        ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure25.png)

1. Forneça os detalhes conforme mencionado no documento de referência acima abaixo [deste](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

1. Apêndice-SAML – F5 mapeamentos de variáveis BIG-IP mostrados abaixo:

    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure26.png)

    ![Configuração de F5 (Kerberos avançado)](./media/advance-kerbf5-tutorial/configure27.png) 

1. Abaixo está a lista completa de atributos padrão do SAML. O determinadoname é representado usando a cadeia de caracteres a seguir.
`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

| | |
| -- | -- |
| eb46b6b6. Session. SAML. Last. AssertionId | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. assertionIssueInstant  | `<ID>` |
| eb46b6b6. Session. SAML. Last. assertionIssuer | `https://sts.windows.net/<TENANT ID>`/ |
| eb46b6b6. Session. SAML. Last. attr. Name. http:\//schemas.microsoft.com/claims/authnmethodsreferences | `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` |
| eb46b6b6. Session. SAML. Last. attr. Name. http:\//schemas.microsoft.com/identity/claims/displayname | user0 |
| eb46b6b6. Session. SAML. Last. attr. Name. http:\//schemas.microsoft.com/identity/claims/identityprovider | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6. Session. SAML. Last. attr. Name. http:\//schemas.microsoft.com/identity/claims/objectidentifier | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. attr. Name. http:\//schemas.microsoft.com/identity/claims/tenantid | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. attr. Name. http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress | `user0@superdemo.live` |
| eb46b6b6. Session. SAML. Last. attr. Name. http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname | user0 |
| eb46b6b6. Session. SAML. Last. attr. Name. http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/name | `user0@superdemo.live` |
| eb46b6b6. Session. SAML. Last. attr. Name. http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/surname | 0 |
| eb46b6b6. Session. SAML. Last. Audience | `https://kerbapp.superdemo.live` |
| eb46b6b6. Session. SAML. Last. authNContextClassRef | urn: Oasis: names: TC: SAML: 2.0: AC: classes: senha |
| eb46b6b6. Session. SAML. Last. authNInstant | `<ID>` |
| eb46b6b6. Session. SAML. Last. Identity | `user0@superdemo.live` |
| eb46b6b6. Session. SAML. Last. inresponseto | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. nameIDValue | `user0@superdemo.live` |
| eb46b6b6. Session. SAML. Last. nameIdFormat | urn: Oasis: names: TC: SAML: 1.1: NameID-Format: emailAddress |
| eb46b6b6. Session. SAML. Last. responseDestination | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6. Session. SAML. Last. responseid | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. responseIssueInstant | `<ID>` |
| eb46b6b6. Session. SAML. Last. responseIssuer | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6. Session. SAML. Last. Result | 1 |
| eb46b6b6. Session. SAML. Last. samlVersion | 2.0 |
| eb46b6b6. Session. SAML. Last. sessionIndex | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. statusvalue | urn: Oasis: names: TC: SAML: 2.0: status: êxito |
| eb46b6b6. Session. SAML. Last. subjectConfirmDataNotOnOrAfter | `<ID>` |
| eb46b6b6. Session. SAML. Last. subjectConfirmDataRecipient | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6. Session. SAML. Last. subjectConfirmMethod | urn: Oasis: names: TC: SAML: 2.0: cm: portador |
| eb46b6b6. Session. SAML. Last. validityNotBefore | `<ID>` |
| eb46b6b6. Session. SAML. Last. validityNotOnOrAfter | `<ID>` |

### <a name="create-f5-test-user"></a>Criar usuário de teste do F5

Nesta seção, você criará um usuário chamado B. Simon em F5. Trabalhe com a [equipe de suporte ao cliente F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para adicionar os usuários à plataforma F5. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco F5 no painel de acesso, você deverá ser conectado automaticamente ao F5 para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o F5 com o Azure AD](https://aad.portal.azure.com/)

- [Configurar o logon único F5 para o aplicativo baseado em cabeçalho](headerf5-tutorial.md)

- [Configurar o logon único F5 para o aplicativo Kerberos](kerbf5-tutorial.md)

