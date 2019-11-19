---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o TINFOIL SECURITY | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TINFOIL SECURITY.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71d3078b553843922cd51e4e0f43ea84b6dcde16
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170770"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tinfoil-security"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o TINFOIL SECURITY

Neste tutorial, você aprenderá a integrar o TINFOIL SECURITY ao Azure Active Directory (Azure AD). Ao integrar o TINFOIL SECURITY ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao TINFOIL SECURITY.
* Habilite seus usuários a serem conectados automaticamente ao TINFOIL SECURITY com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do TINFOIL SECURITY.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O TINFOIL SECURITY dá suporte ao SSO iniciado pelo **IDP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-tinfoil-security-from-the-gallery"></a>Adicionando o TINFOIL SECURITY da Galeria

Para configurar a integração do TINFOIL SECURITY com o Azure AD, você precisará adicionar o TINFOIL SECURITY da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **TINFOIL Security** na caixa de pesquisa.
1. Selecione **TINFOIL Security** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tinfoil-security"></a>Configurar e testar o logon único do Azure AD para o TINFOIL SECURITY

Configure e teste o SSO do Azure AD com o TINFOIL SECURITY usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no TINFOIL SECURITY.

Para configurar e testar o SSO do Azure AD com o TINFOIL SECURITY, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do TINFOIL Security](#configure-tinfoil-security-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do TINFOIL Security](#create-tinfoil-security-test-user)** – para ter um equivalente de B. Simon no TINFOIL Security que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **TINFOIL Security** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , o aplicativo é pré-configurado e as URLs necessárias já foram preenchidas previamente com o Azure. O usuário precisa salvar a configuração clicando no botão **salvar** .

1. O aplicativo de visita espera que as asserções SAML estejam em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além de acima, o aplicativo visitado espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de origem |
    | ------------------- | -------------|
    | AccountId | UXXXXXXXXXXXXX |

    > [!NOTE]
    > Você obterá o valor de AccountId explicado posteriormente no tutorial.

1. Na seção **certificado de autenticação SAML** , clique no botão **Editar** para abrir a caixa de diálogo **certificado de autenticação SAML** .

    ![Editar certificado de autenticação SAML](common/edit-certificate.png)

1. Na seção **certificado de autenticação SAML** , copie o **valor da impressão digital** e salve-o em seu computador.

    ![Copiar valor de impressão digital](common/copy-thumbprint.png)

1. Na seção **Configurar o TINFOIL Security** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao TINFOIL SECURITY.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **TINFOIL Security**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-tinfoil-security-sso"></a>Configurar SSO do TINFOIL SECURITY

1. Em uma janela diferente do navegador da Web, entre no site da empresa do TINFOIL SECURITY como administrador.

1. Na barra de ferramentas na parte superior, clique em **minha conta**.

    ![Dashboard](./media/tinfoil-security-tutorial/ic798971.png "Dashboard")

1. Clique em **segurança**.

    ![Segurança](./media/tinfoil-security-tutorial/ic798972.png "Segurança")

1. Na página de configuração **logon único** , execute as seguintes etapas:

    ![Logon único](./media/tinfoil-security-tutorial/ic798973.png "Logon único")

    a. Selecione **habilitar SAML**.

    b. Clique em **configuração manual**.

    c. Na caixa de texto **URL de postagem SAML** , Cole o valor da **URL de logon** que você copiou do portal do Azure

    d. Na caixa de texto **impressão digital do certificado SAML** , Cole o valor da **impressão digital** que você copiou da seção **certificado de autenticação SAML** .
  
    e. Copie o valor da **ID da conta** e cole o valor na caixa de texto **atributo de origem** em **atributos de usuário & seção declarações** em portal do Azure.

    f. Clique em **Guardar**.

### <a name="create-tinfoil-security-test-user"></a>Criar usuário de teste do TINFOIL SECURITY

Para permitir que os usuários do AD do Azure entrem no TINFOIL SECURITY, eles devem ser provisionados no TINFOIL SECURITY. No caso do TINFOIL SECURITY, o provisionamento é uma tarefa manual.

**Para obter um usuário provisionado, execute as seguintes etapas:**

1. Se o usuário fizer parte de uma conta empresarial, você precisará [entrar em contato com a equipe de suporte do TINFOIL Security](https://www.tinfoilsecurity.com/contact) para obter a conta de usuário criada.

1. Se o usuário for um usuário de SaaS do TINFOIL SECURITY regular, o usuário poderá adicionar um colaborador a qualquer um dos sites do usuário. Isso dispara um processo para enviar um convite ao email especificado para criar uma nova conta de usuário do TINFOIL SECURITY.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação de conta de usuário do TINFOIL SECURITY ou APIs fornecidas pelo TINFOIL SECURITY para provisionar contas de usuário do Azure AD.

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do TINFOIL SECURITY no painel de acesso, você deverá entrar automaticamente no TINFOIL SECURITY para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o TINFOIL SECURITY com o Azure AD](https://aad.portal.azure.com/)