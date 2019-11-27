---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Prontoi | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Prontoi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2469745edb5b8b3696478603cfe874bcabc8c1ff
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231956"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Prontoi

Neste tutorial, você aprenderá a integrar o Prontoi com o Azure Active Directory (Azure AD). Ao integrar o "pronto" ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao "pronto".
* Habilite seus usuários para entrarem automaticamente no Prontoi com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Prontoi dá suporte ao SSO iniciado pelo **IDP**

## <a name="adding-boomi-from-the-gallery"></a>Adicionando o "Prontoi da Galeria"

Para configurar a integração do entanto com o Azure AD, você precisa adicionar o "Prontoi" da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **prontoi** na caixa de pesquisa.
1. Selecione **prontoi** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-boomi"></a>Configurar e testar o logon único do Azure AD para o Prontoi

Configure e teste o SSO do Azure AD com o "pronto" usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em "pronto".

Para configurar e testar o SSO do Azure AD com o Prontoi, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar](#configure-boomi-sso)** o "SSO" para definir as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do prontoi](#create-boomi-test-user)** – para ter um equivalente de B. Simon em pronto, que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo de **prontoi** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL: `https://platform.boomi.com/`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://platform.boomi.com/sso/<boomi-tenant>/saml`

    > [!NOTE]
    > O valor da URL de resposta não é real. Atualize o valor com a URL de resposta real. Contate a [equipe de suporte ao cliente](https://boomi.com/company/contact/) do entanto para obter esse valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. O aplicativo de prontoi espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além de acima, o aplicativo Prontoi espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.

    | Nome |  Atributo de origem|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar o pronto para** copiar, copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao "pronto".

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **prontoi**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-boomi-sso"></a>Configurar o SSO do pronto para

1. Em uma janela diferente do navegador da Web, entre no site da sua empresa pronto para ser um administrador.

1. Navegue até **nome da empresa** e vá para **Configurar**.

1. Clique na guia **Opções de SSO** e execute as etapas a seguir.

    ![Configurar o logon único no lado do aplicativo](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Marque a caixa de seleção **habilitar logon único do SAML** .

    b. Clique em **importar** para carregar o certificado baixado do Azure ad para o **certificado do provedor de identidade**.

    c. Na caixa de texto **URL de logon do provedor de identidade** , coloque o valor da URL de **logon** da janela de configuração de aplicativo do Azure AD.

    d. Como **local da ID da Federação**, selecione a **ID da Federação está no botão de opção FEDERATION_ID elemento de atributo** .

    e. Clique no botão **salvar** .

### <a name="create-boomi-test-user"></a>Criar usuário de teste do Prontoi

Para permitir que os usuários do AD do Azure entrem no entanto, eles devem ser provisionados no "pronto". No caso do "pronto", o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para provisionar uma conta de usuário, execute as seguintes etapas:

1. Entre no site da empresa pronto para participar como administrador.

1. Depois de fazer logon, navegue até **Gerenciamento de usuários** e vá para **usuários**.

    ![Utilizadores](./media/boomi-tutorial/tutorial_boomi_001.png "Utilizadores")

1. Clique no ícone **+** e a caixa de diálogo **Adicionar/manter funções de usuário** será aberta.

    ![Utilizadores](./media/boomi-tutorial/tutorial_boomi_002.png "Utilizadores")

    ![Utilizadores](./media/boomi-tutorial/tutorial_boomi_003.png "Utilizadores")

    a. Na caixa de texto **endereço de email do usuário** , digite o email do usuário, como B.Simon@contoso.com.

    b. Na caixa de texto **nome** , digite o nome do usuário, como B.

    c. Na caixa de texto **sobrenome** , digite o sobrenome do usuário, como Simon.

    d. Insira a ID da **Federação**do usuário. Cada usuário deve ter uma ID de Federação que identifique exclusivamente o usuário na conta.

    e. Atribua a função de **usuário padrão** ao usuário. Não atribua a função de administrador, pois isso daria acesso normal à atmosfera, bem como ao acesso de logon único.

    f. Clique em **OK**.

    > [!NOTE]
    > O usuário não receberá um email de notificação de boas-vindas contendo uma senha que pode ser usada para fazer logon na conta do AtomSphere porque sua senha é gerenciada por meio do provedor de identidade. Você pode usar qualquer outra ferramenta de criação de conta de usuário pronto para uso ou APIs fornecidas por "pronto para" para provisionar contas de usuário do Azure AD.

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco "pronto" no painel de acesso, você deve entrar automaticamente no pronto para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o "pronto para usar" com o Azure AD](https://aad.portal.azure.com/)