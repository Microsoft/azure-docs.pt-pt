---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com acesso de SSO do MS Azure para Ethidex de conformidade do Microsoft Office™ | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e o acesso de SSO do MS Azure para conformidade com o Ethidex Office™.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58b2e70f-d1dd-47b6-b91f-f77581df01c6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa0ec97683fe8597ced234fe4b7251a4daeed49f
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173498"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ms-azure-sso-access-for-ethidex-compliance-office"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com acesso de SSO do MS Azure para o Office Ethidex Compliance™

Neste tutorial, você aprenderá a integrar o acesso do Microsoft Azure SSO para Ethidex Compliance Office™ com o Azure Active Directory (AD do Azure). Ao integrar o acesso do Microsoft Azure SSO ao Ethidex Compliance Office™ com o Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Microsoft Azure SSO Access for Ethidex Compliance Office™.
* Habilite seus usuários a entrar automaticamente no acesso de SSO do MS Azure para conformidade com o Ethidex Office™ com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Acesso de SSO do MS Azure para Ethidex Compliance Office™ assinatura habilitada para logon único (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O acesso do Microsoft Azure SSO para Ethidex Compliance Office™ dá suporte ao SSO iniciado pelo **IDP**

## <a name="adding-ms-azure-sso-access-for-ethidex-compliance-office-from-the-gallery"></a>Adicionando acesso de SSO do MS Azure para™ de conformidade do Ethidex Office da Galeria

Para configurar a integração do acesso do Microsoft Azure SSO para Ethidex Compliance Office™ no Azure AD, você precisa adicionar o acesso de SSO do MS Azure para o Office Compliance™ da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **acesso de SSO do MS Azure para Ethidex de conformidade do Microsoft Office™** na caixa de pesquisa.
1. Selecione **acesso de SSO do MS Azure para conformidade do Ethidex Office™** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ms-azure-sso-access-for-ethidex-compliance-office"></a>Configurar e testar o logon único do Azure AD para o acesso do Microsoft Azure SSO para Ethidex Compliance Office™

Configure e teste o SSO do Azure AD com o acesso de SSO do Microsoft Azure para Ethidex Compliance Office™ usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no acesso de SSO do MS Azure para conformidade com o Ethidex Office™.

Para configurar e testar o SSO do Azure AD com o acesso do Microsoft Azure SSO para Ethidex Compliance Office™, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o acesso do Microsoft Azure SSO para o Ethidex Compliance Office SSO](#configure-ms-azure-sso-access-for-ethidex-compliance-office-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Create Microsoft Azure SSO Access for Ethidex Compliance Office Test User](#create-ms-azure-sso-access-for-ethidex-compliance-office-test-user)** – para ter um equivalente de B. Simon no Microsoft Azure SSO Access for Ethidex compliance Office™ que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **acesso ao SSO do MS Azure para Ethidex Compliance Office™** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , o aplicativo é pré-configurado e as URLs necessárias já foram preenchidas previamente com o Azure. O usuário precisa salvar a configuração clicando no botão **salvar** .


1. O acesso de SSO do MS Azure para Ethidex Compliance Office™ aplicativo de aplicativo espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, onde o **nameidentifier** é mapeado com **User. UserPrincipalName**. O acesso de SSO do MS Azure para Ethidex Compliance Office™ aplicativo espera que o **nameidentifier** seja mapeado com **User. mail**, portanto, você precisa editar o mapeamento de atributo clicando no ícone **Editar** e alterar o mapeamento de atributo.

    ![image](common/edit-attribute.png)

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize **certificado (bruto)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificateraw.png)

1. Na seção **Configurar o acesso ao SSO do MS Azure para Ethidex conformidade do Microsoft Office™** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao acesso do Microsoft Azure SSO para Ethidex Compliance Office™.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **acesso de SSO do MS Azure para Ethidex de conformidade do Microsoft Office™**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-ms-azure-sso-access-for-ethidex-compliance-office-sso"></a>Configurar o acesso de SSO do MS Azure para o SSO do Office de conformidade do Ethidex

Para configurar o logon único no lado do **Microsoft Azure acesso de SSO para Ethidex Compliance Office™** , é necessário enviar o **certificado (bruto)** baixado e as URLs copiadas apropriadas de portal do Azure para o [acesso de SSO do MS Azure para o Office Compliance™ equipe de suporte](mailto:support@ethidex.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-ms-azure-sso-access-for-ethidex-compliance-office-test-user"></a>Criar usuário de teste do Microsoft Azure SSO para Ethidex Compliance Office

Nesta seção, você criará um usuário chamado B. Simon no acesso de SSO do Microsoft Azure para Ethidex Compliance Office™. Trabalhe com o [acesso de SSO do MS Azure para a equipe de suporte do Ethidex Compliance office™](mailto:support@ethidex.com) para adicionar os usuários na plataforma de acesso de SSO do MS Azure para conformidade com o Ethidex Office™. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco acesso de SSO do MS Azure para Ethidex de conformidade do Office™ no painel de acesso, você deverá ser conectado automaticamente à™ do Office de conformidade do MS Azure para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o acesso do Microsoft Azure SSO para Ethidex Compliance Office™ com o Azure AD](https://aad.portal.azure.com/)

