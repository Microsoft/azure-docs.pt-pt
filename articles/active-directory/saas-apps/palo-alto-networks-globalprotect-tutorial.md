---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com o Palo Alto Networks – GlobalProtect | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Palo Alto Networks – GlobalProtect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03bef6f2-3ea2-4eaa-a828-79c5f1346ce5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8faa1d0ecfec629487c9cddee6a01fb9f809393f
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70166416"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-palo-alto-networks---globalprotect"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com o Palo Alto Networks – GlobalProtect

Neste tutorial, você aprenderá a integrar o Palo Alto Networks – GlobalProtect com o Azure Active Directory (Azure AD). Ao integrar o Palo Alto Networks – GlobalProtect com o Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Palo Alto Networks – GlobalProtect.
* Habilite seus usuários a serem conectados automaticamente ao Palo Alto Networks – GlobalProtect com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Palo Alto Networks – GlobalProtect.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Palo Alto Networks – GlobalProtect dá suporte ao SSO iniciado por **SP**
* O Palo Alto Networks – GlobalProtect dá suporte ao provisionamento **de usuário just-in-time**

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Adicionar o Palo Alto Networks – GlobalProtect da Galeria

Para configurar a integração do Palo Alto Networks – GlobalProtect ao Azure AD, você precisa adicionar o Palo Alto Networks – GlobalProtect da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Palo Alto Networks – GlobalProtect** na caixa de pesquisa.
1. Selecione **Palo Alto Networks – GlobalProtect** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-palo-alto-networks---globalprotect"></a>Configurar e testar o logon único do Azure AD para o Palo Alto Networks – GlobalProtect

Configure e teste o SSO do Azure AD com o Palo Alto Networks – GlobalProtect usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Palo Alto Networks – GlobalProtect.

Para configurar e testar o SSO do Azure AD com o Palo Alto Networks – GlobalProtect, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o Palo Alto Networks – GLOBALPROTECT SSO](#configure-palo-alto-networks---globalprotect-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Palo Alto Networks – GlobalProtect](#create-palo-alto-networks---globalprotect-test-user)** – para ter um equivalente de B. Simon no Palo Alto Networks – GlobalProtect que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Palo Alto Networks – GlobalProtect** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://<Customer Firewall URL>`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão:`https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contate a [equipe de suporte ao cliente do Palo Alto Networks – GlobalProtect](https://support.paloaltonetworks.com/support) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar o Palo Alto Networks – GlobalProtect** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao Palo Alto Networks – GlobalProtect.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Palo Alto Networks – GlobalProtect**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-palo-alto-networks---globalprotect-sso"></a>Configurar o Palo Alto Networks – GlobalProtect SSO

1. Abra a interface do usuário do administrador de firewall do Palo Alto Networks como administrador em outra janela do navegador.

2. Clique em **dispositivo**.

    ![Configurar o logon único do Palo Alto](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Selecione **provedor de identidade SAML** na barra de navegação à esquerda e clique em "importar" para importar o arquivo de metadados.

    ![Configurar o logon único do Palo Alto](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Executar as seguintes ações na janela importar

    ![Configurar o logon único do Palo Alto](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. Na caixa de texto **nome do perfil** , forneça um nome, por exemplo, Azure ad GlobalProtect.

    b. Em **metadados do provedor de identidade**, clique em **procurar** e selecione o arquivo Metadata. XML que você baixou de portal do Azure

    c. Clique em **OK**

### <a name="create-palo-alto-networks---globalprotect-test-user"></a>Criar usuário de teste do Palo Alto Networks – GlobalProtect

Nesta seção, um usuário chamado B. Simon é criado no Palo Alto Networks – GlobalProtect. O Palo Alto Networks – GlobalProtect dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no Palo Alto Networks – GlobalProtect, um novo será criado após a autenticação.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco Palo Alto Networks – GlobalProtect no painel de acesso, você deverá ser conectado automaticamente ao Palo Alto Networks – GlobalProtect para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Palo Alto Networks – GlobalProtect com o Azure AD](https://aad.portal.azure.com/)