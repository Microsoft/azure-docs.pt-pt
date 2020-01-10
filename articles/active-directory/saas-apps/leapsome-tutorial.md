---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Leapsome | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Leapsome.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cb523e97-add8-4289-b106-927bf1a02188
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef28b95e779e2b814b0ae91059c3edd12644d7c9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430946"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-leapsome"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Leapsome

Neste tutorial, você aprenderá a integrar o Leapsome com o Azure Active Directory (Azure AD). Ao integrar o Leapsome ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Leapsome.
* Habilite seus usuários a serem conectados automaticamente ao Leapsome com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Leapsome.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Leapsome dá suporte ao **SP e** ao SSO iniciado pelo IDP

## <a name="adding-leapsome-from-the-gallery"></a>Adicionando o Leapsome da Galeria

Para configurar a integração do Leapsome ao Azure AD, você precisará adicionar o Leapsome da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Leapsome** na caixa de pesquisa.
1. Selecione **Leapsome** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-leapsome"></a>Configurar e testar o logon único do Azure AD para o Leapsome

Configure e teste o SSO do Azure AD com o Leapsome usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Leapsome.

Para configurar e testar o SSO do Azure AD com o Leapsome, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do Leapsome](#configure-leapsome-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do Leapsome](#create-leapsome-test-user)** – para ter um equivalente de B. Simon em Leapsome que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Leapsome** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL: `https://www.leapsome.com`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`

    > [!NOTE]
    > A URL de resposta e o valor da URL de logon anteriores não são um valor real. Você vai atualizá-los com os valores reais, que são explicados posteriormente no tutorial.

1. O aplicativo Leapsome espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/default-attributes.png)

1. Além de acima, o aplicativo Leapsome espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de origem | Espaço de Nomes |
    | ---------------| --------------- | --------- |  
    | nomepróprio | user.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | apelido | User. sobrenome | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | title | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | imagem | URL para a imagem do funcionário | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | | |

    > [!Note]
    > O valor do atributo de imagem não é real. Atualize esse valor com a URL da imagem real. Para obter esse valor, entre em contato com a [equipe de suporte ao cliente do Leapsome](mailto:support@leapsome.com).

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar Leapsome** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao Leapsome.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Leapsome**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-leapsome-sso"></a>Configurar o SSO do Leapsome

1. Em uma janela diferente do navegador da Web, entre no Leapsome como um administrador de segurança.

1. No canto superior direito, clique no logotipo configurações e, em seguida, clique em **configurações de administrador**.

    ![Conjunto de Leapsome](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

1. Na barra de menus à esquerda, clique em **logon único (SSO)** e, na página **SSO (logon único) baseado em SAML** , execute as seguintes etapas:

    ![SAML do Leapsome](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. Selecione **habilitar logon único baseado em SAML**.

    b. Copie o valor da **URL de logon (aponte os usuários aqui para iniciar logon)** e cole-o na caixa de texto **URL de logon** na seção **configuração básica do SAML** em portal do Azure.

    c. Copie o valor da **URL de resposta (recebe resposta do seu provedor de identidade)** e cole-o na caixa de texto **URL de resposta** na seção **configuração básica do SAML** em portal do Azure.

    d. Na caixa de texto **URL de logon SSO (fornecida pela provedor de identidade)** , Cole o valor da **URL de logon**, que você copiou do portal do Azure.

    e. Copie o certificado que você baixou de portal do Azure sem comentários `--BEGIN CERTIFICATE and END CERTIFICATE--` e cole-o na caixa de texto **certificado (fornecido pelo provedor de identidade)** .

    f. Clique em **Atualizar configurações de SSO**.

### <a name="create-leapsome-test-user"></a>Criar usuário de teste do Leapsome

Nesta seção, você criará um usuário chamado Brenda Simon no Leapsome. Trabalhe com a [equipe de suporte ao cliente do Leapsome](mailto:support@leapsome.com) para adicionar os usuários ou o domínio que deve ser adicionado a uma lista de permissões para a plataforma Leapsome. Se o domínio for adicionado pela equipe, os usuários serão automaticamente provisionados para a plataforma Leapsome. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Leapsome no painel de acesso, você deverá ser conectado automaticamente ao Leapsome para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Leapsome com o Azure AD](https://aad.portal.azure.com/)