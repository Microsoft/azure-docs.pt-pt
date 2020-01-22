---
title: 'Tutorial: integração do Azure Active Directory com a autenticação de identidade da SAP Cloud Platform | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e a autenticação de identidade da SAP Cloud Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95be73bd125c124409585a478fa9707e7b6a2ac2
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289072"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-platform-identity-authentication"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com a autenticação de identidade da SAP Cloud Platform

Neste tutorial, você aprenderá a integrar a autenticação de identidade da SAP Cloud Platform com o Azure Active Directory (Azure AD). Ao integrar a autenticação de identidade da SAP Cloud Platform ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso à autenticação de identidade da SAP Cloud Platform.
* Habilite seus usuários a serem conectados automaticamente à autenticação de identidade da SAP Cloud Platform com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) da autenticação de identidade da SAP Cloud Platform.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* A autenticação de identidade da SAP Cloud Platform dá suporte ao **SP** e ao SSO iniciado pelo **IDP**
* Depois de configurar a autenticação de identidade da SAP Cloud Platform, você pode impor controles de sessão, que protegem vazamento e pós-infiltração dos dados confidenciais de sua organização em tempo real. Os controles de sessão se estendem do acesso condicional. [Saiba como impor o controle de sessão com Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

Antes de se aprofundar nos detalhes técnicos, é vital entender os conceitos que serão examinados. O Serviços de Federação do Active Directory (AD FS) e a autenticação de identidade da SAP Cloud Platform permitem que você implemente o SSO em aplicativos ou serviços protegidos pelo AD do Azure (como um IdP) com aplicativos e serviços SAP protegidos pela SAP Cloud Autenticação de identidade da plataforma.

Atualmente, a autenticação de identidade da SAP Cloud Platform atua como um provedor de identidade de proxy para aplicativos SAP. Azure Active Directory, por sua vez, atua como o principal provedor de identidade nesta configuração. 

O diagrama a seguir ilustra essa relação:

![Criar um utilizador de teste do Azure AD](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Com essa configuração, seu locatário de autenticação de identidade da SAP Cloud Platform é configurado como um aplicativo confiável no Azure Active Directory.

Todos os aplicativos e serviços SAP que você deseja proteger dessa maneira são posteriormente configurados no console de gerenciamento de autenticação de identidade da SAP Cloud Platform.

Portanto, a autorização para conceder acesso a aplicativos e serviços SAP precisa ocorrer na autenticação de identidade da SAP Cloud Platform (em vez de Azure Active Directory).

Ao configurar a autenticação de identidade da SAP Cloud Platform como um aplicativo por meio do Azure Active Directory Marketplace, você não precisa configurar declarações individuais ou asserções SAML.

> [!NOTE]
> Atualmente, somente o SSO da Web foi testado por ambas as partes. Os fluxos necessários para a comunicação de aplicativo para API ou API para API devem funcionar, mas ainda não foram testados. Eles serão testados durante as atividades subsequentes.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Adicionando a autenticação de identidade da SAP Cloud Platform da Galeria

Para configurar a integração da autenticação de identidade da SAP Cloud Platform ao Azure AD, você precisa adicionar a autenticação de identidade da SAP Cloud Platform da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite autenticação de **identidade da SAP Cloud Platform** na caixa de pesquisa.
1. Selecione **autenticação de identidade da SAP Cloud Platform** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-platform-identity-authentication"></a>Configurar e testar o logon único do Azure AD para autenticação de identidade da SAP Cloud Platform

Configure e teste o SSO do Azure AD com a autenticação de identidade da SAP Cloud Platform usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado na autenticação de identidade da SAP Cloud Platform.

Para configurar e testar o SSO do Azure AD com a autenticação de identidade da SAP Cloud Platform, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar SSO de autenticação de identidade da SAP Cloud Platform](#configure-sap-cloud-platform-identity-authentication-sso)** -para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste de autenticação de identidade da SAP Cloud Platform](#create-sap-cloud-platform-identity-authentication-test-user)** – para ter um equivalente de B. Simon na autenticação de identidade da SAP Cloud Platform vinculada à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos de **autenticação de identidade da SAP Cloud Platform** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , se você quiser configurar o no modo iniciado pelo **IDP**, execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs da autenticação de identidade da SAP Cloud Platform](common/idp-intiated.png)

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `<IAS-tenant-id>.accounts.ondemand.com`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador e a URL de resposta reais. Contate a [equipe de suporte ao cliente de autenticação de identidade da SAP Cloud Platform](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) para obter esses valores. Se você não entender o valor do identificador, leia a documentação de autenticação de identidade da SAP Cloud Platform sobre a [configuração do SAML 2,0 do locatário](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado por **SP**:

    ![Informações de logon único de domínio e URLs da autenticação de identidade da SAP Cloud Platform](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Esse valor não é real. Atualize esse valor com a URL de logon real. Use a URL de logon do aplicativo de negócios específica. Entre em contato com a [equipe de suporte ao cliente de autenticação de identidade da SAP Cloud Platform](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) se tiver alguma dúvida.

1. O aplicativo de autenticação de identidade da SAP Cloud Platform espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/default-attributes.png)

1. Além de acima, o aplicativo de autenticação de identidade da SAP Cloud Platform espera que mais atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | firstName | user.givenname |

8. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **XML de metadados** das opções especificadas de acordo com seu requisito e salve-o em seu computador.

    ![O link de download de certificado](common/metadataxml.png)

9. Na seção **Configurar a autenticação de identidade da SAP Cloud Platform** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso à autenticação de identidade da SAP Cloud Platform.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **autenticação de identidade da SAP Cloud Platform**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-sap-cloud-platform-identity-authentication-sso"></a>Configurar SSO de autenticação de identidade da SAP Cloud Platform

1. Para que o SSO seja configurado para seu aplicativo, vá para o console de administração de autenticação de identidade da SAP Cloud Platform. A URL tem o seguinte padrão: `https://<tenant-id>.accounts.ondemand.com/admin`. Em seguida, leia a documentação sobre autenticação de identidade da SAP Cloud Platform em [integração com o Microsoft Azure ad](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html).

2. No portal do Azure, selecione o botão **salvar** .

3. Continue com o seguinte somente se você quiser adicionar e habilitar o SSO para outro aplicativo SAP. Repita as etapas na seção **adicionando autenticação de identidade da SAP Cloud Platform da Galeria**.

4. Na portal do Azure, na página de integração de aplicativos de **autenticação de identidade da SAP Cloud Platform** , selecione **logon vinculado**.

    ![Configurar logon vinculado](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Guarde a configuração.

> [!NOTE]
> O novo aplicativo aproveita a configuração de logon único do aplicativo SAP anterior. Certifique-se de usar os mesmos provedores de identidade corporativa no console de administração de autenticação de identidade da SAP Cloud Platform.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Criar usuário de teste de autenticação de identidade da SAP Cloud Platform

Você não precisa criar um usuário na autenticação de identidade da SAP Cloud Platform. Os usuários que estão no armazenamento de usuários do Azure AD podem usar a funcionalidade SSO.

A autenticação de identidade da SAP Cloud Platform dá suporte à opção de Federação de identidade. Essa opção permite que o aplicativo Verifique se os usuários autenticados pelo provedor de identidade corporativa existem no repositório de usuários da autenticação de identidade da SAP Cloud Platform.

A opção de Federação de identidade está desabilitada por padrão. Se a Federação de identidades estiver habilitada, somente os usuários que forem importados na autenticação de identidade da SAP Cloud Platform poderão acessar o aplicativo.

Para obter mais informações sobre como habilitar ou desabilitar a Federação de identidades com a autenticação de identidade da SAP Cloud Platform, consulte "Habilitar Federação de identidade com a autenticação de identidade da SAP Cloud Platform" em [Configurar Federação de identidade com o repositório de usuário da autenticação de identidade da SAP Cloud Platform](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco de autenticação de identidade da SAP Cloud Platform no painel de acesso, você deverá ser conectado automaticamente à autenticação de identidade da SAP Cloud Platform para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a autenticação de identidade da SAP Cloud Platform com o Azure AD](https://aad.portal.azure.com/)

- [O que é o controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a autenticação de identidade da SAP Cloud Platform com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
