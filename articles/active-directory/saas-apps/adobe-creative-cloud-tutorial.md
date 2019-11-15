---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Adobe Creative Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 815cffab118f6900c1c9d42a7e44821f8af62532
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081976"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Adobe Creative Cloud

Neste tutorial, você aprenderá a integrar o Adobe Creative Cloud com o Azure Active Directory (AD do Azure). Ao integrar o Adobe Creative Cloud ao Azure AD, você pode:

* Controle no Azure AD que tem acesso ao Adobe Creative Cloud.
* Habilite seus usuários a serem conectados automaticamente ao Adobe Creative Cloud com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Adobe Creative Cloud.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Adobe Creative Cloud dá suporte ao SSO iniciado pelo **SP**





## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Adicionando Adobe Creative Cloud da Galeria

Para configurar a integração do Adobe Creative Cloud ao Azure AD, você precisará adicionar o Adobe Creative Cloud da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Adobe Creative Cloud** na caixa de pesquisa.
1. Selecione **Adobe Creative Cloud** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-adobe-creative-cloud"></a>Configurar e testar o logon único do Azure AD para o Adobe Creative Cloud

Configure e teste o SSO do Azure AD com o Adobe Creative Cloud usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Adobe Creative Cloud.

Para configurar e testar o SSO do Azure AD com o Adobe Creative Cloud, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar Adobe Creative Cloud SSO](#configure-adobe-creative-cloud-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar Adobe Creative Cloud usuário de teste](#create-adobe-creative-cloud-test-user)** – para ter um equivalente de B. Simon em Adobe Creative Cloud que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Adobe Creative Cloud** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL: `https://adobe.com`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > O valor do identificador não é real. Atualize esse valor com o identificador real. Contate a [equipe de suporte ao cliente do Adobe Creative Cloud](https://www.adobe.com/au/creativecloud/business/teams/plans.html) para obter esse valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Adobe Creative Cloud aplicativo espera que as asserções SAML estejam em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/edit-attribute.png)

1. Além de acima, Adobe Creative Cloud aplicativo espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seu requisito.

    | Nome | Atributo de origem|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | User. sobrenome |
    | Email | user.mail |

    > [!NOTE]
    > Os usuários precisam ter uma licença válida do Office 365 ExO para que o valor de declaração de email seja preenchido na resposta SAML.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **configurar Adobe Creative Cloud** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao Adobe Creative Cloud.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Adobe Creative Cloud**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-adobe-creative-cloud-sso"></a>Configurar Adobe Creative Cloud SSO

1. Em uma janela diferente do navegador da Web, entre no [console do administrador do Adobe](https://adminconsole.adobe.com) como administrador.

2. Vá para **configurações** na barra de navegação superior e escolha **identidade**. A lista de domínios é aberta. Clique em **Configurar** link em seu domínio. Em seguida, execute as etapas a seguir na seção **configuração de logon único necessária** . Para obter mais informações, consulte [configurar um domínio](https://helpx.adobe.com/enterprise/using/set-up-domain.html)

    ![Definições](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "Definições")

    a. Clique em **procurar** para carregar o certificado baixado do Azure ad para o **certificado IDP**.

    b. Na caixa de texto **emissor IDP** , Cole o valor do **identificador do Azure ad** que você copiou do portal do Azure.

    c. Na caixa de texto **URL de logon do IDP** , Cole o valor da URL de **logon** que você copiou do portal do Azure.

    d. Selecione **http-redirecionar** como **Associação IDP**.

    e. Selecione **endereço de email** como **configuração de logon do usuário**.

    f. Clique em **guardar** botão.

3. Agora, o painel apresentará o arquivo XML **"baixar metadados"** . Ele contém a URL do EntityDescriptor da Adobe e a URL do AssertionConsumerService. Abra o arquivo e configure-os no aplicativo do Azure AD.

    ![Configurar o logon único no lado do aplicativo](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Use o valor EntityDescriptor Adobe fornecido para o **identificador** na caixa de diálogo **definir configurações do aplicativo** .

    b. Use o valor AssertionConsumerService Adobe fornecido para a **URL de resposta** na caixa de diálogo **definir configurações do aplicativo** .

### <a name="create-adobe-creative-cloud-test-user"></a>Criar Adobe Creative Cloud usuário de teste

Para permitir que os usuários do AD do Azure entrem no Adobe Creative Cloud, eles devem ser provisionados no Adobe Creative Cloud. No caso do Adobe Creative Cloud, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:

1. Entre no site do [console do administrador do Adobe](https://adminconsole.adobe.com) como administrador.

2. Adicione o usuário no console da Adobe como ID federada e atribua-os a um perfil de produto. Para obter informações detalhadas sobre como adicionar usuários, consulte [Adicionar usuários no console de administração do Adobe](https://helpx.adobe.com/enterprise/using/users.html#Addusers) 

3. Neste ponto, digite seu endereço de email/UPN no formulário de entrada do Adobe, pressione TAB e você deve ser federado de volta ao Azure AD:
   * Acesso via Web: www\.adobe.com > entrar
   * No utilitário de aplicativo da área de trabalho > entrar
   * No aplicativo > Ajuda > entrar

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco Adobe Creative Cloud no painel de acesso, você deverá ser conectado automaticamente à Adobe Creative Cloud para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Adobe Creative Cloud com o Azure AD](https://aad.portal.azure.com/)

- [Configurar um domínio (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
  
- [Configurar o Azure para uso com o Adobe SSO (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

