---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Kanbanize | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e o Kanban.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b436d2f6-bfa5-43fd-a8f9-d2144dc25669
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c5a3a096c5a44f681d23587837ae31fd1af33b2
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373218"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kanbanize"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Kanbanize

Neste tutorial, você aprenderá a integrar os Kanbans ao Azure Active Directory (Azure AD). Ao integrar o Kanbanize ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Kanbanize.
* Habilite seus usuários a entrarem automaticamente no Kanban com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Preparar assinatura habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Kanban suporta o **SP e** o SSO iniciado pelo IDP
* O Kanban dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-kanbanize-from-the-gallery"></a>Adicionando o separador de Kanban da Galeria

Para configurar a integração do previsibilidade para o Azure AD, você precisa adicionar o Kanban da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **kanbanize** na caixa de pesquisa.
1. Selecione **kanbanize** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-kanbanize"></a>Configurar e testar o logon único do Azure AD para o Kanbanize

Configure e teste o SSO do Azure AD com o uso de Kanban usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em Kanbanize.

Para configurar e testar o SSO do Azure AD com o Kanban, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar kanbanize o SSO](#configure-kanbanize-sso)** -para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do prefiler](#create-kanbanize-test-user)** – para ter um equivalente de B. Simon em kanbanize que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativo de **Kanban** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

     a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://<subdomain>.kanbanize.com/`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<subdomain>.kanbanize.com/saml/acs`

    c. Clique em **definir URLs adicionais**.

    d. Na caixa de texto **estado de retransmissão** , digite uma URL: `/ctrl_login/saml_login`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<subdomain>.kanbanize.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Contate a [equipe de suporte ao cliente de kanbanize](mailto:support@ms.kanbanize.com) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. O aplicativo de kanbanize espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, onde o nameidentifier é mapeado com **User. UserPrincipalName**. O aplicativo de kanbanize espera que nameidentifier seja mapeado com **User. mail**, portanto, você precisa editar o mapeamento de atributo clicando no ícone de edição e alterar o mapeamento de atributo.

    ![imagem](common/edit-attribute.png)

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/certificatebase64.png)

1. Na seção **Configurar o kanbanize** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao Kanbanize.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **kanbanize**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-kanbanize-sso"></a>Configurar o SSO do Kanban

1. Para automatizar a configuração no Kanban, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar o kanbanize** o direcionará para o aplicativo de Kanban. A partir daí, forneça as credenciais de administrador para entrar em Kanbanize. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-7.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar o Kanban manualmente, abra uma nova janela do navegador da Web e entre no site de sua empresa de Kanban como administrador e execute as seguintes etapas:

4. Vá para o canto superior direito da página, clique no logotipo **configurações** .

    ![Configurações de Kanban](./media/kanbanize-tutorial/tutorial-kanbanize-set.png)

5. Na página painel de administração, no lado esquerdo do menu, clique em **integrações** e habilite o **logon único**.

    ![Preparações de Kanban](./media/kanbanize-tutorial/tutorial-kanbanize-admin.png)

6. Na seção integrações, clique em **Configurar** para abrir a página de **integração de logon único** .

    ![Configuração de kanbanize](./media/kanbanize-tutorial/tutorial-kanbanize-config.png)

7. Na página de **integração logon único** em **configurações**, execute as seguintes etapas:

    ![Preparações de Kanban](./media/kanbanize-tutorial/tutorial-kanbanize-save.png)

    a. Na caixa de texto **ID da entidade do IDP** , Cole o valor do identificador do **Azure ad**copiado do portal do Azure.

    b. Na caixa de texto **ponto de extremidade de logon do IDP** , Cole o valor da URL de **logon**copiado do portal do Azure.

    c. Na caixa de texto **ponto de extremidade de logout do IDP** , Cole o valor da URL de **logout**copiado do portal do Azure.

    d. Na caixa **de texto nome do atributo para email** , insira esse valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. Na caixa **de texto nome do atributo para nome** , insira este valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. Na caixa **de texto nome do atributo para sobrenome** , insira esse valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    > [!Note]
    > Você pode obter esses valores combinando os valores de namespace e nome do respectivo atributo da seção atributos de usuário em portal do Azure.

    g. No bloco de notas, abra o certificado codificado base 64 que você baixou do portal do Azure, copie seu conteúdo (sem os marcadores de início e término) e cole-o na caixa **certificado IDP X. 509** .

    h. Marque **habilitar logon com SSO e kanbanize**.

    i. Clique em **salvar configurações**.

### <a name="create-kanbanize-test-user"></a>Criar usuário de teste do Kanbanize

Nesta seção, um usuário chamado B. Simon é criado em Kanbanize. O Kanban dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no Kanban, um novo será criado após a autenticação. Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte ao cliente de Kanban](mailto:support@ms.kanbanize.com).

## <a name="test-sso"></a>Testar SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco de Kanban no painel de acesso, você deverá ser conectado automaticamente ao Kanban para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Kanbanize com o Azure AD](https://aad.portal.azure.com/)

