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
ms.topic: tutorial
ms.date: 01/10/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad7065ba6378bcb383e67b4a58d7c195e88679ca
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75890671"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-sso-with-netsuite"></a>Tutorial: integrar o SSO (logon único) do Azure AD ao NetSuite

Neste tutorial, você aprenderá a integrar o NetSuite ao Azure Active Directory (Azure AD). Ao integrar o NetSuite ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao NetSuite.
* Habilite seus usuários a entrarem automaticamente no NetSuite com suas contas do Azure AD.
* Gerencie suas contas em um local central, o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do NetSuite.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. 

O NetSuite oferece suporte a:

* SSO iniciado pelo IDP.
* Provisionamento de usuário JIT (just-in-time).
* [Provisionamento automatizado de usuários](NetSuite-provisioning-tutorial.md).

> [!NOTE]
> Como o identificador desse aplicativo é um valor de cadeia de caracteres fixo, apenas uma instância pode ser configurada em um locatário.

## <a name="add-netsuite-from-the-gallery"></a>Adicionar o NetSuite da Galeria

Para configurar a integração do NetSuite ao Azure AD, adicione o NetSuite da Galeria à sua lista de aplicativos SaaS gerenciados fazendo o seguinte:

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta corporativa ou de estudante ou com uma conta Microsoft pessoal.
1. No painel esquerdo, selecione o serviço **Azure Active Directory** .
1. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **NetSuite** na caixa de pesquisa.
1. No painel de resultados, selecione **NetSuite**e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Configurar e testar o logon único do Azure AD para o NetSuite

Configure e teste o SSO do Azure AD com o NetSuite usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no NetSuite.

Para configurar e testar o SSO do Azure AD com o NetSuite, conclua os seguintes blocos de construção:

1. [Configure o SSO do Azure ad](#configure-azure-ad-sso) para permitir que seus usuários usem esse recurso.
    * [Crie um usuário de teste do Azure ad](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com o usuário B. Simon.  
    * [Atribua o usuário de teste do Azure ad](#assign-the-azure-ad-test-user) para habilitar o usuário B. Simon para usar o logon único do Azure AD.
1. [Configure o SSO do NetSuite](#configure-netsuite-sso) para configurar as configurações de logon único no lado do aplicativo.
    * [Crie o usuário de teste do NetSuite](#create-the-netsuite-test-user) para ter um equivalente de usuário B. Simon no NetSuite que esteja vinculado à representação do usuário no Azure AD.
1. [Teste o SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Para habilitar o SSO do Azure AD no portal do Azure, faça o seguinte:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **NetSuite** , procure a seção **gerenciar** e, em seguida, selecione **logon único**.
1. No painel **selecionar um método de logon único** , selecione **SAML**.
1. No painel **Configurar logon único com SAML** , selecione o ícone **Editar** ("lápis") ao lado da **configuração básica do SAML**.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , na caixa de texto **URL de resposta** , digite uma URL em um dos seguintes formatos:

    ||
    |-|
    | `https://<Account ID>.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na1.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na2.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.sandbox.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na1.sandbox.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na2.sandbox.NetSuite.com/saml2/acs`|

    > [!NOTE]
    > Os valores nas URLs anteriores não são reais. Atualize-os com a URL de resposta real. Para obter o valor, entre em contato com a [equipe de suporte ao cliente do NetSuite](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml). Você também pode consultar os formatos mostrados na seção **configuração básica do SAML** no portal do Azure.

1. O aplicativo NetSuite espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/default-attributes.png)

1. Além de acima, o aplicativo NetSuite espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de origem |
    | ---------------| --------------- |
    | conta  | `account id` |

    > [!NOTE]
    > O valor do atributo de conta não é real. Você atualizará esse valor, conforme explicado posteriormente neste tutorial.

1. Na página Configurar logon único com SAML, na seção certificado de autenticação SAML, localize o XML de metadados de Federação e selecione baixar para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/metadataxml.png)

1. Na seção **Configurar o NetSuite** , copie a URL ou URLs apropriadas, dependendo do seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você cria um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo da portal do Azure, selecione **Azure Active Directory** > **usuários** > todos os **usuários**.

1. Selecione **novo utilizador** na parte superior do ecrã.

1. No painel Propriedades do **usuário** , siga estas etapas:

   a. Na caixa **nome** , digite **B. Simon**.  
   b. Na caixa **nome de usuário** , insira o username@companydomain.extension (por exemplo, B.Simon@contoso.com).  
   c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .  
   d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que o usuário B. Simon use o logon único do Azure concedendo acesso ao NetSuite.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **NetSuite**.
1. No painel Visão geral, procure a seção **gerenciar** e, em seguida, selecione o link **usuários e grupos** .

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário** e, em seguida, no painel **Adicionar atribuição** , selecione **usuários e grupos**.

    ![O botão "Adicionar usuário"](common/add-assign-user.png)

1. No painel **usuários e grupos** , na lista suspensa **usuários** , selecione **B. Simon**e, em seguida, selecione o botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, faça o seguinte:

   a. No painel **selecionar função** , na lista suspensa, selecione a função apropriada para o usuário.  
   b. Na parte inferior da tela, selecione o botão **selecionar** .
1. No painel **Adicionar atribuição** , selecione o botão **atribuir** .

## <a name="configure-netsuite-sso"></a>Configurar o SSO do NetSuite

1. Abra uma nova guia no navegador e entre no site da empresa do NetSuite como um administrador.

2. Na barra de navegação superior, selecione **instalação**e, em seguida, selecione a **empresa** > **habilitar recursos**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Na barra de ferramentas no meio da página, selecione **SuiteCloud**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-suitecloud.png)

4. Em **gerenciar autenticação**, marque a caixa de seleção **logon único do SAML** para habilitar a opção de logon único do SAML no NetSuite.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Na barra de navegação superior, selecione **instalação**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-setup.png)

6. Na lista **tarefas de instalação** , selecione **integração**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-integration.png)

7. Em **gerenciar autenticação**, selecione **logon único do SAML**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-saml.png)

