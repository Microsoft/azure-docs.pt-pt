---
title: 'Tutorial: Integração de Azure Active Directory com o backup de ponto de extremidade do carbonite | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o backup de ponto de extremidade carbonite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b78091f1-2f06-4c2c-8541-72aee0b5a322
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e078cb7daa787b9fe5e8bc996b36f0fef198f41c
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879683"
---
# <a name="tutorial-integrate-carbonite-endpoint-backup-with-azure-active-directory"></a>Tutorial: Integrar o backup do ponto de extremidade do Carbonite com o Azure Active Directory

Neste tutorial, você aprenderá a integrar o backup de ponto de extremidade do Carbonite ao Azure Active Directory (Azure AD). Ao integrar o backup de ponto de extremidade do Carbonite ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao backup de ponto de extremidade do carbonite.
* Habilite seus usuários a serem automaticamente conectados ao backup de ponto de extremidade do Carbonite com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único (SSO) do Carbonite Endpoint backup.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O backup de ponto **de** extremidade do Carbonite dá suporte a SP iniciado pelo IDP

## <a name="adding-carbonite-endpoint-backup-from-the-gallery"></a>Adicionando o backup do ponto de extremidade do Carbonite da Galeria

Para configurar a integração do backup do ponto de extremidade do Carbonite ao Azure AD, você precisará adicionar o backup do ponto de extremidade do Carbonite da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite backup do **ponto de extremidade Carbonite** na caixa de pesquisa.
1. Selecione **backup do ponto de extremidade do Carbonite** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste o SSO do Azure AD com o backup de ponto de extremidade do Carbonite usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no backup do ponto de extremidade do carbonite.

Para configurar e testar o SSO do Azure AD com o Carbonite Endpoint backup, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar SSO de backup de ponto de extremidade do Carbonite](#configure-carbonite-endpoint-backup-sso)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
5. **[Criar usuário de teste do Carbonite Endpoint backup](#create-carbonite-endpoint-backup-test-user)** – para ter um equivalente de B. Simon no backup do ponto de extremidade do Carbonite que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Carbonite Endpoint backup** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma das seguintes URLs:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com`|
    | `https://red-apac.mysecuredatavault.com`|
    | `https://red-fr.mysecuredatavault.com`|
    | `https://red-emea.mysecuredatavault.com`|
    | `https://kamino.mysecuredatavault.com`|
    | | |

    b. Na caixa de texto **URL de resposta** , digite uma das seguintes URLs:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-apac.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-fr.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-emea.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | | |

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma das seguintes URLs:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com/`|
    | `https://red-apac.mysecuredatavault.com/`|
    | `https://red-fr.mysecuredatavault.com/`|
    | `https://red-emea.mysecuredatavault.com/`|
    | | |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar backup de ponto de extremidade do Carbonite** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-carbonite-endpoint-backup-sso"></a>Configurar SSO de backup de ponto de extremidade Carbonite

1. Para automatizar a configuração no backup do ponto de extremidade do Carbonite, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar o backup do ponto de extremidade Carbonite** o direcionará para o aplicativo de backup do ponto de extremidade do carbonite. A partir daí, forneça as credenciais de administrador para entrar no backup do ponto de extremidade do carbonite. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-7.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar o backup de ponto de extremidade do Carbonite manualmente, abra uma nova janela do navegador da Web e entre no site da empresa de backup do ponto de extremidade do Carbonite como administrador e execute as seguintes etapas:

4. Clique na **empresa** no painel esquerdo.

    ![Configuração de backup do ponto de extremidade Carbonite ](media/carbonite-endpoint-backup-tutorial/configure1.png)

5. Clique em **logon único**.

    ![Configuração de backup do ponto de extremidade Carbonite ](media/carbonite-endpoint-backup-tutorial/configure2.png)

6. Clique em **habilitar** e em **Editar configurações** para configurar.

    ![Configuração de backup do ponto de extremidade Carbonite ](media/carbonite-endpoint-backup-tutorial/configure3.png)

7. Na página Configurações de **logon único** , execute as seguintes etapas:

    ![Configuração de backup do ponto de extremidade Carbonite ](media/carbonite-endpoint-backup-tutorial/configure4.png)

    1. Na caixa de texto **nome do provedor de identidade** , Cole o valor do identificador do **Azure ad** copiado do portal do Azure.

    1. Na caixa de texto **URL do provedor de identidade** , Cole o valor da URL de **logon** copiado do portal do Azure.

    1. Clique em **escolher arquivo** para carregar o arquivo de **certificado (Base64)** baixado da portal do Azure.

    1. Clique em **Guardar**.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao backup do ponto de extremidade do carbonite.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Carbonite Endpoint backup**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-carbonite-endpoint-backup-test-user"></a>Criar usuário de teste do Carbonite Endpoint backup

1. Em outra janela do navegador da Web, entre no site da empresa de backup do ponto de extremidade do Carbonite como um administrador.

1. Clique em **usuários** no painel esquerdo e, em seguida, clique em **Adicionar usuário**.

    ![Adicionar usuário no backup do ponto de extremidade do carbonite](media/carbonite-endpoint-backup-tutorial/adduser1.png)

1. Na página **Adicionar usuário** , execute as seguintes etapas:

    ![Adicionar usuário no backup do ponto de extremidade do carbonite](media/carbonite-endpoint-backup-tutorial/adduser2.png)

    1. Insira o **email**, o **nome**, o **sobrenome** do usuário e forneça as permissões necessárias para o usuário de acordo com os requisitos organizacionais.

    1. Clique em **Adicionar utilizador**.

### <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco Carbonite Endpoint backup no painel de acesso, você deverá entrar automaticamente no backup do ponto de extremidade do Carbonite para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)