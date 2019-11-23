---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o SAP Cloud for Customer | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAP Cloud for Customer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 837787d375a7570b7daf0a149960ca0020bcdced
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264040"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o SAP Cloud for Customer

Neste tutorial, você aprenderá a integrar o SAP Cloud for Customer com o Azure Active Directory (Azure AD). Ao integrar o SAP Cloud for Customer ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao SAP Cloud for Customer.
* Habilite seus usuários a serem conectados automaticamente ao SAP Cloud for Customer com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do SAP Cloud for Customer.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SAP Cloud for Customer dá suporte ao SSO iniciado por **SP**

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Adicionando o SAP Cloud for Customer por meio da Galeria

Para configurar a integração do SAP Cloud for Customer ao Azure AD, você precisará adicionar o SAP Cloud for Customer da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **SAP Cloud for Customer** na caixa de pesquisa.
1. Selecione **SAP Cloud for Customer** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-for-customer"></a>Configurar e testar o logon único do Azure AD para o SAP Cloud for Customer

Configure e teste o SSO do Azure AD com o SAP Cloud for Customer usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SAP Cloud for Customer.

Para configurar e testar o SSO do Azure AD com o SAP Cloud for Customer, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SAP Cloud for Customer SSO](#configure-sap-cloud-for-customer-sso)** -para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do SAP Cloud for Customer](#create-sap-cloud-for-customer-test-user)** – para ter um equivalente de B. Simon no SAP Cloud for Customer que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SAP Cloud for Customer** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<server name>.crm.ondemand.com`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contate a [equipe de suporte ao cliente do SAP Cloud for Customer](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. O aplicativo SAP Cloud for Customer espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo atributos de usuário.

    ![image](common/edit-attribute.png)

1. Na seção **atributos de usuário** na caixa de diálogo **atributos de usuário & declarações** , execute as seguintes etapas:

    a. Clique no **ícone Editar** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Selecione **transformação** como **fonte**.

    c. Na lista **transformação** , selecione **ExtractMailPrefix ()** .

    d. Na lista **parâmetro 1** , selecione o atributo de usuário que você deseja usar para a sua implementação.
    Por exemplo, se você quiser usar o EmployeeID como um identificador de usuário exclusivo e tiver armazenado o valor do atributo em ExtensionAttribute2, selecione User. ExtensionAttribute2.

    e. Clique em **Guardar**.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar a nuvem do SAP para o cliente** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao SAP Cloud for Customer.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **SAP Cloud for Customer**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-sap-cloud-for-customer-sso"></a>Configurar a nuvem do SAP para SSO do cliente

1. Abra uma nova janela do navegador da Web e entre no site da empresa do SAP Cloud for Customer como um administrador.

2. No lado esquerdo do menu, clique em **provedores de identidade** > **provedores de identidade corporativa** > **Adicionar** e, em pop-up, adicionar o nome do provedor de identidade como o **Azure ad**, clique em **salvar** e, em seguida, clique em **configuração do SAML 2,0**.

    ![Configuração do SAP](./media/sap-customer-cloud-tutorial/configure01.png)

3. Na seção **configuração do SAML 2,0** , execute as seguintes etapas:

    ![Configuração do SAP](./media/sap-customer-cloud-tutorial/configure02.png)

    a. Clique em **procurar** para carregar o arquivo XML de metadados de Federação, que você baixou do portal do Azure.

    b. Depois que o arquivo XML for carregado com êxito, os valores abaixo serão preenchidos automaticamente e, em seguida, clique em **salvar**.

### <a name="create-sap-cloud-for-customer-test-user"></a>Criar um usuário de teste do SAP Cloud for Customer

Para permitir que os usuários do Azure AD entrem no SAP Cloud for Customer, eles devem ser provisionados no SAP Cloud for Customer. No SAP Cloud for Customer, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no SAP Cloud for Customer como um administrador de segurança.

2. No lado esquerdo do menu, clique em **usuários & autorizações** >gerenciamento de  **usuários** > **Adicionar usuário**.

    ![Configuração do SAP](./media/sap-customer-cloud-tutorial/configure03.png)

3. Na seção **Adicionar novo usuário** , execute as seguintes etapas:

    ![Configuração do SAP](./media/sap-customer-cloud-tutorial/configure04.png)

    a. Na caixa de texto **nome** , digite o nome do usuário como **B**.

    b. Na caixa de texto **sobrenome** , digite o nome do usuário, como **Simon**.

    c. Na caixa de texto **email** , insira o email do usuário, como `B.Simon@contoso.com`.

    d. Na caixa de texto **nome de logon** , digite o nome do usuário, como **B. Simon**.

    e. Selecione o **tipo de usuário** de acordo com seu requisito.

    f. Selecione a opção de **ativação de conta** de acordo com seu requisito.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco SAP Cloud for Customer no painel de acesso, você deverá entrar automaticamente no SAP Cloud for Customer para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o SAP Cloud for Customer com o Azure AD](https://aad.portal.azure.com/)