8. No painel **configuração do SAML** , em **configuração do NetSuite**, faça o seguinte:

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Marque a caixa de seleção **método de autenticação principal** .

    b. Em **metadados do provedor de identidade SAMLV2**, selecione **carregar arquivo de metadados IDP**e, em seguida, selecione **procurar** para carregar o arquivo de metadados que você baixou do portal do Azure.

    c. Selecione **submeter**.

9. Na barra de navegação superior do NetSuite, selecione **instalação**e, em seguida, selecione **empresa** > **informações da empresa**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-com.png)

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-account-id.png)

    b. No painel de **informações da empresa** , na coluna à direita, copie o valor da **ID da conta** .

    c. Cole a **ID da conta** que você copiou da conta do NetSuite na caixa **valor do atributo** no Azure AD. 

10. Antes que os usuários possam executar o logon único no NetSuite, eles devem primeiro receber as permissões apropriadas no NetSuite. Para atribuir essas permissões, faça o seguinte:

    a. Na barra de navegação superior, selecione **instalação**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-setup.png)

    b. No painel esquerdo, selecione **usuários/funções**e, em seguida, selecione **gerenciar funções**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Selecione **nova função**.

    d. Insira um **nome** para a nova função.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-new-role.png)

    e. Selecione **Guardar**.

    f. Na barra de navegação superior, selecione **permissões**. Em seguida, selecione **instalação**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-sso.png)

    g. Selecione **logon único do SAML**e, em seguida, selecione **Adicionar**.

    h. Selecione **Guardar**.

    i. Na barra de navegação superior, selecione **instalação**e, em seguida, selecione **Gerenciador de instalação**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-setup.png)

    j. No painel esquerdo, selecione **usuários/funções**e, em seguida, selecione **gerenciar usuários**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Selecione um usuário de teste, selecione **Editar**e, em seguida, selecione a guia **acesso** .

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-edit-user.png)

    l. No painel **funções** , atribua a função apropriada que você criou.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-add-role.png)

    m. Selecione **Guardar**.

### <a name="create-the-netsuite-test-user"></a>Criar o usuário de teste do NetSuite

Nesta seção, um usuário chamado B. Simon é criado no NetSuite. O NetSuite dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no NetSuite, um novo será criado após a autenticação.

## <a name="test-sso"></a>Testar SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao selecionar o bloco do NetSuite no painel de acesso, você deve entrar automaticamente no NetSuite para o qual você configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Experimente o NetSuite com o Azure AD](https://aad.portal.azure.com/)
