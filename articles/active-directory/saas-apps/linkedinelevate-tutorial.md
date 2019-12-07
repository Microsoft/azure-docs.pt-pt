---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o LinkedIn Elevate | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o LinkedIn Elevate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03efae5f9dec904f141a6776766850aa1f328892
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892147"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-elevate"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o LinkedIn Elevate

Neste tutorial, você aprenderá a integrar o LinkedIn Elevate ao Azure Active Directory (Azure AD). Ao integrar o LinkedIn Elevate ao Azure AD, você pode:

* Controle no Azure AD que tem acesso ao LinkedIn Elevate.
* Habilite seus usuários a entrar automaticamente no LinkedIn Elevate com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do LinkedIn Elevate.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.



* O LinkedIn Elevate dá suporte a SP iniciado por **controlador de logon e IDP**
* O LinkedIn Elevate dá suporte ao provisionamento **de usuário just in time**
* O LinkedIn Elevate dá suporte ao [provisionamento **automatizado** de usuários](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Adicionando o LinkedIn Elevate da Galeria

Para configurar a integração do LinkedIn Elevate ao Azure AD, você precisará adicionar o LinkedIn Elevate da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **LinkedIn Elevate** na caixa de pesquisa.
1. Selecione **LinkedIn Elevate** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-elevate"></a>Configurar e testar o logon único do Azure AD para o LinkedIn Elevate

Configure e teste o SSO do Azure AD com o LinkedIn Elevate usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no LinkedIn Elevate.

Para configurar e testar o SSO do Azure AD com o LinkedIn Elevate, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o LinkedIn Elevate SSO](#configure-linkedin-elevate-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do LinkedIn Elevate](#create-linkedin-elevate-test-user)** – para ter um equivalente de B. Simon no LinkedIn Elevate que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **LinkedIn Elevate** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , insira o valor **ID da entidade** , você copiará o valor da ID da entidade no portal do LinkedIn explicado posteriormente neste tutorial.

    b. Na caixa de texto **URL de resposta** , insira o valor da URL de acesso do **consumidor de asserção (ACS)** , você copiará o valor da URL do ACS (acesso do consumidor de asserção) do portal do LinkedIn explicado posteriormente neste tutorial.

5. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

1. O aplicativo LinkedIn Elevate espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, onde o **nameidentifier** é mapeado com **User. UserPrincipalName**. O aplicativo LinkedIn Elevate espera que nameidentifier seja mapeado com **User. mail**, portanto, você precisa editar o mapeamento de atributo clicando no ícone Editar e alterar o mapeamento de atributo.

    ![imagem](common/edit-attribute.png)

1. Além de acima, o aplicativo LinkedIn Elevate espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seu requisito.

    | Nome | Atributo de origem|
    | -------| -------------|
    | Departamento | user.department |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar o LinkedIn Elevate** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao LinkedIn Elevate.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **LinkedIn Elevate**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-linkedin-elevate-sso"></a>Configurar SSO de elevação do LinkedIn

1. Em uma janela diferente do navegador da Web, faça logon em seu locatário do LinkedIn Elevate como administrador.

1. No **centro de contas**, clique em **configurações globais** em **configurações**. Além disso, selecione **elevar – elevar o teste do AAD** na lista suspensa.

    ![Configurar o início de sessão único](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Clique **ou clique aqui para carregar e copiar campos individuais do formulário** e execute as seguintes etapas:

    ![Configurar o início de sessão único](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. Copie a **ID da entidade** e cole-a na caixa de texto **identificador** na **configuração básica do SAML** no portal do Azure.

    b. Copie a **URL do ACS (acesso do consumidor de asserção)** e cole-a na caixa de texto **URL de resposta** na **configuração básica do SAML** no portal do Azure.

1. Acesse a seção **configurações de administração do LinkedIn** . Carregue o arquivo XML que você baixou do portal do Azure clicando na opção carregar arquivo XML.

    ![Configurar o início de sessão único](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Clique **em** ativar para habilitar o SSO. O status do SSO será alterado de **não conectado** para **conectado**

    ![Configurar o início de sessão único](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-elevate-test-user"></a>Criar usuário de teste do LinkedIn Elevate

O aplicativo LinkedIn Elevate dá suporte ao provisionamento de usuário just-in-time e, após a autenticação, os usuários serão criados no aplicativo automaticamente. Na página Configurações de administrador no portal do LinkedIn Elevate, inverta a opção **atribuir licenças automaticamente** ao provisionamento ativo just in time e isso também atribuirá uma licença ao usuário. O LinkedIn Elevate também dá suporte ao provisionamento automático de usuário. você pode encontrar mais detalhes [aqui](linkedinelevate-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuário.

   ![Criar um utilizador de teste do Azure AD](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do LinkedIn Elevate no painel de acesso, você deverá ser conectado automaticamente ao LinkedIn Elevate para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o LinkedIn Elevate com o Azure AD](https://aad.portal.azure.com/)

