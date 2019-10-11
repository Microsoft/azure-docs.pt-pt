---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com benefícios de descoberta SSO | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e benefícios de descoberta SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a788cd07-0eed-4067-b79d-19b840e8836d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64c4a6811ef5d7ed4f29c7dae89561616895a42a
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72266144"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-discovery-benefits-sso"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com benefícios de descoberta SSO

Neste tutorial, você aprenderá a integrar os benefícios de descoberta SSO com o Azure Active Directory (Azure AD). Ao integrar benefícios de descoberta SSO com o Azure AD, você pode:

* Controle no Azure AD que tem acesso aos benefícios de descoberta SSO.
* Habilite seus usuários a serem conectados automaticamente à descoberta beneficia o SSO com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* A descoberta beneficia a assinatura habilitada para SSO (logon único) do SSO.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Benefícios da descoberta o SSO dá suporte ao SSO iniciado pelo **IDP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-discovery-benefits-sso-from-the-gallery"></a>Adicionando o SSO dos benefícios da descoberta da Galeria

Para configurar a integração dos benefícios de descoberta do SSO no Azure AD, você precisa adicionar o SSO de benefícios de descoberta da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite SSO de **benefícios da descoberta** na caixa de pesquisa.
1. Selecione **benefícios de descoberta SSO** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-discovery-benefits-sso"></a>Configurar e testar o logon único do Azure AD para benefícios de descoberta SSO

Configure e teste o SSO do Azure AD com a descoberta beneficia o SSO usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em benefícios de descoberta SSO.

Para configurar e testar o SSO do Azure AD com o SSO de benefícios de descoberta, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar benefícios da descoberta](#configure-discovery-benefits-sso-sso)** SSO SSO – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do SSO de benefícios de descoberta](#create-discovery-benefits-sso-test-user)** – para ter um equivalente de B. Simon na descoberta beneficia o SSO que está vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SSO dos benefícios da descoberta** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na **configuração básica do SAML** section o aplicativo é pré-configurado no modo  initiated **IDP**e as URLs necessárias já estão preenchidas previamente com o Azure. O usuário precisa salvar a configuração clicando em **salvar** button.

1. Benefícios de descoberta o aplicativo SSO espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo atributos de usuário.

    ![imagem](common/edit-attribute.png)

    a. Clique no ícone **Editar** para abrir a caixa de diálogo **identificador de usuário exclusivo (ID de nome)** .

    ![Configuração de SSO dos benefícios da descoberta](./media/discovery-benefits-sso-tutorial/attribute01.png)

    ![Configuração de SSO dos benefícios da descoberta](./media/discovery-benefits-sso-tutorial/attribute02.png)

    b. Clique no ícone **Editar** para abrir a caixa de diálogo **gerenciar transformação** .

    c. Na caixa de texto **transformação** , digite o **topercase ()** mostrado para essa linha.

    d. Na caixa de texto **parâmetro 1** , digite o parâmetro como `<Name Identifier value>`.

    e. Clique em **Adicionar**.

    > [!NOTE]
    > Benefícios da descoberta o SSO requer que um valor de cadeia de caracteres fixo seja passado no campo **exclusivo do identificador de usuário (ID)** para fazer com que essa integração funcione. Atualmente, o Azure AD não dá suporte a esse recurso, assim como uma solução alternativa, você pode usar transformações **ToUpper** ou **ToLower** de NameID para definir um valor de cadeia de caracteres fixo, conforme mostrado acima na captura de tela.

    f. Nós populamos automaticamente as declarações adicionais que são necessárias para a configuração de SSO (`SSOInstance` e `SSOID`). Use o ícone **Editar** para mapear os valores de acordo com sua organização.

    ![Configuração de SSO dos benefícios da descoberta](./media/discovery-benefits-sso-tutorial/attribute03.png)

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/certificatebase64.png)

1. Na seção **Configurar o SSO de benefícios da descoberta** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso aos benefícios de descoberta SSO.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **benefícios de descoberta SSO**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-discovery-benefits-sso-sso"></a>Configurar benefícios de descoberta SSO do SSO

Para configurar o logon único em **benefícios de descoberta** , o lado do SSO, você precisa enviar o **certificado (Base64)** baixado e as URLs copiadas apropriadas de portal do Azure para a [equipe de suporte do SSO dos benefícios da descoberta](mailto:Jsimpson@DiscoveryBenefits.com). Eles definem essa configuração para que a conexão de SSO do SAML seja definida corretamente em ambos os lados.

### <a name="create-discovery-benefits-sso-test-user"></a>Criar benefícios de descoberta usuário de teste do SSO

Nesta seção, você criará um usuário chamado Brenda Simon nos benefícios de descoberta SSO. Trabalhe com a [equipe de suporte do SSO dos benefícios da descoberta](mailto:Jsimpson@DiscoveryBenefits.com) para adicionar os usuários na plataforma de SSO benefícios da descoberta. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco de SSO benefícios de descoberta no painel de acesso, você deverá ser conectado automaticamente ao SSO benefícios da descoberta para o qual você configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimentar os benefícios de descoberta SSO com o Azure AD](https://aad.portal.azure.com/)

