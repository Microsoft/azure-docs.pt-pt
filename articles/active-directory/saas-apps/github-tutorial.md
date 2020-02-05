---
title: 'Tutorial: integração do Azure Active Directory com o GitHub | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ddc2622a67979f989c030d615c402387e7762d5
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983491"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o GitHub

Neste tutorial, você aprenderá a integrar o GitHub ao Azure Active Directory (Azure AD). Ao integrar o GitHub ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao GitHub.
* Habilite seus usuários a serem conectados automaticamente ao GitHub com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o GitHub, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma organização do GitHub criada no [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise), que requer o [plano de cobrança do GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O GitHub dá suporte ao SSO iniciado pelo **SP**

* O GitHub dá suporte ao [provisionamento **automatizado** de usuários](github-provisioning-tutorial.md)
* Assim que configurar o GitHub, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controle de sessão com Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-github-from-the-gallery"></a>Adicionando o GitHub da Galeria

Para configurar a integração do GitHub ao Azure AD, você precisará adicionar o GitHub da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **GitHub** na caixa de pesquisa.
1. Selecione **GitHub** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-github"></a>Configurar e testar o logon único do Azure AD para o GitHub

Configure e teste o SSO do Azure AD com o GitHub usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no GitHub.

Para configurar e testar o SSO do Azure AD com o GitHub, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar SSO do GitHub](#configure-github-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do GitHub](#create-github-test-user)** – para ter um equivalente de B. Simon no GitHub que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **GitHub** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

   a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://github.com/orgs/<entity-id>/sso`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Observe que esses não são os valores reais. Você precisa atualizar esses valores com a URL de entrada e o identificador reais. Aqui, sugerimos que você use o valor exclusivo da cadeia de caracteres no identificador. Acesse a seção de administração do GitHub para recuperar esses valores.

5. Seu aplicativo GitHub espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, onde o **nameidentifier** é mapeado com **User. UserPrincipalName**. O aplicativo GitHub espera que **nameidentifier** seja mapeado com **User. mail**, portanto, você precisa editar o mapeamento de atributo clicando no ícone **Editar** e alterar o mapeamento de atributo.

    ![imagem](common/edit-attribute.png)

6. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download de certificado](common/certificatebase64.png)

7. Na seção **Configurar o GitHub** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao GitHub.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **GitHub**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-github-sso"></a>Configurar SSO do GitHub

1. Em uma janela diferente do navegador da Web, faça logon em seu site de organização do GitHub como administrador.

2. Navegue até **configurações** e clique em **segurança**

    ![Definições](./media/github-tutorial/tutorial_github_config_github_03.png)

3. Marque a caixa **habilitar autenticação SAML** , revelando os campos de configuração de logon único. Em seguida, use o valor da URL de logon único para atualizar a URL de logon único na configuração do Azure AD.

    ![Definições](./media/github-tutorial/tutorial_github_config_github_13.png)

4. Configure os seguintes campos:

    ![Definições](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. Na caixa de texto **URL de logon** , Cole o valor da **URL de logon** que você copiou do portal do Azure.

    b. Na caixa de texto **emissor** , Cole o valor do **identificador do Azure ad** copiado do portal do Azure.

    c. Abra o certificado baixado de portal do Azure no bloco de notas, Cole o conteúdo na caixa de texto **certificado público** .

    d. Clique no ícone **Editar** para editar o **método de assinatura** e o método de **Resumo** de **RSA-SHA1** e **SHA1** para **RSA-SHA256** e **SHA256** , conforme mostrado abaixo.

    ![imagem](./media/github-tutorial/tutorial_github_sha.png)

5. Clique em **testar configuração do SAML** para confirmar que não há falhas de validação ou erros durante o SSO.

    ![Definições](./media/github-tutorial/tutorial_github_config_github_06.png)

6. Clicar em **Guardar**

> [!NOTE]
> O logon único no GitHub é autenticado em uma organização específica no GitHub e não substitui a autenticação do próprio GitHub. Portanto, se a sessão github.com do usuário tiver expirado, você poderá ser solicitado a autenticar com a ID/senha do GitHub durante o processo de logon único.

### <a name="create-github-test-user"></a>Criar usuário de teste do GitHub

O objetivo desta seção é criar um usuário chamado Brenda Simon no GitHub. O GitHub dá suporte ao provisionamento automático de usuário, que está habilitado por padrão. Você pode encontrar mais detalhes [aqui](github-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuário.

**Se você precisar criar o usuário manualmente, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do GitHub como administrador.

2. Clique em **pessoas**.

    ![Porta](./media/github-tutorial/tutorial_github_config_github_08.png "Pessoas")

3. Clique em **convidar membro**.

    ![Convidar usuários](./media/github-tutorial/tutorial_github_config_github_09.png "Convidar usuários")

4. Na página da caixa de diálogo **convidar membro** , execute as seguintes etapas:

    a. Na caixa de texto **email** , digite o endereço de email da conta Brenda Simon.

    ![Convidar pessoas](./media/github-tutorial/tutorial_github_config_github_10.png "Convidar pessoas")

    b. Clique em **Enviar convite**.

    ![Convidar pessoas](./media/github-tutorial/tutorial_github_config_github_11.png "Convidar pessoas")

    > [!NOTE]
    > O titular da conta Azure Active Directory receberá um email e seguirá um link para confirmar sua conta antes que ela se torne ativa.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do GitHub no painel de acesso, você deverá ser conectado automaticamente ao GitHub para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o GitHub com o Azure AD](https://aad.portal.azure.com/)

- [O que é o controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)