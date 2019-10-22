---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o onit | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o onit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bc479a28-8fcd-493f-ac53-681975a5149c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e908cb76a57f027494230edc648b69da0730ac27
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "70164231"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-onit"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o onit

Neste tutorial, você aprenderá a integrar o onit com o Azure Active Directory (Azure AD). Ao integrar o onit ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao onit.
* Habilite seus usuários a serem conectados automaticamente ao onit com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do onit.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O onit dá suporte ao SSO iniciado por **SP**

## <a name="adding-onit-from-the-gallery"></a>Adicionando o onit da Galeria

Para configurar a integração do onit ao Azure AD, você precisará adicionar o onit da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **onit** na caixa de pesquisa.
1. Selecione **onit** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-onit"></a>Configurar e testar o logon único do Azure AD para o onit

Configure e teste o SSO do Azure AD com o onit usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no onit.

Para configurar e testar o SSO do Azure AD com o onit, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do onit](#configure-onit-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do onit](#create-onit-test-user)** – para ter um equivalente de B. Simon em onit que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **onit** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<sub-domain>.onit.com`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `https://<sub-domain>.onit.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contate a [equipe de suporte ao cliente do onit](https://www.onit.com/support) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na seção **certificado de autenticação SAML** , clique no botão **Editar** para abrir a caixa de diálogo **certificado de autenticação SAML** .

    ![Editar certificado de autenticação SAML](common/edit-certificate.png)

1. Na seção **certificado de autenticação SAML** , copie o **valor da impressão digital** e salve-o em seu computador.

    ![Copiar valor de impressão digital](common/copy-thumbprint.png)

1. Na seção **Configurar onit** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao onit.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **onit**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-onit-sso"></a>Configurar o SSO do onit

1. Em uma janela diferente do navegador da Web, faça logon em seu site de empresa do onit como administrador.

2. No menu na parte superior, clique em **Administração**.
   
    ![Administrar](./media/onit-tutorial/IC791174.png "Administração")

3. Clique em **Editar empresa**.
   
    ![Editar empresa](./media/onit-tutorial/IC791175.png "Editar empresa")
   
4. Clique na guia **segurança** .
    
    ![Editar informações da empresa](./media/onit-tutorial/IC791176.png "Editar informações da empresa")

5. Na guia **segurança** , execute as seguintes etapas:

    ![Logon único](./media/onit-tutorial/IC791177.png "Logon único")

    a. Como **estratégia de autenticação**, selecione **logon único e senha**.
    
    b. Na caixa de texto **URL de destino do IDP** , Cole o valor da URL de **logon**copiado de portal do Azure.

    c. Na caixa de texto **URL de logout do IDP** , Cole o valor da URL de **logout**copiado de portal do Azure.

    d. Na caixa de texto **impressão digital do certificado IDP (SHA1)** , Cole o valor da **impressão digital** do certificado copiado de portal do Azure.

### <a name="create-onit-test-user"></a>Criar usuário de teste do onit

Para permitir que os usuários do AD do Azure façam logon no onit, eles devem ser provisionados no onit. No caso do onit, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **onit** como administrador.

2. Clique em **Adicionar usuário**.

    ![Administrar](./media/onit-tutorial/IC791180.png "Administração")

3. Na página da caixa de diálogo **Adicionar usuário** , execute as seguintes etapas:

    ![Adicionar usuário](./media/onit-tutorial/IC791181.png "Adicionar Utilizador")

    a. Digite o **nome** e o **endereço de email** de uma conta válida do Azure AD que você deseja provisionar nas caixas de texto relacionadas.

    b. Clique em **Criar**.

    > [!NOTE]
    > O titular da conta Azure Active Directory recebe um email e segue um link para confirmar sua conta antes que ela se torne ativa.

## <a name="test-sso"></a>Testar SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do onit no painel de acesso, você deverá ser conectado automaticamente ao onit para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o onit com o Azure AD](https://aad.portal.azure.com/)