---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Coralogix | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Coralogix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ba79bfc1-992e-4924-b76a-8eb0dfb97724
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e56d2104fd5e82b60f4e6aaa32d1bbc191d67d4
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638761"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coralogix"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Coralogix

Neste tutorial, você aprenderá a integrar o Coralogix com o Azure Active Directory (Azure AD). Ao integrar o Coralogix ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Coralogix.
* Habilite seus usuários a serem conectados automaticamente ao Coralogix com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Coralogix.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Coralogix dá suporte ao SSO iniciado por **SP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-coralogix-from-the-gallery"></a>Adicionando o Coralogix da Galeria

Para configurar a integração do Coralogix ao Azure AD, você precisará adicionar o Coralogix da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Coralogix** na caixa de pesquisa.
1. Selecione **Coralogix** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-coralogix"></a>Configurar e testar o logon único do Azure AD para o Coralogix

Configure e teste o SSO do Azure AD com o Coralogix usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Coralogix.

Para configurar e testar o SSO do Azure AD com o Coralogix, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do Coralogix](#configure-coralogix-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Coralogix](#create-coralogix-test-user)** – para ter um equivalente de B. Simon em Coralogix que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Coralogix** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa **URL de logon** , insira uma URL com o seguinte padrão: `https://<SUBDOMAIN>.coralogix.com`

    b. Na caixa de texto **identificador (ID da entidade)** , insira uma URL, como:
    
    `https://api.coralogix.com/saml/metadata.xml`

    ou

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize o valor com a URL de logon real. Contate a [equipe de suporte ao cliente do Coralogix](mailto:info@coralogix.com) para obter o valor. Você também pode consultar os padrões na seção **configuração básica do SAML** no portal do Azure.

 1. O aplicativo Coralogix espera as asserções SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos na seção **atributos de usuário** na página de integração de aplicativos. Na página **Configurar logon único com SAML** , selecione o botão **Editar** para abrir a caixa de diálogo **atributos de usuário** .

    ![imagem](common/edit-attribute.png)

1. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , edite as declarações usando o ícone **Editar** . Você também pode adicionar as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML, conforme mostrado na imagem anterior. Em seguida, execute as seguintes etapas:
    
    a. Selecione o **ícone Editar** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    imagem de ![](./media/coralogix-tutorial/tutorial_usermail.png) ![de imagem](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. Na lista **escolher formato do identificador de nome** , selecione **endereço de email**.

    c. Na lista **atributo de origem** , selecione **User. mail**.

    d. Selecione **Guardar**.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar Coralogix** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao Coralogix.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Coralogix**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-coralogix-sso"></a>Configurar o SSO do Coralogix

Para configurar o logon único no lado do **Coralogix** , é necessário enviar o XML de **metadados de Federação** baixado e as URLs copiadas apropriadas de portal do Azure para a equipe de suporte do [Coralogix](mailto:info@coralogix.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-coralogix-test-user"></a>Criar usuário de teste do Coralogix

Nesta seção, você criará um usuário chamado Brenda Simon no Coralogix. Trabalhe com a [equipe de suporte do Coralogix](mailto:info@coralogix.com) para adicionar os usuários na plataforma do Coralogix. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Coralogix no painel de acesso, você deverá ser conectado automaticamente ao Coralogix para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Coralogix com o Azure AD](https://aad.portal.azure.com/)

