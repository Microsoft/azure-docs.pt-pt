---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com EAB navegar em cuidados estratégicos | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e EAB navegar pelo atendimento estratégico.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0c61fcfc-b89b-4f89-9b81-27098047cddb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1185217d6b12d3efd5dedc9faf903f1b365481db
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014070"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-eab-navigate-strategic-care"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com EAB navegar pelo atendimento estratégico

Neste tutorial, você aprenderá a integrar o EAB a navegar pelo atendimento estratégico com o Azure Active Directory (Azure AD). Ao integrar o EAB, navegue pelo atendimento estratégico com o Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao EAB navegue pelo atendimento estratégico.
* Habilite seus usuários a entrarem automaticamente no EAB para navegar pelo atendimento estratégico com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* EAB navegue pela assinatura habilitada para SSO (logon único) habilitado do Strategic Care.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* EAB navegar pelo atendimento estratégico dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-eab-navigate-strategic-care-from-the-gallery"></a>Adicionando EAB navegar pelo atendimento estratégico da Galeria

Para configurar a integração do EAB, navegue pelo atendimento estratégico no Azure AD, você precisa adicionar o EAB navegar pelo cuidado estratégico da galeria para sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **EAB navegar para o atendimento estratégico** na caixa de pesquisa.
1. Selecione **EAB navegar por cuidados estratégicos** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-eab-navigate-strategic-care"></a>Configurar e testar o logon único do Azure AD para EAB navegar no atendimento estratégico

Configurar e testar o SSO do Azure AD com o EAB navegue pelo cuidado estratégico usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no EAB navegar pelo atendimento estratégico.

Para configurar e testar o SSO do Azure AD com o EAB navegue pelo atendimento estratégico, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o EAB navegar pelo SSO de atendimento estratégico](#configure-eab-navigate-strategic-care-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar EAB navegar pelo usuário de teste do Strategic Care](#create-eab-navigate-strategic-care-test-user)** – para ter um equivalente de B. Simon em EAB, navegue pelo atendimento estratégico que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **EAB Navigate Strategic Care** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://<CUSTOMERURL>.eab.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de logon real. Contate a [equipe de suporte ao cliente do EAB Navigate Strategic Care](mailto:tech@gradesfirst.com) para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique no botão Copiar para copiar a **URL de metadados de Federação do aplicativo** e salvá-la no computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure, concedendo acesso ao EAB navegar pelo atendimento estratégico.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **EAB navegar em atendimento estratégico**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-eab-navigate-strategic-care-sso"></a>Configurar o SSO de assistência estratégica do EAB Navigate

Para configurar o logon único no lado do **EAB navegue pelo atendimento estratégico** , você precisa enviar a **URL de metadados de Federação do aplicativo** para a equipe de suporte do [EAB navegar pelo atendimento estratégico](mailto:tech@gradesfirst.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-eab-navigate-strategic-care-test-user"></a>Criar EAB navegar pelo usuário de teste do Strategic Care

Nesta seção, você criará um usuário chamado B. Simon em EAB navegar pelo atendimento estratégico. Trabalhe com a [equipe de suporte de assistência estratégica do EAB Navigate](mailto:tech@gradesfirst.com) para adicionar os usuários na EAB navegar pela plataforma de atendimento estratégico. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco EAB navegar em cuidados estratégicos no painel de acesso, você deverá entrar automaticamente no EAB para navegar pelo cuidado estratégico para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o EAB navegue pelo atendimento estratégico com o Azure AD](https://aad.portal.azure.com/)

