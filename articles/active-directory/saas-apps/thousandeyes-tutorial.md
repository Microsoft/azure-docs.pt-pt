---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o ThousandEyes | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ThousandEyes.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd179049f88454c62244cf1819cee08ef78d0633
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373237"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-thousandeyes"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o ThousandEyes

Neste tutorial, você aprenderá a integrar o ThousandEyes com o Azure Active Directory (Azure AD). Ao integrar o ThousandEyes ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao ThousandEyes.
* Habilite seus usuários a serem conectados automaticamente ao ThousandEyes com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do ThousandEyes.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O ThousandEyes dá suporte ao **SP e** ao SSO iniciado pelo IDP
* O ThousandEyes dá suporte ao [provisionamento **automatizado** de usuários](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-thousandeyes-from-the-gallery"></a>Adicionando o ThousandEyes da Galeria

Para configurar a integração do ThousandEyes ao Azure AD, você precisará adicionar o ThousandEyes da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **ThousandEyes** na caixa de pesquisa.
1. Selecione **ThousandEyes** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-thousandeyes"></a>Configurar e testar o logon único do Azure AD para o ThousandEyes

Configure e teste o SSO do Azure AD com o ThousandEyes usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no ThousandEyes.

Para configurar e testar o SSO do Azure AD com o ThousandEyes, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do ThousandEyes](#configure-thousandeyes-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do ThousandEyes](#create-thousandeyes-test-user)** – para ter um equivalente de B. Simon em ThousandEyes que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ThousandEyes** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , o aplicativo é pré-configurado e as URLs necessárias já foram preenchidas previamente com o Azure. O usuário precisa salvar a configuração clicando no botão **salvar** .

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma url: `https://app.thousandeyes.com/login/sso`

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/certificatebase64.png)

1. Na seção **Configurar ThousandEyes** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao ThousandEyes.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **ThousandEyes**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-thousandeyes-sso"></a>Configurar o SSO do ThousandEyes

1. Em uma janela diferente do navegador da Web, faça logon em seu site de empresa do **ThousandEyes** como administrador.

2. No menu na parte superior, clique em **configurações**.

    ![](./media/thousandeyes-tutorial/ic790066.png "Configurações") de configurações

3. Clique em **conta**

    ![](./media/thousandeyes-tutorial/ic790067.png "Conta") da conta

4. Clique na guia **autenticação de & de segurança** .

    Segurança de ![autenticação de & de segurança](./media/thousandeyes-tutorial/ic790068.png "& autenticação")

5. Na seção **Configurar logon único** , execute as seguintes etapas:

    ![Configurar logon]único(./media/thousandeyes-tutorial/ic790069.png "configuração") de logon único

    a. Selecione **habilitar logon único**.

    b. Na caixa de texto **URL da página de logon** , Cole a **URL de logon**que você copiou do portal do Azure.

    c. Na caixa de texto **URL da página de logout** , Cole a **URL de logout**que você copiou do portal do Azure.

    d. Caixa de texto **emissor do provedor de identidade** , Cole o identificador do **Azure ad**, que você copiou do portal do Azure.

    e. Em **certificado de verificação**, clique em **escolher arquivo**e carregue o certificado que você baixou de portal do Azure.

    f. Clique em **Guardar**.

### <a name="create-thousandeyes-test-user"></a>Criar usuário de teste do ThousandEyes

O objetivo desta seção é criar um usuário chamado Brenda Simon no ThousandEyes. O ThousandEyes dá suporte ao provisionamento automático de usuário, que está habilitado por padrão. Você pode encontrar mais detalhes [aqui](thousandeyes-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuário.

**Se você precisar criar o usuário manualmente, execute as seguintes etapas:**

1. Entre no site da empresa do ThousandEyes como um administrador.

2. Clique em **configurações**.

    ![](./media/thousandeyes-tutorial/IC790066.png "Configurações") de configurações

3. Clique em **conta**.

    ![](./media/thousandeyes-tutorial/IC790067.png "Conta") da conta

4. Clique na guia **contas & usuários** .

    ![Contas &](./media/thousandeyes-tutorial/IC790073.png "contas de usuários & usuários")

5. Na seção **Adicionar contas de usuários &** , execute as seguintes etapas:

    ![Adicionar contas de usuário](./media/thousandeyes-tutorial/IC790074.png "Adicionar contas de usuário")

    a. Na caixa de texto **nome** , digite o nome do usuário, como **B. Simon**.

    b. Na caixa de texto **email** , digite o email do usuário, como b.simon@contoso.com.

    b. Clique em **Adicionar novo usuário à conta**.

    > [!NOTE]
    > O titular da conta Azure Active Directory receberá um email, incluindo um link para confirmar e ativar a conta.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação de conta de usuário ThousandEyes ou APIs fornecidas pelo ThousandEyes para provisionar Azure Active Directory contas de usuário.


## <a name="test-sso"></a>Testar SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do ThousandEyes no painel de acesso, você deverá ser conectado automaticamente ao ThousandEyes para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o ThousandEyes com o Azure AD](https://aad.portal.azure.com/)

- [Configurar provisionamento de usuário](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)