---
title: 'Tutorial: integração do Azure Active Directory com o Syncplicity | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: 663958ae367162eaeb336c819d1d219dc74a2cbe
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233288"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Tutorial: integrar o Syncplicity ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Syncplicity com o Azure Active Directory (Azure AD). Ao integrar o Syncplicity ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Syncplicity.
* Habilite seus usuários a serem conectados automaticamente ao Syncplicity com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura habilitada para SSO (logon único) do Syncplicity.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Syncplicity dá suporte ao SSO iniciado por **SP** .

## <a name="adding-syncplicity-from-the-gallery"></a>Adicionando o Syncplicity da Galeria

Para configurar a integração do Syncplicity ao Azure AD, você precisará adicionar o Syncplicity da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Syncplicity** na caixa de pesquisa.
1. Selecione **Syncplicity** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar o SSO do Azure AD

Configure e teste o SSO do Azure AD com o Syncplicity usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Syncplicity.

Para configurar e testar o SSO do Azure AD com o Syncplicity, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do Syncplicity](#configure-syncplicity-sso)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
5. **[Criar usuário de teste do Syncplicity](#create-syncplicity-test-user)** – para ter um equivalente de B. Simon em Syncplicity que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Syncplicity** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na página **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<companyname>.syncplicity.com`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contate a [equipe de suporte ao cliente do Syncplicity](https://www.syncplicity.com/contact-us) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

   ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar Syncplicity** , copie as URLs apropriadas com base em seu requisito.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso"></a>Configurar o SSO do Syncplicity

1. Entre no seu locatário do **Syncplicity** .

1. No menu na parte superior, clique em **administrador**, selecione **configurações**e, em seguida, clique em **domínio personalizado e logon único**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Na página da caixa de diálogo **logon único (SSO)** , execute as seguintes etapas:

    ![Logon único \(SSO\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. Na caixa de texto **domínio personalizado** , digite o nome do seu domínio.
  
    b. Selecione **habilitado** como **status de logon único**.

    c. Na caixa de texto **ID da entidade** , Cole o valor do **identificador (ID da entidade)** , que você usou na **configuração básica do SAML** no portal do Azure.

    d. Na caixa de texto **URL da página de entrada** , Cole a **URL de logon** que você copiou do portal do Azure.

    e. Na caixa de texto **URL da página de logout** , Cole a **URL de logout** que você copiou do portal do Azure.

    f. Em **certificado do provedor de identidade**, clique em **escolher arquivo**e carregue o certificado que você baixou do portal do Azure.

    g. Clique em **salvar alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo usuário** na parte superior da tela.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao Syncplicity.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Syncplicity**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-syncplicity-test-user"></a>Criar usuário de teste do Syncplicity

Para que os usuários do Azure AD possam entrar, eles devem ser provisionados no aplicativo Syncplicity. Esta seção descreve como criar contas de usuário do Azure AD no Syncplicity.

**Para provisionar uma conta de usuário no Syncplicity, execute as seguintes etapas:**

1. Entre no seu locatário do **Syncplicity** (por exemplo: `https://company.Syncplicity.com`).

1. Clique em **administrador** e selecione **contas de usuário** e, em seguida, clique em **Adicionar um usuário**.

    ![Gerenciar usuários](./media/syncplicity-tutorial/ic769764.png "Gerenciar usuários")

1. Digite os **endereços de email** de uma conta do Azure AD que você deseja provisionar, selecione **usuário** como **função**e clique em **Avançar**.

    ![Informações da conta](./media/syncplicity-tutorial/ic769765.png "Informações sobre a Conta")

    > [!NOTE]
    > O titular da conta do Azure AD recebe um email incluindo um link para confirmar e ativar a conta.

1. Selecione um grupo em sua empresa do qual o novo usuário deve se tornar membro e clique em **Avançar**.

    ![Associação de grupo](./media/syncplicity-tutorial/ic769772.png "Associação de grupo")

    > [!NOTE]
    > Se não houver grupos listados, clique em **Avançar**.

1. Selecione as pastas que você gostaria de colocar sob o controle do Syncplicity no computador do usuário e clique em **Avançar**.

    ![Pastas Syncplicity](./media/syncplicity-tutorial/ic769773.png "Pastas Syncplicity")

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação de conta de usuário Syncplicity ou APIs fornecidas pelo Syncplicity para provisionar contas de usuário do Azure AD.

### <a name="test-sso"></a>Testar SSO

Ao selecionar o bloco Syncplicity no painel de acesso, você deverá entrar automaticamente no Syncplicity para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)