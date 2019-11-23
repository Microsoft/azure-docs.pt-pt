---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Freshworks | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Freshworks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 697c3509-0e3a-4b05-9d5b-9ded4269eb60
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b73b8769b5f7360ba4ad778600b1000b8b3f7bc7
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376414"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshworks"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Freshworks

Neste tutorial, você aprenderá a integrar o Freshworks com o Azure Active Directory (Azure AD). Ao integrar o Freshworks ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Freshworks.
* Habilite seus usuários a serem conectados automaticamente ao Freshworks com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Freshworks.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Freshworks dá suporte ao SSO iniciado por **SP**

## <a name="adding-freshworks-from-the-gallery"></a>Adicionando o Freshworks da Galeria

Para configurar a integração do Freshworks ao Azure AD, você precisará adicionar o Freshworks da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Freshworks** na caixa de pesquisa.
1. Selecione **Freshworks** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshworks"></a>Configurar e testar o logon único do Azure AD para o Freshworks

Configure e teste o SSO do Azure AD com o Freshworks usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Freshworks.

Para configurar e testar o SSO do Azure AD com o Freshworks, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do Freshworks](#configure-freshworks-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Freshworks](#create-freshworks-test-user)** – para ter um equivalente de B. Simon em Freshworks que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Freshworks** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.freshworks.com/login`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.freshworks.com/sp/SAML/<MODULE_ID>/metadata`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contate a [equipe de suporte ao cliente do Freshworks](mailto:support@freshworks.com) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Para modificar as opções de de **assinatura** de acordo com seu requisito, clique no botão **Editar** para abrir o **certificado de autenticação SAML** caixa de diálogo.

     ![image](common/edit-certificate.png)

     ![Configuração do Freshworks](./media/freshworks-tutorial/response.png)

    a. Selecione **assinar resposta SAML** como **opção de assinatura**.

    b. Clique em **Guardar**.

1. Na seção **Configurar Freshworks** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao Freshworks.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Freshworks**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-freshworks-sso"></a>Configurar o SSO do Freshworks

1. Abra uma nova janela do navegador da Web e entre no site da empresa do Freshworks como administrador e execute as seguintes etapas:

2. No lado esquerdo do menu, clique no ícone **segurança** , em seguida, marque a opção **logon único** e selecione **SSO do SAML** em **métodos de autenticação**.

    ![Configuração do Freshworks](./media/freshworks-tutorial/configure01.png)

3. Na seção **logon único** , execute as seguintes etapas:

    ![Configuração do Freshworks](./media/freshworks-tutorial/configure02.png)

    a. Clique em **copiar** para copiar a **ID da entidade do provedor de serviços (SP)** para sua instância e cole-a na caixa de texto **identificador (ID da entidade)** na seção **configuração básica do SAML** em portal do Azure.

    b. Na **ID da entidade fornecida pela caixa de texto IDP** , Cole o valor do identificador do **Azure ad** copiado do portal do Azure.

    c. Na caixa de texto **URL de SSO do SAML** , Cole o valor da URL de **logon** copiado do portal do Azure.

    d. Abra o certificado codificado em base64 no bloco de notas, copie seu conteúdo e cole-o na caixa de texto **certificado de segurança** .

    e. Clique em **Guardar**.

### <a name="create-freshworks-test-user"></a>Criar usuário de teste do Freshworks

Nesta seção, você criará um usuário chamado B. Simon em Freshworks. Trabalhe com a [equipe de suporte ao cliente do Freshworks](mailto:support@freshworks.com) para adicionar os usuários na plataforma do Freshworks. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Freshworks no painel de acesso, você deverá ser conectado automaticamente ao Freshworks para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Freshworks com o Azure AD](https://aad.portal.azure.com/)

