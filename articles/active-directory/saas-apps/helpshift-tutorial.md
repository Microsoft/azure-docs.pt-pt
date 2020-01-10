---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Helpshift | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Helpshift.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 114de95d-e9a7-4f87-b14d-54b91a63ce49
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f7e932a3b71e802c5b814f6dfb59922148c2519
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75533875"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-helpshift"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Helpshift

Neste tutorial, você aprenderá a integrar o Helpshift com o Azure Active Directory (Azure AD). Ao integrar o Helpshift ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Helpshift.
* Habilite seus usuários a serem conectados automaticamente ao Helpshift com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Helpshift.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Helpshift dá suporte ao **SP e** ao SSO iniciado pelo IDP

## <a name="adding-helpshift-from-the-gallery"></a>Adicionando o Helpshift da Galeria

Para configurar a integração do Helpshift ao Azure AD, você precisará adicionar o Helpshift da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Helpshift** na caixa de pesquisa.
1. Selecione **Helpshift** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-helpshift"></a>Configurar e testar o logon único do Azure AD para o Helpshift

Configure e teste o SSO do Azure AD com o Helpshift usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Helpshift.

Para configurar e testar o SSO do Azure AD com o Helpshift, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do Helpshift](#configure-helpshift-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do Helpshift](#create-helpshift-test-user)** – para ter um equivalente de B. Simon em Helpshift que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Helpshift** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://<YourDOMAIN>.helpshift.com/`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<YourDOMAIN>.helpshift.com/login/saml/acs/`

1. Clique em **definir URLs adicionais** e execute as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    d. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<YourDOMAIN>.helpshift.com/login/saml/idp-login/`

    e. Na caixa de texto **estado de retransmissão** , digite uma URL usando o seguinte padrão: `https://<YourDOMAIN>.helpshift.com/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta, a URL de logon e o estado de retransmissão reais. Contate a [equipe de suporte ao cliente do Helpshift](mailto:support@helpshift.com) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar Helpshift** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao Helpshift.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Helpshift**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-helpshift-sso"></a>Configurar o SSO do Helpshift

1. Em um navegador da Web diferente, entre no aplicativo Helpshift como administrador.

1. Abra o **painel** do Helpshift e clique no **ícone de configurações**.

    ![A configuração do Helpshift](./media/helpshift-tutorial/configuration01.png)

1. Clique na guia **integrações** e execute as seguintes etapas:

    ![A configuração do Helpshift](./media/helpshift-tutorial/configuration02.png)

    a. Ative o **logon único (SAML – SSO)** .

    b. Selecione o **IDP (provedor de identidade)** como **Azure Active Directory**.

    c. Na caixa de texto **URL do ponto de extremidade 2,0 do SAML** , Cole o valor da URL de **logon** copiado do portal do Azure.

    d. Abra o arquivo de **certificado (Base64)** baixado no bloco de notas, copie o conteúdo do arquivo (sem usar as linhas ' — – Begin Certificate –-' e ' – – End Certificate –-') e cole-o na caixa de texto **certificado X. 509** .

    e. Na caixa de texto **URL do emissor** , Cole o valor do **identificador do Azure ad** copiado do portal do Azure.

    f. Clique em **aplicar alterações**.

### <a name="create-helpshift-test-user"></a>Criar usuário de teste do Helpshift

Nesta seção, você criará um usuário chamado B. Simon em Helpshift. Trabalhe com a [equipe de suporte ao cliente do Helpshift](mailto:support@helpshift.com) para adicionar os usuários na plataforma do Helpshift. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Helpshift no painel de acesso, você deverá ser conectado automaticamente ao Helpshift para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Helpshift com o Azure AD](https://aad.portal.azure.com/)