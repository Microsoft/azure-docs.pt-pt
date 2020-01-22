---
title: 'Tutorial: integração do Azure Active Directory com o Jive | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o jive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9fc5659a-c116-4a1b-a601-333325a26b46
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccdab373be4bab876ef52ba478076b6a8b6e0845
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76291180"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jive"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Jive

Neste tutorial, você aprenderá a integrar o jive ao Azure Active Directory (Azure AD). Ao integrar o jive ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao jive.
* Habilite seus usuários a serem automaticamente conectados ao Jive com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do jive.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Jive dá suporte ao SSO iniciado pelo **SP**
* O Jive dá suporte ao [provisionamento **automatizado** de usuários](jive-provisioning-tutorial.md)
* Depois de configurar o Jive, você pode impor controles de sessão, que protegem vazamento e pós-infiltração dos dados confidenciais de sua organização em tempo real. Os controles de sessão se estendem do acesso condicional. [Saiba como impor o controle de sessão com Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-jive-from-the-gallery"></a>Adicionando o Jive da Galeria

Para configurar a integração do jive ao Azure AD, você precisa adicionar o Jive da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Jive** na caixa de pesquisa.
1. Selecione **Jive** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-jive"></a>Configurar e testar o logon único do Azure AD para o Jive

Configure e teste o SSO do Azure AD com o Jive usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no jive.

Para configurar e testar o SSO do Azure AD com o Jive, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do Jive](#configure-jive-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do Jive](#create-jive-test-user)** – para ter um equivalente de B. Simon no Jive que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Jive** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<instance name>.jivecustom.com`

    b. In the **Identifier (Entity ID)** text box, type a URL using the following pattern:
    `https://<instance name>.jiveon.com`

    > [!NOTE]
    > These values are not real. Update these values with the actual Sign on URL and Identifier. Contact [Jive Client support team](https://www.jivesoftware.com/services-support/) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Na seção **Configurar o Jive** , copie as URLs apropriadas de acordo com seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao jive.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Jive**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-jive-sso"></a>Configurar SSO do Jive

1. Para configurar o logon único no lado do **Jive** , faça logon no seu locatário do jive como administrador.

1. No menu na parte superior, clique em **SAML**.

    ![Configurar o logon único no lado do aplicativo](./media/jive-tutorial/tutorial_jive_002.png)

    a. Selecione **habilitado** na guia **geral** .

    b. Clique no botão **salvar todas as configurações do SAML** .

1. Navegue até a guia **metadados IDP** .

    ![Configurar o logon único no lado do aplicativo](./media/jive-tutorial/tutorial_jive_003.png)

    a. Copie o conteúdo do arquivo XML de metadados baixado e cole-o na caixa de texto **metadados do IDP (provedor de identidade)** .

    b. Clique no botão **salvar todas as configurações do SAML** .

1. Selecione a guia **mapeamento de atributo de usuário** .

    ![Configurar o logon único no lado do aplicativo](./media/jive-tutorial/tutorial_jive_004.png)

    a. Na caixa de texto **email** , copie e cole o nome do atributo de valor de **email** .

    b. Na caixa de texto **nome** , copie e cole o nome do atributo de valor **especificado** .

    c. Na caixa de texto **sobrenome** , copie e cole o nome do atributo do valor **sobrenome** .

### <a name="create-jive-test-user"></a>Criar usuário de teste do Jive

O objetivo desta seção é criar um usuário chamado Brenda Simon no jive. O Jive dá suporte ao provisionamento automático de usuário, que está habilitado por padrão. Você pode encontrar mais detalhes [aqui](jive-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuário.

Se você precisar criar o usuário manualmente, trabalhe com a [equipe de suporte ao cliente do Jive](https://www.jivesoftware.com/services-support/) para adicionar os usuários na plataforma jive.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Jive no painel de acesso, você deverá ser conectado automaticamente ao Jive para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Jive com o Azure AD](https://aad.portal.azure.com/)

- [O que é o controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Configurar provisionamento de usuário](jive-provisioning-tutorial.md)

- [Como proteger o Jive com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
