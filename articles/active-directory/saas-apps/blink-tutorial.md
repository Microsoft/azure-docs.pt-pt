---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com piscar | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o piscar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6a7c17d6-addc-4df0-bb52-283f7790bca0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c87fe6dce4fa35eaed4ab43d6dcbffeae32e27f
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69626048"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-blink"></a>Tutorial: Integração de SSO (logon único) do Azure Active Directory com intermitência

Neste tutorial, você aprenderá a integrar o pisque com o Azure Active Directory (Azure AD). Ao integrar o piscar com o Azure AD, você pode:

* Controle no Azure AD que tem acesso ao piscar.
* Habilite seus usuários a serem automaticamente conectados para piscar com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Piscar dá suporte ao SSO iniciado pelo **SP**
* Piscar dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-blink-from-the-gallery"></a>Adicionando o piscar da Galeria

Para configurar a integração do piscar com o Azure AD, você precisará adicionar o piscar da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **piscar** na caixa de pesquisa.
1. Selecione **piscar** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-blink"></a>Configurar e testar o logon único do Azure AD para piscar

Configure e teste o SSO do Azure AD com intermitência usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em intermitência.

Para configurar e testar o SSO do Azure AD com o pisque, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO de intermitência](#configure-blink-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste de intermitência](#create-blink-test-user)** – para ter um equivalente de B. Simon em intermitência que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos de **piscar** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando um dos seguintes padrões:
    
    | | |
    |-|-|
    | `https://app.joinblink.com`|
    | `https://<SUBDOMAIN>.joinblink.com`|
    | | |

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão:`https://api.joinblink.com/saml/o-<TENANTID>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contate a [equipe de suporte ao cliente do piscar](https://help.joinblink.com) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. O aplicativo de reuniões de intermitência espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo atributos de usuário.

    ![image](common/edit-attribute.png)

1. Além de acima, o aplicativo de reuniões de intermitência espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção declarações do usuário, na caixa de diálogo atributos de usuário, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | Name | Atributo de origem|
    | ---------------|  --------- |
    |   first_name    | user.givenname |
    |   second_name    | User. sobrenome |
    |   email       | user.mail |
    | | |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **Guardar**.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar intermitência** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao piscar.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **piscar**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-blink-sso"></a>Configurar SSO de intermitência

Para configurar o logon único no lado do **piscar** , é necessário enviar o XML de **metadados de Federação** baixado e as URLs copiadas apropriadas do portal do Azure para a equipe de suporte do [piscar](https://help.joinblink.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-blink-test-user"></a>Criar usuário de teste de intermitência

Nesta seção, um usuário chamado Brenda Simon é criado em intermitência. O piscar dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir em intermitência, um novo será criado após a autenticação.

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco piscar no painel de acesso, você deverá entrar automaticamente no piscar para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a margem de atraso com o Azure AD](https://aad.portal.azure.com/)