---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o NetSuite | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a9b834feacd6241e66e18a4f4e6aadc43e909c7
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72438489"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netsuite"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o NetSuite

Neste tutorial, você aprenderá a integrar o NetSuite ao Azure Active Directory (Azure AD). Ao integrar o NetSuite ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao NetSuite.
* Habilite seus usuários a serem conectados automaticamente ao NetSuite com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do NetSuite.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O NetSuite dá suporte ao SSO iniciado pelo **IDP**

* O NetSuite dá suporte ao provisionamento **de usuário just in time**

* O NetSuite dá suporte ao [provisionamento automatizado de usuários](NetSuite-provisioning-tutorial.md)

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-netsuite-from-the-gallery"></a>Adicionando o NetSuite da Galeria

Para configurar a integração do NetSuite ao Azure AD, você precisará adicionar o NetSuite da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **NetSuite** na caixa de pesquisa.
1. Selecione **NetSuite** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Configurar e testar o logon único do Azure AD para o NetSuite

Configure e teste o SSO do Azure AD com o NetSuite usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no NetSuite.

Para configurar e testar o SSO do Azure AD com o NetSuite, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do NetSuite](#configure-netsuite-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do NetSuite](#create-netsuite-test-user)** – para ter um equivalente de B. Simon no NetSuite que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **NetSuite** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://<tenant-name>.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs` |

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de resposta real. Contate a [equipe de suporte ao cliente do NetSuite](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml) para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. O aplicativo NetSuite espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo atributos de usuário.

    ![imagem](common/edit-attribute.png)

1. Além de acima, o aplicativo NetSuite espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção declarações do usuário, na caixa de diálogo atributos de usuário, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | Nome | Atributo de origem | 
    | ---------------| --------------- |
    | conta  | `account id` |

    1. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    1. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    1. Deixe o **namespace** em branco.

    1. Selecione origem como **atributo**.

    1. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    1. Clique em **OK**

    1. Clique em **Guardar**.

    >[!NOTE]
    >O valor do atributo de conta não é real. Você atualizará esse valor, que é explicado posteriormente no tutorial.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/metadataxml.png)

1. Na seção **Configurar o NetSuite** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao NetSuite.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **NetSuite**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-netsuite-sso"></a>Configurar o SSO do NetSuite

1. Abra uma nova guia no navegador e entre no site da empresa do NetSuite como um administrador.

2. Na barra de ferramentas na parte superior da página, clique em **configuração**, navegue até **empresa** e clique em **habilitar recursos**.

    ![Configurar logon único](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Na barra de ferramentas no meio da página, clique em **SuiteCloud**.

    ![Configurar logon único](./media/NetSuite-tutorial/ns-suitecloud.png)

4. Na seção **gerenciar autenticação** , selecione **logon único do SAML** para habilitar a opção de logon único do SAML no NetSuite.

    ![Configurar logon único](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Na barra de ferramentas na parte superior da página, clique em **configuração**.

    ![Configurar logon único](./media/NetSuite-tutorial/ns-setup.png)

6. Na lista **tarefas de instalação** , clique em **integração**.

    ![Configurar logon único](./media/NetSuite-tutorial/ns-integration.png)

7. Na seção **gerenciar autenticação** , clique em **logon único do SAML**.

    ![Configurar logon único](./media/NetSuite-tutorial/ns-saml.png)

8. Na página **instalação do SAML** , na seção **configuração do NetSuite** , execute as seguintes etapas:

    ![Configurar logon único](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Selecione o **método de autenticação primária**.

    b. Para o campo rotulado como **SAMLV2 metadados do provedor de identidade**, selecione **carregar arquivo de metadados IDP**. Em seguida, clique em **procurar** para carregar o arquivo de metadados que você baixou de portal do Azure.

    c. Clique em **Enviar**.

9. No NetSuite, clique em **configuração** e, em seguida, navegue até **empresa** e clique em **informações da empresa** no menu de navegação superior.

    ![Configurar logon único](./media/NetSuite-tutorial/ns-com.png)

    ![Configurar logon único](./media/NetSuite-tutorial/ns-account-id.png)

    b. Na página **informações da empresa** , na coluna à direita, copie a **ID da conta**.

    c. Cole a **ID da conta** que você copiou da conta do NetSuite no campo **valor do atributo** no Azure AD. 

10. Antes que os usuários possam executar o logon único no NetSuite, eles devem primeiro receber as permissões apropriadas no NetSuite. Siga as instruções abaixo para atribuir essas permissões.

    a. No menu de navegação superior, clique em **configuração**.

    ![Configurar logon único](./media/NetSuite-tutorial/ns-setup.png)

    b. No menu de navegação à esquerda, selecione **usuários/funções**e, em seguida, clique em **gerenciar funções**.

    ![Configurar logon único](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Clique em **nova função**.

    d. Digite um **nome** para a nova função.

    ![Configurar logon único](./media/NetSuite-tutorial/ns-new-role.png)

    e. Clique em **Guardar**.

    f. No menu na parte superior, clique em **permissões**. Em seguida, clique em **instalação**.

    ![Configurar logon único](./media/NetSuite-tutorial/ns-sso.png)

    g. Selecione **logon único do SAML**e clique em **Adicionar**.

    h. Clique em **Guardar**.

    i. No menu de navegação superior, clique em **instalação**e em **Gerenciador de instalação**.

    ![Configurar logon único](./media/NetSuite-tutorial/ns-setup.png)

    j. No menu de navegação à esquerda, selecione **usuários/funções**e clique em **gerenciar usuários**.

    ![Configurar logon único](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Selecione um usuário de teste. Em seguida, clique em **Editar** e navegue até a guia **acessar** .

    ![Configurar logon único](./media/NetSuite-tutorial/ns-edit-user.png)

    debug. Na caixa de diálogo funções, atribua a função apropriada que você criou.

    ![Configurar logon único](./media/NetSuite-tutorial/ns-add-role.png)

    d. Clique em **Guardar**.

### <a name="create-netsuite-test-user"></a>Criar usuário de teste do NetSuite

Nesta seção, um usuário chamado Brenda Simon é criado no NetSuite. O NetSuite dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no NetSuite, um novo será criado após a autenticação.

## <a name="test-sso"></a>Testar SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do NetSuite no painel de acesso, você deverá ser conectado automaticamente ao NetSuite para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o NetSuite com o Azure AD](https://aad.portal.azure.com/)

