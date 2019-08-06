---
title: 'Tutorial: Integração do Azure Active Directory com o Learning at Work | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o aprendizado no trabalho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d607174-bea1-4f40-8233-54cabe02c66a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f04e6f8df55059e2aa2981f85f40e487c6f0f8b
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823682"
---
# <a name="tutorial-integrate-learning-at-work-with-azure-active-directory"></a>Tutorial: Integre o aprendizado em trabalho com o Azure Active Directory

Neste tutorial, você aprenderá a integrar o Learning at Work ao Azure Active Directory (Azure AD). Ao integrar o Learning at Work ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Learning at Work.
* Habilite seus usuários a entrarem automaticamente no Learning at Work com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único (SSO) do Learning at Work.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Learning at Work dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-learning-at-work-from-the-gallery"></a>Adicionando o Learning at Work da Galeria

Para configurar a integração do Learning at Work ao Azure AD, você precisará adicionar o Learning at Work da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Learning at Work** na caixa de pesquisa.
1. Selecione **Learning at Work** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste o SSO do Azure AD com o Learning at Work usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Learning at Work.

Para configurar e testar o SSO do Azure AD com o Learning at Work, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configure o aprendizado de aprendizagem no SSO](#configure-learning-at-work-sso)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
5. **[Criar usuário de teste do Learning at Work](#create-learning-at-work-test-user)** – para ter um equivalente de B. Simon no Learning at Work que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Learning at Work** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://<subdomain>.sabacloud.com/Saba/Web/<company code>`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão:`https://<subdomain>.sabacloud.com/Saba/saml/SSO/alias/<company name>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contate a [equipe de suporte ao cliente do Learning at Work](https://www.learninga-z.com/site/contact/support) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. O aplicativo Learning at Work espera que as asserções SAML estejam em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão, onde o **nameidentifier** é mapeado com **User. UserPrincipalName**.

    Você pode atualizar o valor de **nameidentifier** no Azure AD com base na configuração da sua organização e esse valor precisa corresponder à **ID de usuário** na nuvem do Saba, para que você precise editar o mapeamento de atributo clicando no ícone **Editar**  e alterar o mapeamento de atributo.

    ![image](common/edit-attribute.png)

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Na seção **Configurar aprendizado no trabalho** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-learning-at-work-sso"></a>Configurar o SSO do Learning at Work

Para configurar o logon único no lado do **Learning at Work** , é necessário enviar o XML de **metadados de Federação** baixado e as URLs copiadas apropriadas do portal do Azure para a [equipe de suporte do Learning at Work](https://www.learninga-z.com/site/contact/support). Eles definem essa configuração para que a conexão de SSO do SAML seja definida corretamente em ambos os lados

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao Learning at Work.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Learning at Work**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-learning-at-work-test-user"></a>Criar usuário de teste do Learning at Work

Nesta seção, você criará um usuário chamado B. Simon no Learning at Work. Trabalhe com a [equipe de suporte do Learning at Work](https://www.learninga-z.com/site/contact/support) para adicionar os usuários na plataforma Learning at Work. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco Learning at Work no painel de acesso, você deverá entrar automaticamente no Learning at Work para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

