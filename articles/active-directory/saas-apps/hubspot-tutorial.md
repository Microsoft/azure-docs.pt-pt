---
title: 'Tutorial: Integração do Azure Active Directory com o HubSpot | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o HubSpot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4b235426a7029abb9bb79ba56e582cccc3b14a6
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68944452"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Tutorial: Integração do Azure Active Directory com o HubSpot

Neste tutorial, você aprenderá a integrar o HubSpot com o Azure Active Directory (Azure AD).

A integração do HubSpot ao Azure AD oferece os seguintes benefícios:

* Você pode usar o Azure AD para controlar quem tem acesso ao HubSpot.
* Os usuários podem ser conectados automaticamente ao HubSpot com suas contas do Azure AD (logon único).
* Você pode gerenciar suas contas em um local central, o portal do Azure.

Para obter mais informações sobre a integração de aplicativos SaaS (software como serviço) com o Azure AD, consulte [logon único para aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao HubSpot, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura do Azure AD, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma assinatura do HubSpot com logon único habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste e integrará o HubSpot ao Azure AD.

O HubSpot dá suporte aos seguintes recursos:

* **Logon único iniciado pelo SP**
* **Logon único iniciado pelo IDP**

## <a name="add-hubspot-in-the-azure-portal"></a>Adicionar HubSpot no portal do Azure

Para integrar o HubSpot ao Azure AD, você deve adicionar o HubSpot à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu à esquerda, selecione **Azure Active Directory**.

    ![A opção Azure Active Directory](common/select-azuread.png)

1. Selecione **aplicativos** > empresariais**todos os aplicativos**.

    ![O painel aplicativos empresariais](common/enterprise-applications.png)

1. Para adicionar um aplicativo, selecione **novo aplicativo**.

    ![A opção novo aplicativo](common/add-new-app.png)

1. Na caixa de pesquisa, digite **HubSpot**. Nos resultados da pesquisa, selecione **HubSpot**e, em seguida, selecione **Adicionar**.

    ![HubSpot na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o HubSpot, com base em um usuário de teste chamado **Brenda Simon**. Para que o logon único funcione, você deve estabelecer uma relação vinculada entre um usuário do Azure AD e o usuário relacionado no HubSpot.

Para configurar e testar o logon único do Azure AD com o HubSpot, você deve concluir os seguintes blocos de construção:

| Tarefa | Descrição |
| --- | --- |
| **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** | Permite que os usuários usem esse recurso. |
| **[Configurar o logon único do HubSpot](#configure-hubspot-single-sign-on)** | Define as configurações de logon único no aplicativo. |
| **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** | Testa o logon único do Azure AD para um usuário chamado Brenda Simon. |
| **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** | Permite que o Brenda Simon use o logon único do Azure AD. |
| **[Criar um usuário de teste do HubSpot](#create-a-hubspot-test-user)** | Cria um equivalente de Brenda Simon no HubSpot que está vinculado à representação do usuário no Azure AD. |
| **[Testar logon único](#test-single-sign-on)** | Verifica se a configuração funciona. |

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você configurará o logon único do Azure AD com o HubSpot no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), no painel de integração de aplicativos do **HubSpot** , selecione **logon único**.

    ![Configurar opção de logon único](common/select-sso.png)

1. No painel **selecionar um método de logon único** , selecione o modo **SAML** ou **SAML/WS-** enalimentado para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

1. No painel **Configurar logon único com SAML** , selecione **Editar** (o ícone de lápis) para abrir o painel **configuração básica do SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

1. No painel **configuração básica do SAML** , para configurar o *modo iniciado pelo IDP*, conclua as seguintes etapas:

    1. Na caixa **identificador** , insira uma URL que tenha o seguinte padrão: https:\//API.hubspot.com/login-API/v1/SAML/login?portalId =\<Customer ID\>.

    1. Na caixa **URL de resposta** , insira uma URL que tenha o seguinte padrão: https:\//API.hubspot.com/login-API/v1/SAML/ACS?portalId =\<Customer ID\>.

    ![Informações de logon único de domínio e URLs do HubSpot](common/idp-intiated.png)

    > [!NOTE]
    > Para formatar as URLs, você também pode consultar os padrões mostrados no painel **configuração básica do SAML** no portal do Azure.

1. Para configurar o aplicativo no modo *iniciado pelo SP* :

    1. Selecione **definir URLs adicionais**.

    1. Na caixa **URL de logon** , digite **https\/:/app.hubspot.com/login**.

    ![A opção definir URLs adicionais](common/metadata-upload-additional-signon.png)

1. No painel **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , selecione **baixar** ao lado de **certificado (Base64)** . Selecione uma opção de download com base em seus requisitos. Salve o certificado em seu computador.

    ![A opção de download do certificado (Base64)](common/certificatebase64.png)

1. Na seção **Configurar HubSpot** , copie as seguintes URLs com base em seus requisitos:

    * URL de início de sessão
    * Identificador do Azure AD
    * URL de fim de sessão

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>Configurar o logon único do HubSpot

1. Abra uma nova guia no navegador e entre na sua conta de administrador do HubSpot.

1. Selecione o ícone de **configurações** no canto superior direito da página.

    ![O ícone de configurações em HubSpot](./media/hubspot-tutorial/config1.png)

1. Selecione **padrões de conta**.

    ![A opção de padrões de conta em HubSpot](./media/hubspot-tutorial/config2.png)

1. Role para baixo até a seção **segurança** e, em seguida, selecione **Configurar**.

    ![A opção set up no HubSpot](./media/hubspot-tutorial/config3.png)

1. Na seção **Configurar logon único** , conclua as seguintes etapas:

    1. Na caixa **URL do público (ID da entidade do provedor de serviços)** , selecione **copiar** para copiar o valor. No portal do Azure, no painel **configuração básica do SAML** , Cole o valor na caixa **identificador** .

    1. Na **URL de logon, no ACS, no destinatário ou** na caixa de redirecionamento, selecione **copiar** para copiar o valor. No portal do Azure, no painel **configuração básica do SAML** , Cole o valor na caixa **URL de resposta** .

    1. Em HubSpot, na caixa **identificador do provedor de identidade ou URL do emissor** , Cole o valor do **identificador do Azure ad** que você copiou no portal do Azure.

    1. No HubSpot, na caixa **URL de logon único do provedor de identidade** , Cole o valor da **URL de logon** que você copiou no portal do Azure.

    1. No bloco de notas do Windows, abra o arquivo de certificado (Base64) que você baixou. Selecione e copie o conteúdo do arquivo. Em seguida, em HubSpot, Cole-o na caixa **certificado X. 509** .

    1. Selecione **verificar**.

        ![A seção configurar logon único no HubSpot](./media/hubspot-tutorial/config4.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você cria um usuário de teste chamado Brenda Simon no portal do Azure.

1. Na portal do Azure, selecione **Azure Active Directory** > **usuários** > **todos os usuários**.

    ![As opções usuários e todos os usuários](common/users.png)

1. Selecione **novo usuário**.

    ![A nova opção de usuário](common/new-user.png)

1. No painel **usuário** , conclua as seguintes etapas:

    1. Na caixa **nome** , digite **brendafernandes**.
  
    1. Na caixa **nome de usuário** , digite **brendafernandes\@\<seu domínio-empresa->.\< extensão\>** . Por exemplo, **brendafernandes\@contoso.com**.

    1. Marque a caixa de seleção **Mostrar senha** . Anote o valor exibido na caixa **senha** .

    1. Selecione **Criar**.

    ![O painel de usuário](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você concede o acesso Brenda Simon ao HubSpot para que possa usar o logon único do Azure.

1. Na portal do Azure, selecione **aplicativos** > empresariais**todos os aplicativos** > **HubSpot**.

    ![O painel aplicativos empresariais](common/enterprise-applications.png)

1. Na lista de aplicativos, selecione **HubSpot**.

    ![HubSpot na lista de aplicativos](common/all-applications.png)

1. No menu, selecione **usuários e grupos**.

    ![A opção usuários e grupos](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**. Em seguida, no painel **Adicionar atribuição** , selecione **usuários e grupos**.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

1. No painel **usuários e grupos** , selecione **Brenda Simon** na lista de usuários. Escolha **Selecionar**.

1. Se você estiver esperando um valor de função na declaração SAML, no painel **selecionar função** , selecione a função relevante para o usuário na lista. Escolha **Selecionar**.

1. No painel **Adicionar atribuição** , selecione **atribuir**.

### <a name="create-a-hubspot-test-user"></a>Criar um usuário de teste do HubSpot

Para permitir que o Azure AD um usuário entre no HubSpot, o usuário deve ser provisionado no HubSpot. No HubSpot, o provisionamento é uma tarefa manual.

Para provisionar uma conta de usuário no HubSpot:

1. Entre no site da empresa do HubSpot como administrador.

1. Selecione o ícone de **configurações** no canto superior direito da página.

    ![O ícone de configurações em HubSpot](./media/hubspot-tutorial/config1.png)

1. Selecione **usuários & equipes**.

    ![A opção usuários & equipes no HubSpot](./media/hubspot-tutorial/user1.png)

1. Selecione **criar usuário**.

    ![A opção Create User no HubSpot](./media/hubspot-tutorial/user2.png)

1. Na caixa **Adicionar endereço (es) de email** , insira o endereço de email do usuário no formato brendafernandes\@contoso.com e, em seguida, selecione **Avançar**.

    ![A caixa Adicionar endereço (s) de email na seção criar usuários em HubSpot](./media/hubspot-tutorial/user3.png)

1. Na seção **criar usuários** , selecione cada guia. Em cada guia, defina as opções e permissões relevantes para o usuário. Em seguida, selecione **Seguinte**.

    ![Guias na seção criar usuários em HubSpot](./media/hubspot-tutorial/user4.png)

1. Para enviar o convite ao usuário, selecione **Enviar**.

    ![A opção Send em HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > O usuário é ativado depois que o usuário aceita o convite.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o portal meus aplicativos.

Depois de configurar o logon único, ao selecionar **HubSpot** no portal meus aplicativos, você será automaticamente conectado ao HubSpot. Para obter mais informações sobre o portal meus aplicativos, consulte [acessar e usar aplicativos no portal meus aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passos seguintes

Para saber mais, leia estes artigos:

- [Lista de tutoriais para integração de aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Logon único para aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
