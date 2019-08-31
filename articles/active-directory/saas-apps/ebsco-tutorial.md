---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com EBSCO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o EBSCO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ebaf3a1d877025cafe8829bc937ef032a3c95d03
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163464"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ebsco"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com EBSCO

Neste tutorial, você aprenderá a integrar o EBSCO com o Azure Active Directory (Azure AD). Ao integrar o EBSCO ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao EBSCO.
* Habilite seus usuários a serem conectados automaticamente ao EBSCO com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do EBSCO.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O EBSCO dá suporte ao **SP e** ao SSO iniciado pelo IDP
* O EBSCO dá suporte ao provisionamento **de usuário just in time**

> [!NOTE]
> Como o identificador desse aplicativo é um valor de cadeia de caracteres fixo, apenas uma instância pode ser configurada em um locatário.

## <a name="adding-ebsco-from-the-gallery"></a>Adicionando o EBSCO da Galeria

Para configurar a integração do EBSCO ao Azure AD, você precisará adicionar o EBSCO da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **EBSCO** na caixa de pesquisa.
1. Selecione **EBSCO** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ebsco"></a>Configurar e testar o logon único do Azure AD para o EBSCO

Configure e teste o SSO do Azure AD com o EBSCO usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no EBSCO.

Para configurar e testar o SSO do Azure AD com o EBSCO, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do EBSCO](#configure-ebsco-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do EBSCO](#create-ebsco-test-user)** – para ter um equivalente de B. Simon em EBSCO que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **EBSCO** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    Na caixa de texto **identificador** , digite uma URL:`pingsso.ebscohost.com`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize o valor com a URL de logon real. Contate a [equipe de suporte ao cliente do EBSCO](mailto:support@ebsco.com) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

    o **elementos exclusivos:**  

    o **CustID** = inserir ID de cliente EBSCO exclusivo 

    o **Profile** = os clientes podem personalizar o link para direcionar os usuários para um perfil específico (dependendo do que eles compram do EBSCO). Eles podem inserir uma ID de perfil específica. As principais IDs são EDS (serviço de descoberta EBSCO) e ehost (bancos de dados bancos). As instruções para o mesmo são fornecidas [aqui](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar EBSCO** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao EBSCO.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **EBSCO**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-ebsco-sso"></a>Configurar o SSO do EBSCO

Para configurar o logon único no lado do **EBSCO** , é necessário enviar o XML de **metadados de Federação** baixado e as URLs copiadas apropriadas de portal do Azure para a equipe de suporte do [EBSCO](mailto:support@ebsco.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-ebsco-test-user"></a>Criar usuário de teste do EBSCO

No caso do EBSCO, o provisionamento do usuário é automático.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

O Azure AD passa os dados necessários para o aplicativo EBSCO. O provisionamento de usuário do EBSCO pode ser automático ou exigir um formulário único. Depende se o cliente tem muitas contas de EBSCOhost preexistentes com configurações pessoais salvas. O mesmo pode ser discutido com a [equipe de suporte do EBSCO](mailto:support@ebsco.com) durante a implementação. De qualquer forma, o cliente não precisa criar contas EBSCOhost antes do teste.

   >[!Note]
   >Você pode automatizar o provisionamento/personalização de usuários do EBSCOhost. Contate a [equipe de suporte do EBSCO](mailto:support@ebsco.com) sobre o provisionamento de usuário just-in-time.

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

1. Ao clicar no bloco do EBSCO no painel de acesso, você deverá ser conectado automaticamente ao seu aplicativo EBSCO.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

1. Depois de fazer logon no aplicativo, clique no botão **entrar** no canto superior direito.

    ![A entrada EBSCO na lista de aplicativos](./media/ebsco-tutorial/tutorial_ebsco_signin.png)

1. Você receberá uma solicitação única para emparelhar o logon institucional/SAML com um **link de sua conta do MyEBSCOhost existente para sua conta de instituição agora** ou **criar uma nova conta do MyEBSCOhost e vinculá-la à sua conta da instituição**. A conta é usada para personalização no aplicativo EBSCOhost. Selecione a opção **criar uma nova conta** e você verá que o formulário para personalização é previamente concluído com os valores da resposta SAML, conforme mostrado na captura de tela abaixo. Clique em **' continuar '** para salvar esta seleção.
    
     ![O usuário EBSCO na lista de aplicativos](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. Depois de concluir a configuração acima, limpe cookies/cache e faça logon novamente. Você não precisará entrar manualmente novamente e as configurações de personalização serão lembradas.

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o EBSCO com o Azure AD](https://aad.portal.azure.com/)

