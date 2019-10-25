---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o iPass SmartConnect | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o iPass SmartConnect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dee6d039-f9bb-49a2-a408-5ed40ef17d9f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15a84504efc39b901120c6584634950809f0c306
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790973"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ipass-smartconnect"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o iPass SmartConnect

Neste tutorial, você aprenderá a integrar o iPass SmartConnect ao Azure Active Directory (Azure AD). Ao integrar o iPass SmartConnect ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao iPass SmartConnect.
* Habilite seus usuários a serem conectados automaticamente ao iPass SmartConnect com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* assinatura habilitada para SSO (logon único) do iPass SmartConnect.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* iPass SmartConnect dá suporte ao **SP e** ao SSO iniciado pelo IDP
* o iPass SmartConnect dá suporte ao provisionamento **de usuário just in time**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>Adicionando o iPass SmartConnect da Galeria

Para configurar a integração do iPass SmartConnect ao Azure AD, você precisará adicionar o iPass SmartConnect da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **iPass SmartConnect** na caixa de pesquisa.
1. Selecione **IPass SmartConnect** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ipass-smartconnect"></a>Configurar e testar o logon único do Azure AD para iPass SmartConnect

Configure e teste o SSO do Azure AD com o iPass SmartConnect usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no iPass SmartConnect.

Para configurar e testar o SSO do Azure AD com o iPass SmartConnect, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do IPass SmartConnect](#configure-ipass-smartconnect-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do IPass SmartConnect](#create-ipass-smartconnect-test-user)** – para ter um equivalente de B. Simon no iPass SmartConnect que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **iPass SmartConnect** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , o usuário não precisará executar nenhuma etapa, pois o aplicativo já está previamente integrado ao Azure.

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL: `https://om-activation.ipass.com/ClientActivation/ssolanding.go`

1. Clique em **Guardar**.

1. o aplicativo iPass SmartConnect espera que as asserções SAML estejam em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/default-attributes.png)

1. Além de acima, o aplicativo iPass SmartConnect espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.

    | Nome |  Atributo de origem|
    | ---------------| ----------|
    | firstName | User. excertoname |
    | lastName | User. sobrenome |
    | e-mail | User. UserPrincipalName |
    | o nome de utilizador | User. UserPrincipalName |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/metadataxml.png)

1. Na seção **Configurar IPass SmartConnect** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo usuário** na parte superior da tela.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao iPass SmartConnect.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **IPass SmartConnect**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-ipass-smartconnect-sso"></a>Configurar o SSO do iPass SmartConnect

Para configurar o logon único no lado do **IPass SmartConnect** , é necessário enviar o XML de **metadados de Federação** baixado e as URLs copiadas apropriadas de portal do Azure para a [equipe de suporte do iPass SmartConnect](mailto:help@ipass.com). Eles definem essa configuração para que a conexão de SSO do SAML seja definida corretamente em ambos os lados.

### <a name="create-ipass-smartconnect-test-user"></a>Criar usuário de teste do iPass SmartConnect

Nesta seção, você criará um usuário chamado Brenda Simon no iPass SmartConnect. Trabalhe com a [equipe de suporte do IPass SmartConnect](mailto:help@ipass.com) para adicionar os usuários ou o domínio que deve ser adicionado a uma lista de permissões para a plataforma iPass SmartConnect. Se o domínio for adicionado pela equipe, os usuários serão automaticamente provisionados para a plataforma iPass SmartConnect. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco iPass SmartConnect no painel de acesso, você deverá ser conectado automaticamente ao iPass SmartConnect para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o iPass SmartConnect com o Azure AD](https://aad.portal.azure.com/)

