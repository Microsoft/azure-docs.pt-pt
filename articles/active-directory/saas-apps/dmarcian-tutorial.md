---
title: 'Tutorial: Integração do Azure Active Directory com o dmarcian | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o dmarcian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 602c885deca429b56417181971ced495831ba5d3
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823692"
---
# <a name="tutorial-integrate-dmarcian-with-azure-active-directory"></a>Tutorial: Integrar o dmarcian ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o dmarcian com o Azure Active Directory (Azure AD). Ao integrar o dmarcian ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao dmarcian.
* Habilite seus usuários a serem conectados automaticamente ao dmarcian com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* assinatura habilitada para SSO (logon único) do dmarcian.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* o dmarcian dá suporte ao **SP e** ao SSO iniciado pelo IDP

## <a name="adding-dmarcian-from-the-gallery"></a>Adicionando o dmarcian da Galeria

Para configurar a integração do dmarcian ao Azure AD, você precisará adicionar o dmarcian da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **dmarcian** na caixa de pesquisa.
1. Selecione **dmarcian** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste o SSO do Azure AD com o dmarcian usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no dmarcian.

Para configurar e testar o SSO do Azure AD com o dmarcian, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do dmarcian](#configure-dmarcian-sso)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
5. **[Criar usuário de teste do dmarcian](#create-dmarcian-test-user)** – para ter um equivalente de B. Simon em dmarcian que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **dmarcian** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , execute as seguintes etapas:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão:

    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

5. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:
    
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Esses valores não são reais. Você atualizará esses valores com o identificador, a URL de resposta e a URL de logon reais que são explicadas posteriormente no tutorial.

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique no botão Copiar para copiar a URL de **metadados de Federação do aplicativo** e salvá-la no computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

### <a name="configure-dmarcian-sso"></a>Configurar o SSO do dmarcian

1. Para automatizar a configuração no dmarcian, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Setup dmarcian** irá direcioná-lo para o aplicativo dmarcian. A partir daí, forneça as credenciais de administrador para entrar no dmarcian. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-6.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar o dmarcian manualmente, abra uma nova janela do navegador da Web e entre no site da empresa do dmarcian como administrador e execute as seguintes etapas:

4. Clique em **perfil** no canto superior direito e navegue até **preferências**.

    ![As preferências](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

5. Role para baixo e clique na seção **logon único** e, em seguida, clique em **Configurar**.

    ![O único](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

6. Na página **logon único do SAML,** defina o **status** como **habilitado** e execute as seguintes etapas:

    ![A autenticação do](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * Em **Adicionar dmarcian à seção do provedor de identidade** , clique em **copiar** para copiar a URL do serviço do **consumidor de asserção** para sua instância e cole-a na caixa de texto **URL de resposta** na **seção configuração básica do SAML** em portal do Azure.

    * Em **Adicionar dmarcian à seção do provedor de identidade** , clique em **copiar** para copiar a **ID da entidade** para sua instância e cole-a na caixa de texto **identificador** na **seção configuração básica do SAML** em portal do Azure.

    * Na seção **configurar autenticação** , na caixa de texto **metadados do provedor de identidade** , Cole a URL de metadados de Federação do **aplicativo**, que você copiou do portal do Azure.

    * Na seção **configurar autenticação** , na caixa de texto **instruções do atributo** , Cole a URL`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * Na seção **Configurar URL de logon** , copie a **URL de logon** da sua instância e cole-a na caixa de texto **URL** de logon na **seção configuração básica do SAML** em portal do Azure.

        > [!Note]
        > Você pode modificar a **URL de logon** de acordo com sua organização.

    * Clique em **Guardar**.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao dmarcian.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **dmarcian**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-dmarcian-test-user"></a>Criar usuário de teste do dmarcian

Para permitir que os usuários do Azure AD entrem no dmarcian, eles devem ser provisionados no dmarcian. No dmarcian, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no dmarcian como um administrador de segurança.

2. Clique em **perfil** no canto superior direito e navegue até **gerenciar usuários**.

    ![O usuário](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. No lado direito da seção **usuários de SSO** , clique em **Adicionar novo usuário**.

    ![O Adicionar usuário](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. No pop-up **Adicionar novo usuário** , execute as seguintes etapas:

    ![O novo usuário](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. Na caixa de texto **novo email do usuário** , insira o email do usuário, como **brendafernandes\@contoso.com**.

    b. Se você quiser conceder direitos de administrador ao usuário, selecione **tornar o usuário um administrador**.

    c. Clique em **adicionar utilizador**.

### <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do dmarcian no painel de acesso, você deverá ser conectado automaticamente ao dmarcian para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

