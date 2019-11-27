---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Sugar CRM | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Sugar CRM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fae7b80fd4d2fcec32bbef5e4cdf18e576412a86
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231967"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sugar-crm"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Sugar CRM

Neste tutorial, você aprenderá a integrar o Sugar CRM com o Azure Active Directory (Azure AD). Ao integrar o Sugar CRM ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Sugar CRM.
* Habilite seus usuários a entrarem automaticamente no Sugar CRM com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Sugar CRM.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Sugar CRM dá suporte ao SSO iniciado pelo **SP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-sugar-crm-from-the-gallery"></a>Adicionando o Sugar CRM da Galeria

Para configurar a integração do Sugar CRM ao Azure AD, você precisará adicionar o Sugar CRM da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite Sugar **CRM** na caixa de pesquisa.
1. Selecione Sugar **CRM** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sugar-crm"></a>Configurar e testar o logon único do Azure AD para o Sugar CRM

Configure e teste o SSO do Azure AD com o Sugar CRM usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Sugar CRM.

Para configurar e testar o SSO do Azure AD com o Sugar CRM, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o Sugar CRM SSO](#configure-sugar-crm-sso)** -para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste](#create-sugar-crm-test-user)** do Sugar CRM – para ter um equivalente de B. Simon no Sugar CRM vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do Sugar **CRM** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://<companyname>.sugarondemand.com`|
    | `https://<companyname>.trial.sugarcrm`|

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://<companyname>.sugarondemand.com/<companyname>`|
    | `https://<companyname>.trial.sugarcrm.com/<companyname>`|
    | `https://<companyname>.trial.sugarcrm.eu/<companyname>`|

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e a URL de resposta reais. Contate a [equipe de suporte ao cliente](https://support.sugarcrm.com/) do Sugar CRM para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar o CRM** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo usuário** na parte superior da tela.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao Sugar CRM.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione Sugar **CRM**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-sugar-crm-sso"></a>Configurar o logon único do Sugar CRM

1. Em uma janela diferente do navegador da Web, entre no site da empresa do Sugar CRM como administrador.

1. Vá para **administrador**.

    ![ADM](./media/sugarcrm-tutorial/ic795888.png "administrador")

1. Na seção **Administração** , clique em **Gerenciamento de senhas**.

    ![Administrar](./media/sugarcrm-tutorial/ic795889.png "Administração")

1. Selecione **habilitar autenticação SAML**.

    ![Administrar](./media/sugarcrm-tutorial/ic795890.png "Administração")

1. Na seção **autenticação SAML** , execute as seguintes etapas:

    ![Autenticação SAML](./media/sugarcrm-tutorial/ic795891.png "Autenticação SAML")  

    a. Na caixa de texto **URL de logon** , Cole o valor da **URL de logon**copiado de portal do Azure.
  
    b. Na caixa de texto **URL do SLO** , Cole o valor da **URL de logout**copiado de portal do Azure.
  
    c. Abra seu certificado codificado em base 64 no bloco de notas, copie o conteúdo dele para a área de transferência e cole o certificado inteiro na caixa de texto **certificado X. 509** .
  
    d. Clique em **Guardar**.

### <a name="create-sugar-crm-test-user"></a>Criar usuário de teste do Sugar CRM

Para permitir que os usuários do AD do Azure entrem no Sugar CRM, eles devem ser provisionados para o Sugar CRM. No caso do Sugar CRM, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no site da empresa do Sugar **CRM** como administrador.

1. Vá para **administrador**.

    ![ADM](./media/sugarcrm-tutorial/ic795888.png "administrador")

1. Na seção **Administração** , clique em **Gerenciamento de usuários**.

    ![Administrar](./media/sugarcrm-tutorial/ic795893.png "Administração")

1. Vá para **usuários \> criar novo usuário**.

    ![Criar novo usuário](./media/sugarcrm-tutorial/ic795894.png "Criar novo usuário")

1. Na guia **perfil do usuário** , execute as seguintes etapas:

    ![Novo usuário](./media/sugarcrm-tutorial/ic795895.png "Novo usuário")

    * Digite o **nome de usuário**, o **sobrenome**e o **endereço de email** de um usuário de Azure Active Directory válido nas caixas de texto relacionadas.
  
1. Em **status**, selecione **ativo**.

1. Na guia senha, execute as seguintes etapas:

    ![Novo usuário](./media/sugarcrm-tutorial/ic795896.png "Novo usuário")

    a. Digite a senha na caixa de texto relacionada.

    b. Clique em **Guardar**.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação de conta de usuário do Sugar CRM ou APIs fornecidas pelo Sugar CRM para provisionar contas de usuário do Azure AD.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Sugar CRM no painel de acesso, você deverá entrar automaticamente no Sugar CRM para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Sugar CRM com o Azure AD](https://aad.portal.azure.com/)

