---
title: 'Tutorial: integração do Azure Active Directory com o LucidChart | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o LucidChart.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3eace60445dc9d52f9690da74360282efbb4cbe5
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76291216"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-lucidchart"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o LucidChart

Neste tutorial, você aprenderá a integrar o LucidChart com o Azure Active Directory (Azure AD). Ao integrar o LucidChart ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao LucidChart.
* Habilite seus usuários a serem conectados automaticamente ao LucidChart com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do LucidChart.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O LucidChart dá suporte ao SSO iniciado por **SP**
* O LucidChart dá suporte ao provisionamento **de usuário just in time**
* Depois de configurar o LucidChart, você pode impor controles de sessão, que protegem vazamento e pós-infiltração dos dados confidenciais de sua organização em tempo real. Os controles de sessão se estendem do acesso condicional. [Saiba como impor o controle de sessão com Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-lucidchart-from-the-gallery"></a>Adicionando o LucidChart da Galeria

Para configurar a integração do LucidChart ao Azure AD, você precisará adicionar o LucidChart da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **LucidChart** na caixa de pesquisa.
1. Selecione **LucidChart** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-lucidchart"></a>Configurar e testar o logon único do Azure AD para o LucidChart

Configure e teste o SSO do Azure AD com o LucidChart usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no LucidChart.

Para configurar e testar o SSO do Azure AD com o LucidChart, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do LucidChart](#configure-lucidchart-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do LucidChart](#create-lucidchart-test-user)** – para ter um equivalente de B. Simon em LucidChart que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **LucidChart** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

   Na caixa de texto **URL de logon** , digite uma URL como: `https://chart2.office.lucidchart.com/saml/sso/azure`

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Na seção **Configurar LucidChart** , copie as URLs apropriadas de acordo com seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao LucidChart.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **LucidChart**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-lucidchart-sso"></a>Configurar o SSO do LucidChart

1. Em uma janela diferente do navegador da Web, faça logon em seu site de empresa do LucidChart como administrador.

2. No menu na parte superior, clique em **equipe**.

    ![Time](./media/lucidchart-tutorial/ic791190.png "Equipa")

3. Clique em **aplicativos \> gerenciar SAML**.

    ![Gerenciar SAML](./media/lucidchart-tutorial/ic791191.png "Gerenciar SAML")

4. Na página de diálogo **configurações de autenticação do SAML** , execute as seguintes etapas:

    a. Selecione **habilitar autenticação SAML**e, em seguida, clique em **opcional**.

    ![Configurações de autenticação do SAML](./media/lucidchart-tutorial/ic791192.png "Configurações de autenticação do SAML")

    b. Na caixa de texto **domínio** , digite seu domínio e clique em **alterar certificado**.

    ![Alterar certificado](./media/lucidchart-tutorial/ic791193.png "Alterar certificado")

    c. Abra o arquivo de metadados baixado, copie o conteúdo e cole-o na caixa de texto **carregar metadados** .

    ![Carregar metadados](./media/lucidchart-tutorial/ic791194.png "Carregar metadados")

    d. Selecione **Adicionar novos usuários automaticamente à equipe**e, em seguida, clique em **salvar alterações**.

    ![Salvar alterações](./media/lucidchart-tutorial/ic791195.png "Salvar alterações")

### <a name="create-lucidchart-test-user"></a>Criar usuário de teste do LucidChart

Não há nenhum item de ação para você configurar o provisionamento de usuário para LucidChart.  Quando um usuário atribuído tenta fazer logon no LucidChart usando o painel de acesso, o LucidChart verifica se o usuário existe.  

Se ainda não houver nenhuma conta de usuário disponível, ela será criada automaticamente pelo LucidChart.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do LucidChart no painel de acesso, você deverá ser conectado automaticamente ao LucidChart para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o LucidChart com o Azure AD](https://aad.portal.azure.com/)

- [O que é o controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o LucidChart com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
