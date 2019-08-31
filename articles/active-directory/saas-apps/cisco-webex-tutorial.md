---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com reuniões do Cisco WebEx | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e as reuniões do Cisco WebEx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 141d3062f231e198b3587efcdf4ae6e9a1c97ed6
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70162667"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com reuniões do Cisco WebEx

Neste tutorial, você aprenderá a integrar as reuniões do Cisco WebEx com o Azure Active Directory (Azure AD). Ao integrar reuniões do Cisco WebEx com o Azure AD, você pode:

* Controle no Azure AD quem tem acesso às reuniões do Cisco WebEx.
* Habilite seus usuários a entrar automaticamente em reuniões do Cisco WebEx com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) de reuniões do Cisco WebEx.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* As reuniões do Cisco WebEx oferecem suporte ao **SP e** ao SSO iniciado pelo IDP

* As reuniões do Cisco WebEx dão suporte ao provisionamento **de usuário just in time**

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Adicionando reuniões do Cisco WebEx da Galeria

Para configurar a integração das reuniões do Cisco WebEx com o Azure AD, você precisa adicionar reuniões do Cisco WebEx da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite reuniões do **Cisco WebEx** na caixa de pesquisa.
1. Selecione **reuniões do Cisco WebEx** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex-meetings"></a>Configurar e testar o logon único do Azure AD para reuniões do Cisco WebEx

Configure e teste o SSO do Azure AD com reuniões do Cisco WebEx usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em reuniões do Cisco WebEx.

Para configurar e testar o SSO do Azure AD com reuniões do Cisco WebEx, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
2. **[Configurar o SSO de reuniões do Cisco WebEx](#configure-cisco-webex-meetings-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste de reuniões do Cisco WebEx](#create-cisco-webex-meetings-test-user)** – para ter um equivalente de B. Simon em reuniões do Cisco WebEx que esteja vinculado à representação do usuário no Azure AD.
3. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Cisco WebEx encontros** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , você pode configurar o aplicativo no modo iniciado pelo **IDP** carregando o arquivo de metadados do provedor de **Serviços** da seguinte maneira:

    a. Clique em **carregamento de ficheiro de metadados**.

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    c. Após a conclusão bem-sucedida do carregamento do arquivo de metadados do provedor de serviços, os valores do **identificador** e da **URL de resposta** serão preenchidos automaticamente na seção **configuração básica do SAML** .

    >[!Note]
    >Você obterá o arquivo de metadados do provedor de serviços da seção **Configurar o SSO de reuniões do Cisco WebEx** , que é explicada posteriormente no tutorial. 

1. Se desejar configurar o aplicativo no modo iniciado pelo **SP** , execute as seguintes etapas:  

    a. Na seção **configuração básica do SAML** , clique no ícone Editar/caneta.

   ![Editar configuração básica de SAML](common/edit-urls.png)
    
    b. Na caixa de texto **URL de logon** , digite a URL usando o seguinte padrão:` https://<customername>.my.webex.com`

5. O aplicativo de reuniões Cisco WebEx espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo atributos de usuário.

    ![image](common/edit-attribute.png)

6. Além de acima, o aplicativo de reuniões do Cisco WebEx espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção declarações do usuário, na caixa de diálogo atributos de usuário, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo: 

    | Name | Atributo de origem|
    | ---------------|  --------- |
    |   FirstName    | user.givenname |
    |   LastName    | User. sobrenome |
    |   email       | user.mail |
    |   UID    | user.mail |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , selecione o valor do atributo mostrado para essa linha na lista suspensa.

    f. Clique em **Guardar**.

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Na seção **Configurar reuniões do Cisco WebEx** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso às reuniões do Cisco WebEx.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **reuniões do Cisco WebEx**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-cisco-webex-meetings-sso"></a>Configurar SSO de reuniões do Cisco WebEx

1. Acesse `https://<customername>.webex.com/admin` a URL com suas credenciais de administração.

2. Vá para **configurações de site comuns** e navegue até **configuração de SSO**.
 
    ![Configurar o início de sessão único](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

3. Na página de **Administração do WebEx** , execute as seguintes etapas:

    ![Configurar o início de sessão único](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

    a. Selecione **SAML 2,0** como **protocolo de Federação**.

    b. Clique no link **importar metadados SAML** para carregar o arquivo de metadados, que você baixou do portal do Azure.

    c. Clique no botão **Exportar** para baixar o arquivo de metadados do provedor de serviços e carregá-lo na seção **configuração básica do SAML** em portal do Azure.

    d. Na caixa de texto **AuthContextClassRef** , `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` digite e, se você quiser habilitar a MFA usando o Azure AD, digite os dois valores como`urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

    e. Selecione **criação automática de conta**.

    >[!NOTE]
    >Para habilitar o provisionamento de usuário **just-in-time** , você precisa verificar a **criação automática da conta**. Além disso, os atributos do token SAML precisam ser passados na resposta SAML.

    f. Clique em **Guardar**.

    >[!NOTE]
    >Essa configuração destina-se apenas aos clientes que usam o ID de email WebEx no formato de e-mail.

### <a name="create-cisco-webex-meetings-test-user"></a>Criar usuário de teste de reuniões do Cisco WebEx

O objetivo desta seção é criar um usuário chamado B. Simon em reuniões do Cisco WebEx. As reuniões do Cisco WebEx dão suporte ao provisionamento **just-in-time** , que está habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir em reuniões do Cisco WebEx, um novo será criado quando você tentar acessar reuniões do Cisco WebEx.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco reuniões do Cisco WebEx no painel de acesso, você deverá ser conectado automaticamente às reuniões do Cisco WebEx para as quais você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o ServiceNow com o Azure AD](https://aad.portal.azure.com)
