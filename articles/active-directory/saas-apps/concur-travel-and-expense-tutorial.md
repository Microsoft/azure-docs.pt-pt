---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o concur Travel e Expense | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o concur e as despesas de viagem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6401ace4-71c0-4778-8b15-900f2f5f0c4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dddd9f6904aa5ef7840850792aeabf04666dddc
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373425"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur-travel-and-expense"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com concur viagem e despesas

Neste tutorial, você aprenderá a integrar o concur Travel e despesas com o Azure Active Directory (Azure AD). Ao integrar o concur Travel e despesas com o Azure AD, você pode:

* Controle no Azure AD quem tem acesso a despesas e viagens concurs.
* Habilite seus usuários a entrar automaticamente no concur Travel e Expense com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura de viagem e despesas do concur.
* Uma função de "administrador da empresa" em sua conta de usuário do concur. Você pode testar se tem o acesso certo acessando a [ferramenta de autoatendimento do SSO do concur](https://www.concursolutions.com/nui/authadmin/ssoadmin). Se você não tiver o acesso, entre em contato com o suporte do concur ou com o gerente de projeto de implementação. 

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD.

* A viagem e as despesas do concur dão suporte ao SSO iniciado pelo **IDP** e pelo **SP**
* A viagem e as despesas do concur dão suporte ao teste de SSO no ambiente de produção e de implementação 

> [!NOTE]
> O identificador deste aplicativo é um valor de cadeia de caracteres fixo para cada uma das três regiões: US, EMEA e China. Portanto, apenas uma instância pode ser configurada para cada região em um locatário. 

## <a name="adding-concur-travel-and-expense-from-the-gallery"></a>Adicionando concur viagem e despesas da Galeria

Para configurar a integração do concur Travel e despesas ao Azure AD, você precisa adicionar o concur Travel e despesas da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **concur viagem e despesas** na caixa de pesquisa.
1. Selecione **viagem e despesas do concur** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-concur-travel-and-expense"></a>Configurar e testar o logon único do Azure AD para concur viagem e despesas

Configure e teste o SSO do Azure AD com o concur Travel e Expense usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em concur viagem e despesas.

Para configurar e testar o SSO do Azure AD com o concur Travel e Expense, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO de viagem e despesas do concur](#configure-concur-travel-and-expense-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste de viagem e despesas do concur](#create-concur-travel-and-expense-test-user)** – para ter um equivalente de B. Simon em concur Travel e Expense que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos de **viagem e despesas do concur** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , o aplicativo é pré-configurado no modo iniciado pelo **IDP** e as URLs necessárias já foram preenchidas previamente com o Azure. O usuário precisa salvar a configuração clicando no botão **salvar** .

    > [!NOTE]
    > O identificador (ID da entidade) e a URL de resposta (URL do serviço do consumidor de asserção) são específicos da região. Selecione com base no datacenter da sua entidade concur. Se você não souber o datacenter da sua entidade concur, entre em contato com o suporte do concur. 

5. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para o **atributo de usuário** para editar as configurações. O identificador de usuário exclusivo precisa corresponder concur usuário login_id. Normalmente, você deve alterar **User. UserPrincipalName** para **User. mail**.

    ![Editar atributo de usuário](common/edit-attribute.png)

6. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar os metadados e salvá-los no computador.

    ![O link de download do certificado](common/metadataxml.png)

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso a viagens e despesas concurs.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **concur viagem e despesas**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-concur-travel-and-expense-sso"></a>Configurar SSO de viagem e despesas do concur

1. Para configurar o logon único no lado do **concur Travel e Expense** , você precisa carregar o XML de **metadados de Federação** baixado para a [ferramenta de autoatendimento do SSO do concur](https://www.concursolutions.com/nui/authadmin/ssoadmin) e fazer logon com uma conta com a função "administrador da empresa". 

1. Clique em **Adicionar**.
1. Insira um nome personalizado para o IdP, por exemplo, "Azure AD (US)". 
1. Clique em **carregar arquivo XML** e anexar **XML de metadados de Federação** que você baixou anteriormente.
1. Clique em **adicionar metadados** para salvar a alteração.

    ![Captura de tela da ferramenta de autoatendimento do SSO do concur](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-concur-travel-and-expense-test-user"></a>Criar usuário de teste de viagem e despesas do concur

Nesta seção, você criará um usuário chamado B. Simon em concur Travel e Expense. Trabalhe com a equipe de suporte do concur para adicionar os usuários na plataforma de viagem e despesas do concur. Os usuários devem ser criados e ativados antes de usar o logon único. 

> [!NOTE]
> B. Simon ' s concur ID de logon precisa corresponder ao identificador exclusivo de B. Simon no Azure AD. Por exemplo, se o identificador exclusivo do Azure AD da B. Simon for `B.Simon@contoso.com`. B. Simon ' s concur ID de logon precisa ser `B.Simon@contoso.com` também. 

## <a name="configure-concur-mobile-sso"></a>Configurar o SSO móvel do concur
Para habilitar o SSO do concur Mobile, você precisa fornecer à **URL de acesso do usuário**da equipe de suporte do concur. Siga as etapas abaixo para obter a **URL de acesso do usuário** do Azure AD:
1. Ir para **aplicativos empresariais**
1. Clique em **concur viagem e despesas**
1. Clique em **Propriedades**
1. Copiar a **URL de acesso do usuário** e fornecer essa URL para o suporte do concur

> [!NOTE]
> A opção de autoatendimento para configurar o SSO não está disponível, portanto, trabalhe com a equipe de suporte do concur para habilitar o SSO móvel. 

## <a name="test-sso"></a>Testar SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco de viagem e despesas do concur no painel de acesso, você deverá entrar automaticamente na viagem e nas despesas do concur para as quais você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente viagens concurs e despesas com o Azure AD](https://aad.portal.azure.com/)

