---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com os elementos SD | Microsoft Docs'
description: Saiba como configurar o logon único entre os elementos Azure Active Directory e SD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f0386307-bb3b-4810-8d4b-d0bfebda04f4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a387659e2375444fd32cf731ab4bccc210b669a
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081671"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sd-elements"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com os elementos SD

Neste tutorial, você aprenderá a integrar os elementos SD com o Azure Active Directory (Azure AD). Ao integrar elementos SD ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso aos elementos SD.
* Habilite seus usuários a serem automaticamente conectados a elementos SD com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) de elementos SD.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Os elementos SD dão suporte ao SSO iniciado pelo **IDP**

## <a name="adding-sd-elements-from-the-gallery"></a>Adicionando elementos SD da Galeria

Para configurar a integração de elementos SD ao Azure AD, você precisa adicionar elementos SD da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite os **elementos SD** na caixa de pesquisa.
1. Selecione os **elementos SD** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sd-elements"></a>Configurar e testar o logon único do Azure AD para elementos SD

Configure e teste o SSO do Azure AD com os elementos SD usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em elementos SD.

Para configurar e testar o SSO do Azure AD com os elementos SD, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO de elementos SD](#configure-sd-elements-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste de elementos SD](#create-sd-elements-test-user)** – para ter um equivalente de B. Simon em elementos SD que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SD Elements** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador e a URL de resposta reais. Contate a [equipe de suporte ao cliente de elementos SD](mailto:support@sdelements.com) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. O aplicativo de elementos SD espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além de acima, o aplicativo SD Elements espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.

    | Nome |  Atributo de origem|
    | --- | --- |
    | e-mail |user.mail |
    | FirstName |user.givenname |
    | LastName |User. sobrenome |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **configurar elementos SD** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso a elementos SD.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **elementos SD**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-sd-elements-sso"></a>Configurar SSO de elementos SD

1. Para obter o logon único habilitado, entre em contato com a [equipe de suporte dos elementos SD](mailto:support@sdelements.com) e forneça o arquivo de certificado baixado.

1. Em uma janela diferente do navegador, faça logon em seu locatário do SD Elements como administrador.

1. No menu na parte superior, clique em **sistema**e, em seguida, **logon único**.

    ![Configurar o início de sessão único](./media/sd-elements-tutorial/tutorial_sd-elements_09.png)

1. Na caixa de diálogo **configurações de logon único** , execute as seguintes etapas:

    ![Configurar o início de sessão único](./media/sd-elements-tutorial/tutorial_sd-elements_10.png)

    a. Como **tipo de SSO**, selecione **SAML**.

    b. Na caixa de texto **ID da entidade do provedor de identidade** , Cole o valor do identificador do **Azure ad**que você copiou do portal do Azure.

    c. Na caixa de texto **serviço de logon único do provedor de identidade** , Cole o valor da URL de **logon**copiado de portal do Azure.

    d. Clique em **Guardar**.

### <a name="create-sd-elements-test-user"></a>Criar usuário de teste de elementos SD

O objetivo desta seção é criar um usuário chamado B. Simon em elementos SD. No caso de elementos SD, a criação de elementos SD usuários é uma tarefa manual.

**Para criar B. Simon em elementos SD, execute as seguintes etapas:**

1. Em uma janela do navegador da Web, faça logon em seu site de empresa de elementos do SD como administrador.

1. No menu na parte superior, clique em **Gerenciamento de usuários**e em **usuários**.

    ![Criando um usuário de teste de elementos SD](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

1. Clique em **Adicionar novo usuário**.

    ![Criando um usuário de teste de elementos SD](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)

1. Na caixa de diálogo **Adicionar novo usuário** , execute as seguintes etapas:

    ![Criando um usuário de teste de elementos SD](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 

    a. Na caixa de texto **email** , insira o email do usuário, como **b.simon@contoso.com** .

    b. Na caixa de texto **nome** , insira o nome do usuário, como **B.**

    c. Na caixa de texto **sobrenome** , insira o sobrenome do usuário, como **Simon**.

    d. Como **função**, selecione **usuário**.

    e. Clique em **criar usuário**.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando você clica no bloco elementos SD no painel de acesso, deve ser automaticamente conectado aos elementos SD para os quais você configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente os elementos SD com o Azure AD](https://aad.portal.azure.com/)