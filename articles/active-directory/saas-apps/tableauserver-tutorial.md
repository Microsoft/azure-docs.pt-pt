---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Tableau Server  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d19a7cc8d81f9e6e913f147b24c5cce03ff82027
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986738"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com o Tableau Server

Neste tutorial, você vai aprender a integrar o Tableau Server com o Azure Ative Directory (Azure AD). Quando integrar o Tableau Server com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Tableau Server.
* Ative que os seus utilizadores sejam automaticamente inscritos no Tableau Server com as suas contas Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* A subscrição ativada pelo Tableau Server (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Tableau Server suporta **SP** iniciado SSO
* Assim que configurar o Tableau Server, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controle de sessão com Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-server-from-the-gallery"></a>Adicionar Tableau Server da galeria

Para configurar a integração do Tableau Server em Azure AD, precisa adicionar o Tableau Server da galeria à sua lista de aplicações saaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. No Add da secção **galeria,** digite **O Servidor Tableau** na caixa de pesquisa.
1. Selecione **Tableau Server** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tableau-server"></a>Configure e teste Azure AD único sign-on para Tableau Server

Configure e teste Azure AD SSO com Tableau Server utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Tableau Server.

Para configurar e testar o Azure AD SSO com o Tableau Server, complete os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configure tableau Server SSO](#configure-tableau-server-sso)** - para configurar as definições de sinal única no lado da aplicação.
    1. **[Create Tableau Server utilizador](#create-tableau-server-test-user)** de teste - para ter uma contraparte de B.Simon no Tableau Server que está ligado à representação do utilizador da AD Azure.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Tableau Server,** encontre a secção **Gerir** e selecione **um único sinal .**
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão: `https://azure.<domain name>.link`

    b. Na caixa **de identificador,** digite um URL utilizando o seguinte padrão: `https://azure.<domain name>.link`

    c. Na caixa de texto **URL de resposta,** escreva um URL utilizando o seguinte padrão: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Os valores anteriores não são valores reais. Atualize os valores com o URL real e identificador a partir da página de configuração do Tableau Server, o que é explicado mais tarde no tutorial.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na secção Configurar o **Servidor tableau,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao Tableau Server.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Tableau Server**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-tableau-server-sso"></a>Configure Tableau Server SSO

1. Para configurar o SSO para a sua aplicação, precisa de se inscrever no seu inquilino do Tableau Server como administrador.

2. No separador **CONFIGURAÇÃO,** selecione **identidade e acesso**do utilizador e, em seguida, selecione o separador Método de **Autenticação.**

    ![Configurar o início de sessão único](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Na página **DE CONFIGURAÇÃO,** execute os seguintes passos:

    ![Configurar o início de sessão único](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. Para obter método de **autenticação,** selecione SAML.

    b. Selecione a caixa de verificação de **Autenticação Ativa sAML para o servidor**.

    c. URL de devolução do Servidor tableau — O URL a que os utilizadores do Tableau Server vão aceder, como <http://tableau_server>. Não é recomendável a utilização `http://localhost`. Não é suportado utilizar um URL com um corte de rasto (por exemplo, `http://tableau_server/`). Copy **Tableau Server devolver URL** e colá-lo para assinar caixa de texto **URL** na secção básica **de configuração SAML** no portal Azure

    d. ID da entidade SAML — O ID da entidade identifica exclusivamente a instalação do Seu Servidor Tableau para o IdP. Pode introduzir o url do Tableau Server novamente aqui, se quiser, mas não tem de ser o url do Tableau Server. Copie **o ID da entidade SAML** e cole-o na caixa de texto **identificador** na secção **de configuração Básica SAML** no portal Azure

    e. Clique no Ficheiro de **Metadados XML de download** e abra-o na aplicação do editor de texto. Localize o URL do Serviço de Consumidor de Afirmação com http post e índice 0 e copie o URL. Colá-lo agora na caixa de texto **URL de resposta** na secção de **configuração Básica SAML** no portal Azure

    f. Localize o ficheiro de Metadados da Federação descarregado do portal Azure e, em seguida, carregue-o no ficheiro de **metadados SAML IDp**.

    g. Introduza os nomes dos atributos que o IDP utiliza para manter os nomes dos utilizadores, nomes de visualização e endereços de e-mail.

    h. Clicar em **Guardar**

    > [!NOTE]
    > O cliente tem de carregar qualquer certificado na configuração Tableau Server SAML SSO e será ignorado no fluxo SSO. Se precisar de ajuda para configurar o SAML no Tableau Server, consulte este artigo [Configure SAML](https://help.tableau.com/current/online/en-us/saml_config_azure_ad.htm).

### <a name="create-tableau-server-test-user"></a>Criar o utilizador de teste do Tableau Server

O objetivo desta secção é criar um utilizador chamado B.Simon no Tableau Server. Tem de fornecer todos os utilizadores do servidor Tableau.

Este nome de utilizador do utilizador deve corresponder ao valor configurado no atributo personalizado do Nome de **utilizador**Azure AD . Com o mapeamento correto, a integração deve funcionar Configurando o Single Sign-On azure AD.

> [!NOTE]
> Se necessitar de criar um utilizador manualmente, tem de contactar o administrador do Tableau Server na sua organização.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no azulejo tableau Server no Painel de Acesso, deve ser automaticamente inscrito no Tableau Server para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Tableau Server com AD Azure](https://aad.portal.azure.com/)

- [O que é o controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)