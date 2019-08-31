---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com o ADP | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ADP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b031ded2022078c31bd8570c6a6317c398715480
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70162664"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com o ADP

Neste tutorial, você aprenderá a integrar o ADP ao Azure Active Directory (Azure AD). Ao integrar o ADP ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao ADP.
* Habilite seus usuários a serem automaticamente conectados ao ADP com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do ADP.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O ADP oferece suporte ao SSO iniciado pelo **IDP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-adp-from-the-gallery"></a>Adicionando o ADP da Galeria

Para configurar a integração do ADP ao Azure AD, você precisará adicionar o ADP da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **ADP** na caixa de pesquisa.
1. Selecione **ADP** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-adp"></a>Configurar e testar o logon único do Azure AD para o ADP

Configure e teste o SSO do Azure AD com o ADP usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no ADP.

Para configurar e testar o SSO do Azure AD com o ADP, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
2. **[Configurar o SSO do ADP](#configure-adp-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do ADP](#create-adp-test-user)** – para ter um equivalente de B. Simon no ADP que esteja vinculado à representação do usuário no Azure AD.
3. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na portal do Azure, na página de integração de aplicativos do **ADP** , clique na **guia Propriedades** e execute as seguintes etapas: 

    ![Propriedades de logon único](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Defina o valor **do campo habilitado para usuários para entrar** como **Sim**.

    b. Copie a **URL de acesso do usuário** e você precisa colá-la na **seção configurar URL de logon**, que é explicada posteriormente no tutorial.

    c. Defina o valor do campo de **atribuição de usuário obrigatório** como **Sim**.

    d. Defina o valor do campo **visível para usuários** como **não**.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ADP** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    Na caixa de texto **identificador (ID da entidade)** , digite uma URL:`https://fed.adp.com`

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Na seção **Configurar ADP** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao ADP.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **ADP**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-adp-sso"></a>Configurar SSO do ADP

Para configurar o logon único no lado do **ADP** , você precisa carregar o XML de **metadados** baixado no [site do ADP](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Esse processo pode levar alguns dias.

### <a name="configure-your-adp-services-for-federated-access"></a>Configurar seus serviços do ADP para acesso federado

>[!Important]
> Seus funcionários que exigem acesso federado aos serviços do ADP devem ser atribuídos ao aplicativo de serviço do ADP e, subsequentemente, os usuários devem ser reatribuídos ao serviço ADP específico.
Após o recebimento da confirmação do seu representante ADP, configure seus serviços do ADP e atribua/gerencie usuários para controlar o acesso do usuário ao serviço ADP específico.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **ADP** na caixa de pesquisa.
1. Selecione **ADP** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.
1. Na portal do Azure, na página de integração de aplicativos do **ADP** , clique na **guia Propriedades** e execute as seguintes etapas:  

    ![Propriedades vinculadas de logon único](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Defina o valor **do campo habilitado para usuários para entrar** como **Sim**.

    b.  Defina o valor do campo de **atribuição de usuário obrigatório** como **Sim**.

    c.  Defina o valor do campo **visível para usuários** como **Sim**.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ADP** , localize a seção **gerenciar** e selecione **logon único**.

1. Na caixa de diálogo **selecionar um método de logon único** , selecione **modo** como **vinculado**. para vincular seu aplicativo ao **ADP**.

    ![Logon único vinculado](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

1. Navegue até a seção **Configurar URL de logon** , execute as seguintes etapas:

    ![Prop de logon único](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. Cole a **URL de acesso do usuário**, que você copiou da **guia Propriedades** acima (do aplicativo principal do ADP).
                                                             
    b. A seguir estão os cinco aplicativos que dão suporte a diferentes **URLs de estado**de retransmissão. Você precisa acrescentar o valor da **URL do estado** de retransmissão apropriado para determinado aplicativo manualmente à **URL de acesso do usuário**.
    
    * **A equipe de funcionários da ADP agora**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **A equipe de funcionários da ADP agora aprimorou o tempo**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP privilegiando HCM**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **RH do ADP Enterprise**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Salve** suas alterações.

10. Após o recebimento da confirmação do seu representante ADP, comece a testar com um ou dois usuários.

    a. Atribua alguns usuários ao aplicativo de serviço ADP para testar o acesso federado.

    b. O teste é bem-sucedido quando os usuários acessam o aplicativo de serviço ADP na galeria e podem acessar seu serviço ADP.
 
11. Após a confirmação de um teste bem-sucedido, atribua o serviço ADP federado a usuários individuais ou grupos de usuários, que será explicado posteriormente no tutorial e distribua-o para seus funcionários.

### <a name="create-adp-test-user"></a>Criar usuário de teste do ADP

O objetivo desta seção é criar um usuário chamado B. Simon em ADP. Trabalhe com a [equipe de suporte do ADP](https://www.adp.com/contact-us/overview.aspx) para adicionar os usuários na conta do ADP. 

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco ADP no painel de acesso, você deverá ser conectado automaticamente à ADP para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o ADP com o Azure AD](https://aad.portal.azure.com)
