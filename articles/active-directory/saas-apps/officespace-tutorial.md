---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o OfficeSpace software | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o software OfficeSpace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80712c7f59845287006c1699524573c6094498b3
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561717"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-officespace-software"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o OfficeSpace software

Neste tutorial, você aprenderá a integrar o OfficeSpace software ao Azure Active Directory (Azure AD). Ao integrar o OfficeSpace software ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao OfficeSpace software.
* Habilite seus usuários a serem conectados automaticamente ao OfficeSpace software com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do OfficeSpace software.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O software OfficeSpace dá suporte ao SSO iniciado por **SP**


* O OfficeSpace software dá suporte ao provisionamento **de usuário just-in-time**


## <a name="adding-officespace-software-from-the-gallery"></a>Adicionar o OfficeSpace software da Galeria

Para configurar a integração do OfficeSpace software ao Azure AD, você precisará adicionar o OfficeSpace software da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **OfficeSpace software** na caixa de pesquisa.
1. Selecione **OfficeSpace software** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-officespace-software"></a>Configurar e testar o logon único do Azure AD para o OfficeSpace software

Configure e teste o SSO do Azure AD com o OfficeSpace software usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no OfficeSpace software.

Para configurar e testar o SSO do Azure AD com o OfficeSpace software, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do OfficeSpace software](#configure-officespace-software-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do OfficeSpace software](#create-officespace-software-test-user)** – para ter um equivalente de B. Simon no OfficeSpace software que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **OfficeSpace software** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `<company name>.officespacesoftware.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contate a [equipe de suporte ao cliente do OfficeSpace software](mailto:support@officespacesoftware.com) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. O aplicativo OfficeSpace software espera que as asserções SAML estejam em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, onde o **nameidentifier** é mapeado com **User. UserPrincipalName**. O aplicativo OfficeSpace software espera que o **nameidentifier** seja mapeado com **User. mail**, portanto, você precisa editar o mapeamento de atributo clicando no ícone **Editar** e alterar o mapeamento de atributo.

    ![imagem](common/edit-attribute.png)

1. Além disso, o aplicativo OfficeSpace software espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seu requisito.

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | e-mail | user.mail |
    | nome | user.displayname |
    | first_name | user.givenname |
    | last_name | User. sobrenome |

1. Na seção **certificado de autenticação SAML** , clique no botão **Editar** para abrir a caixa de diálogo **certificado de autenticação SAML** .

    ![Editar certificado de autenticação SAML](common/edit-certificate.png)

1. Na seção **certificado de autenticação SAML** , copie o **valor da impressão digital** e salve-o em seu computador.

    ![Copiar valor de impressão digital](common/copy-thumbprint.png)

1. Na seção **configurar software OfficeSpace** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao OfficeSpace software.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **OfficeSpace software**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="configure-officespace-software-sso"></a>Configurar o SSO do OfficeSpace software

1. Em uma janela diferente do navegador da Web, entre no seu locatário do OfficeSpace software como administrador.

2. Vá para **configurações** e clique em **conectores**.

    ![Configurar o logon único no lado do aplicativo](./media/officespace-tutorial/tutorial_officespace_002.png)

3. Clique em **autenticação SAML**.

    ![Configurar o logon único no lado do aplicativo](./media/officespace-tutorial/tutorial_officespace_003.png)

4. Na seção **autenticação SAML** , execute as seguintes etapas:

    ![Configurar o logon único no lado do aplicativo](./media/officespace-tutorial/tutorial_officespace_004.png)

    a. Na caixa de texto **URL do provedor de logout** , Cole o valor da URL de **logout** que você copiou do portal do Azure.

    b. Na caixa de texto **URL de destino do IDP do cliente** , Cole o valor da URL de **logon** que você copiou do portal do Azure.

    c. Cole o valor de **impressão digital** copiado de portal do Azure na caixa de texto **impressão digital do certificado IDP do cliente** . 

    d. Clique em **salvar configurações**.

### <a name="create-officespace-software-test-user"></a>Criar usuário de teste do OfficeSpace software

Nesta seção, um usuário chamado B. Simon é criado no OfficeSpace software. O OfficeSpace software dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no OfficeSpace software, um novo será criado após a autenticação.

> [!NOTE]
> Se precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do OfficeSpace software](mailto:support@officespacesoftware.com).

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco OfficeSpace software no painel de acesso, você deverá ser conectado automaticamente ao software OfficeSpace para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o OfficeSpace software com o Azure AD](https://aad.portal.azure.com/)

