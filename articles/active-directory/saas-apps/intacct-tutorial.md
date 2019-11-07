---
title: 'Tutorial: integração do Azure Active Directory com o Sage Intacct | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e o Sage Intacct.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b28390dba009226d493f5bfc6a5270b067f5bba0
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "73570487"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>Tutorial: integrar o Sage Intacct com o Azure Active Directory

Neste tutorial, você aprenderá a integrar o Sage Intacct com o Azure Active Directory (Azure AD). Ao integrar o Sage Intacct ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Sage Intacct.
* Habilite seus usuários a entrar automaticamente no Sage Intacct com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Sage Intacct.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Sage Intacct dá suporte ao SSO iniciado pelo **IDP**

## <a name="adding-sage-intacct-from-the-gallery"></a>Adicionando o Sage Intacct da Galeria

Para configurar a integração do Sage Intacct ao Azure AD, você precisa adicionar o Sage Intacct da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Sage Intacct** na caixa de pesquisa.
1. Selecione o **Sage Intacct** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sage-intacct"></a>Configurar e testar o logon único do Azure AD para o Sage Intacct

Configure e teste o SSO do Azure AD com o Sage Intacct usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Sage Intacct.

Para configurar e testar o SSO do Azure AD com o Sage Intacct, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
2. **[Configurar o INTACCT SSO do Sage](#configure-sage-intacct-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Sage Intacct](#create-sage-intacct-test-user)** – para ter um equivalente de B. Simon no Sage Intacct que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo do **Sage Intacct** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta** , digite uma URL: `https://www.intacct.com/ia/acct/sso_response.phtml`

1. O aplicativo Sage Intacct espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo atributos de usuário..

    ![image](common/edit-attribute.png)

1. Além de acima, o aplicativo Sage Intacct espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | Nome  |  Atributo de origem|
    | ---------------| --------------- |
    | Nome da Empresa | **ID da empresa do Sage Intacct** |
    | nome | O valor deve ser o mesmo que a **ID de usuário**do Sage Intacct, que você insere na **seção criar usuário de teste do Sage Intacct**, que é explicada posteriormente no tutorial |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier | O valor deve ser o mesmo que a **ID de usuário do SSO Federado**do Sage Intacct, que você insere na **seção criar usuário de teste do Sage Intacct**, que é explicada posteriormente no tutorial |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **OK**

    g. Clique em **Guardar**.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/certificatebase64.png)

1. Na seção **Configurar o Sage Intacct** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao Sage Intacct.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Sage Intacct**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-sage-intacct-sso"></a>Configurar o SSO Intacct do Sage

1. Em uma janela diferente do navegador da Web, entre em seu site de empresa do Sage Intacct como administrador.

1. Clique na guia **empresa** e, em seguida, clique em **informações da empresa**.

    (./media/intacct-tutorial/ic790037.png "Empresa") ![da empresa]

1. Clique na guia **segurança** e, em seguida, clique em **Editar**.

    ![](./media/intacct-tutorial/ic790038.png "Segurança") de segurança

1. Na seção **SSO (logon único)** , execute as seguintes etapas:

    Logon ![único logon](./media/intacct-tutorial/ic790039.png "único")

    a. Selecione **habilitar logon único**.

    b. Como **tipo de provedor de identidade**, selecione **SAML 2,0**.

    c. Na caixa de texto **URL do emissor** , Cole o valor do **identificador do Azure ad**que você copiou do portal do Azure.

    d. Na caixa de texto **URL de logon** , Cole o valor da **URL de logon**copiado de portal do Azure.

    e. Abra seu certificado codificado em **base 64** no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa **certificado** .

    f. Clique em **Guardar**.

### <a name="create-sage-intacct-test-user"></a>Criar usuário de teste do Sage Intacct

Para configurar os usuários do Azure AD para que eles possam entrar no Sage Intacct, eles devem ser provisionados no Sage Intacct. Para o Sage Intacct, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Entre em seu locatário do **Sage Intacct** .

1. Clique na guia **empresa** e, em seguida, clique em **usuários**.

    ![](./media/intacct-tutorial/ic790041.png "Usuários do") usuário

1. Clique na guia **Adicionar** .

    ![Adicionar](./media/intacct-tutorial/ic790042.png "Adicionar")

1. Na seção **informações do usuário** , execute as seguintes etapas:

    ![](./media/intacct-tutorial/ic790043.png "Informações") do usuário informações do usuário

    a. Digite a **ID de usuário**, o **sobrenome**, **o nome**, o **endereço de email**, o **título**e o **telefone** de uma conta do Azure AD que você deseja provisionar na seção informações do **usuário** .

    > [!NOTE]
    > Verifique se a **ID de usuário** na captura de tela acima e o valor do **atributo de origem** que está mapeado com o atributo **Name** na seção de **atributos de usuário** no portal do Azure devem ser iguais.

    b. Selecione os **privilégios de administrador** de uma conta do Azure AD que você deseja provisionar.

    c. Clique em **Guardar**. 
    
    d. O titular da conta do Azure AD recebe um email e segue um link para confirmar sua conta antes que ela se torne ativa.

1. Clique na guia **logon único** e verifique se a ID de **usuário do SSO Federado** na captura de tela abaixo e o valor do **atributo de origem** que está mapeado com o `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` na seção **atributos de usuário** na portal do Azure devem ser iguais.

    ![](./media/intacct-tutorial/ic790044.png "Informações") do usuário informações do usuário

> [!NOTE]
> Para provisionar contas de usuário do Azure AD, você pode usar outras ferramentas de criação de conta de usuário do Sage Intacct ou APIs fornecidas pelo Sage Intacct.

## <a name="test-sso"></a>Testar SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do Sage Intacct no painel de acesso, você deverá entrar automaticamente no Sage Intacct para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

