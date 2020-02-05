---
title: 'Tutorial: Integração do Diretório Ativo Azure com a O.C. Tanner - AppreciateHub [ AppreciateHub ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o O.C. Tanner - AppreciateHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/28/2020
ms.author: jeedes
ms.openlocfilehash: 768756e013a4301ea11f5f9478178bdad46f9d67
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984001"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oc-tanner---appreciatehub"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com O.C. Tanner - AppreciateHub

Neste tutorial, aprenderá a integrar O.C. Tanner - AppreciateHub com Diretório Ativo Azure (Azure AD). Quando integrar o O.C. Tanner - AppreciateHub com Azure AD, pode:

* Controlo em Azure AD que tem acesso a O.C. Tanner - AppreciateHub.
* Ative que os seus utilizadores sejam automaticamente inscritos na O.C. Tanner - AppreciateHub com as suas contas Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* O.C. Tanner - AppreciateHub single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O.C. Tanner - AppreciateHub suporta **IDP** iniciado SSO

* Assim que configurar o O.C. Tanner - AppreciateHub pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controles de sessão se estendem do acesso condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>Adicionando O.C. Tanner - AppreciateHub da galeria

Para configurar a integração de O.C. Tanner - AppreciateHub em Azure AD, você precisa adicionar O.C. Tanner - AppreciateHub da galeria para a sua lista de aplicações geridas saaS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. No Add da secção **da galeria,** digite **O.C. Tanner - AppreciateHub** na caixa de pesquisa.
1. Selecione **O.C. Tanner - AppreciateHub** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-oc-tanner---appreciatehub"></a>Configure e teste azure AD único sinal para O.C. Tanner - AppreciateHub

Configure e teste Azure AD SSO com O.C. Tanner - AppreciateHub usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em O.C. Tanner - AppreciateHub.

Para configurar e testar Azure AD SSO com O.C. Tanner - AppreciateHub, complete os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configure O.C. Tanner - AppreciateHub SSO](#configure-oc-tanner---appreciatehub-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie O.C. Tanner - utilizador](#create-oc-tanner---appreciatehub-test-user)** de testes AppreciateHub - para ter uma contrapartida de B.Simon em O.C. Tanner - AppreciateHub que está ligado à representação da AD Azure do utilizador.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **O.C. Tanner - AppreciateHub,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , o usuário não precisa executar nenhuma etapa, pois o aplicativo já está previamente integrado ao Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na **secção set up O.C. Tanner - AppreciateHub,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso a O.C. Tanner - AppreciateHub.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **O.C. Tanner - AppreciateHub**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-oc-tanner---appreciatehub-sso"></a>Configure O.C. Tanner - AppreciateHub SSO

Para configurar um único sign-on no **lado de O.C. Tanner - AppreciateHub,** você precisa enviar o descarregamento **da Federação Metadados XML** e URLs copiados apropriados do portal Azure para [O.C. Tanner - Equipa](mailto:sso@octanner.com)de suporte AppreciateHub . Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-oc-tanner---appreciatehub-test-user"></a>Criar O.C. Tanner - Utilizador de teste AppreciateHub

O objetivo desta secção é criar uma utilizadora chamada Britta Simon em O.C. Tanner - AppreciateHub.

**Para criar um utilizador chamado Britta Simon em O.C. Tanner - AppreciateHub, execute os seguintes passos:**

Peça à sua equipa de [suporte O.C. Tanner - AppreciateHub](mailto:sso@octanner.com) para criar um utilizador que tenha como nomeID atributo o mesmo valor que o nome de utilizador de Britta Simon em Azure AD.

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no O.C. Tanner - AppreciateHub azulejo no Painel de Acesso, você deve ser automaticamente inscrito no O.C. Tanner - AppreciateHub para o qual criou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente O.C. Tanner - AppreciateHub com Azure AD](https://aad.portal.azure.com/)

- [O que é o controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o O.C. Tanner - AppreciateHub com visibilidade avançada e controlos](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)