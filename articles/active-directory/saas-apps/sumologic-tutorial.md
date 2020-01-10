---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o SumoLogic | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SumoLogic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de6d941c6efe42993b6bad7c556582831d179250
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659751"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sumologic"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o SumoLogic

Neste tutorial, você aprenderá a integrar o SumoLogic com o Azure Active Directory (Azure AD). Ao integrar o SumoLogic ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao SumoLogic.
* Habilite seus usuários a serem conectados automaticamente ao SumoLogic com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do SumoLogic.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* SumoLogic dá suporte ao SSO iniciado pelo **IDP**

## <a name="adding-sumologic-from-the-gallery"></a>Adicionando o SumoLogic da Galeria

Para configurar a integração do SumoLogic ao Azure AD, você precisará adicionar o SumoLogic da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **SumoLogic** na caixa de pesquisa.
1. Selecione **SumoLogic** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sumologic"></a>Configurar e testar o logon único do Azure AD para o SumoLogic

Configure e teste o SSO do Azure AD com o SumoLogic usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SumoLogic.

Para configurar e testar o SSO do Azure AD com o SumoLogic, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do SumoLogic](#configure-sumologic-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do SumoLogic](#create-sumologic-test-user)** – para ter um equivalente de B. Simon em SumoLogic que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SumoLogic** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://service.sumologic.com`|
    | `https://<tenantname>.us2.sumologic.com`|
    | `https://<tenantname>.us4.sumologic.com`|
    | `https://<tenantname>.eu.sumologic.com`|
    | `https://<tenantname>.jp.sumologic.com`|
    | `https://<tenantname>.de.sumologic.com`|
    | `https://<tenantname>.ca.sumologic.com`|

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://service.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.us2.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.us4.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.eu.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.jp.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.de.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.ca.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.au.sumologic.com/sumo/saml/consume/<tenantname>`|

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador e a URL de resposta reais. Contate a [equipe de suporte ao cliente do SumoLogic](https://www.sumologic.com/contact-us/) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. O aplicativo SumoLogic espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/default-attributes.png)

1. Além de acima, o aplicativo SumoLogic espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.

    |  Nome | Atributo de origem |
    | ---------------| --------------- |
    | FirstName | user.givenname |
    | Apelido | User. sobrenome |
    | Funções | user.assignedroles |

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) para saber como configurar a **função** no Azure AD.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar SumoLogic** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao SumoLogic.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **SumoLogic**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-sumologic-sso"></a>Configurar o SSO do SumoLogic

1. Em uma janela diferente do navegador da Web, entre no site da empresa do SumoLogic como um administrador.

1. Vá para **Gerenciar segurança de \>** .

    ![Gerir](./media/sumologic-tutorial/ic778556.png "Gerir")

1. Clique em **SAML**.

    ![Configurações globais de segurança](./media/sumologic-tutorial/ic778557.png "Configurações globais de segurança")

1. Na lista **selecionar uma configuração ou criar uma nova** , selecione **Azure ad**e clique em **Configurar**.

    ![Configurar SAML 2,0](./media/sumologic-tutorial/ic778558.png "Configurar SAML 2,0")

1. Na caixa de diálogo **Configurar SAML 2,0** , execute as seguintes etapas:

    ![Configurar SAML 2,0](./media/sumologic-tutorial/ic778559.png "Configurar SAML 2,0")

    a. Na caixa de texto **nome da configuração** , digite **Azure ad**.

    b. Selecione **modo de depuração**.

    c. Na caixa de texto **emissor** , Cole o valor do **identificador do Azure ad**copiado de portal do Azure.

    d. Na caixa de texto **URL da solicitação Authn** , Cole o valor da **URL de logon**copiado de portal do Azure.

    e. Abra seu certificado codificado em base 64 no bloco de notas, copie o conteúdo dele para a área de transferência e cole o certificado inteiro na caixa de texto **certificado X. 509** .

    f. Como **atributo de email**, selecione **usar entidade SAML**.  

    g. Selecione **configuração de logon iniciada pelo SP**.

    h. Na caixa de texto **caminho de logon** , digite **Azure** e clique em **salvar**.

### <a name="create-sumologic-test-user"></a>Criar usuário de teste do SumoLogic

Para permitir que os usuários do AD do Azure entrem no SumoLogic, eles devem ser provisionados no SumoLogic. No caso do SumoLogic, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no seu locatário do **SumoLogic** .

1. Vá para **gerenciar \> usuários**.

    ![Utilizadores](./media/sumologic-tutorial/ic778561.png "Utilizadores")

1. Clique em **Adicionar**.

    ![Utilizadores](./media/sumologic-tutorial/ic778562.png "Utilizadores")

1. Na caixa de diálogo **novo usuário** , execute as seguintes etapas:

    ![Novo usuário](./media/sumologic-tutorial/ic778563.png "Novo Utilizador")

    a. Digite as informações relacionadas da conta do Azure AD que você deseja provisionar nas caixas de texto **nome**, **sobrenome**e **email** .
  
    b. Selecione uma função.
  
    c. Em **status**, selecione **ativo**.
  
    d. Clique em **Guardar**.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação de conta de usuário SumoLogic ou APIs fornecidas pelo SumoLogic para provisionar contas de usuário do Azure AD.

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do SumoLogic no painel de acesso, você deverá ser conectado automaticamente ao SumoLogic para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o SumoLogic com o Azure AD](https://aad.portal.azure.com/)