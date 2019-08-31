---
title: 'Tutorial: Azure Active Directory integração de SSO (logon único) com pessoas | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e as pessoas.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7c9b6202-11dd-4bb6-a679-8fb0a7a0ef4e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a9b8f08a54c978d81a8d33c61ab3d5f5fc7271f
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164225"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-people"></a>Tutorial: Azure Active Directory integração de SSO (logon único) com pessoas

Neste tutorial, você aprenderá a integrar pessoas com o Azure Active Directory (Azure AD). Ao integrar pessoas ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso às pessoas.
* Habilite seus usuários a serem conectados automaticamente a pessoas com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do People.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* As pessoas dão suporte ao SSO iniciado por **SP**
* O aplicativo móvel de pessoas agora pode ser configurado com o Azure AD para habilitar o SSO. Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

>[!NOTE]
>O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-people-from-the-gallery"></a>Adicionando pessoas da Galeria

Para configurar a integração de pessoas ao Azure AD, você precisa adicionar pessoas da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **pessoas** na caixa de pesquisa.
1. Selecione **pessoas** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-people"></a>Configurar e testar o logon único do Azure AD para pessoas

Configure e teste o SSO do Azure AD com pessoas usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no People.

Para configurar e testar o SSO do Azure AD com pessoas, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
2. **[Configurar o SSO de pessoas](#configure-people-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste de pessoas](#create-people-test-user)** – para ter um equivalente de B. Simon em pessoas que estejam vinculadas à representação de usuário do Azure AD.
6. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **People** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://<company name>.peoplehr.net`

    b. Na caixa **identificador** , digite uma URL:`https://www.peoplehr.com`

    c. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:`https://<company name>.peoplehr.net/Pages/Saml/ConsumeAzureAD.aspx`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e a URL de resposta reais. Contate a [equipe de suporte ao cliente do People](mailto:customerservices@peoplehr.com) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Na seção **Configurar pessoas** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso a pessoas.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **pessoas**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-people-sso"></a>Configurar SSO de pessoas

1. Para automatizar a configuração dentro das pessoas, você precisa instalar a **extensão do navegador de entrada seguro de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar pessoas** direcionará você para o aplicativo pessoas. A partir daí, forneça as credenciais de administrador para entrar em pessoas. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-6.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar pessoas manualmente, abra uma nova janela do navegador da Web e entre no site da empresa do People como administrador e execute as seguintes etapas:
   
4. No menu no lado esquerdo, clique em **configurações**.

    ![Configurar o início de sessão único](./media/people-tutorial/tutorial_people_001.png)

5. Clique em **empresa**.

    ![Configurar o início de sessão único](./media/people-tutorial/tutorial_people_002.png)

6. No **arquivo de metadados SAML do upload ' logon único '** , clique em **procurar** para carregar o arquivo de metadados baixado.

    ![Configurar o início de sessão único](./media/people-tutorial/tutorial_people_003.png)

### <a name="create-people-test-user"></a>Criar usuário de teste de pessoas

Nesta seção, você criará um usuário chamado B. Simon em pessoas. Trabalhe com a [equipe de suporte ao cliente do People](mailto:customerservices@peoplehr.com) para adicionar os usuários na plataforma de pessoas. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco pessoas no painel de acesso, você deverá ser conectado automaticamente às pessoas para as quais você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-people-mobile"></a>Testar SSO para pessoas (móvel)

1. Abra o aplicativo móvel de pessoas. Na página de entrada, insira a **ID de email** e clique em **logon único**.

    ![A entrada](./media/people-tutorial/test01.png)

2. Insira **userid da organização** e clique em **Avançar**.

    ![O email](./media/people-tutorial/test02.png)

3. Por fim, após entrar com êxito, a home page do aplicativo será exibida abaixo:

    ![Uma vez](./media/people-tutorial/test03.png)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente as pessoas com o Azure AD](https://aad.portal.azure.com)
