---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com wedo  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o WEDO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 73adc94e-7656-4a92-99e3-a401c67be477
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ce7ffb389a585511883c3b35de3773ae37342b8
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992374"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wedo"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com wedo

Neste tutorial, você vai aprender a integrar wedo com o Azure Ative Directory (Azure AD). Quando integrar o WEDO com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao WEDO.
* Ative que os seus utilizadores sejam automaticamente inscritos no WEDO com as suas contas Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Wedo single sign-on (SSO) enabled subscrição. Contacte a equipa de suporte ao [cliente WEDO](mailto:info@wedo.swiss) para obter uma subscrição SSO.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* WEDO apoia **SP e IDP** iniciadoS SSO

* [Assim que configurar o WEDO, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se a partir do Acesso Condicional. Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-wedo-from-the-gallery"></a>Adicionando WEDO da galeria

Para configurar a integração do WEDO em Azure AD, precisa adicionar WEDO da galeria à sua lista de aplicações saaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. No Add da secção **galeria,** digite **WEDO** na caixa de pesquisa.
1. Selecione **WEDO** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-wedo"></a>Configure e teste Azure AD único signo para WEDO

Configure e teste Azure AD SSO com WEDO utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no WEDO.

Para configurar e testar o Azure AD SSO com o WEDO, complete os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configure WEDO SSO](#configure-wedo-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * Criar o utilizador de **[teste WEDO](#create-wedo-test-user)** - para ter uma contraparte de B.Simon no WEDO que esteja ligada à representação azure AD do utilizador.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **WEDO,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.wedo.swiss/sp/acs`

    b. Na caixa de texto **URL de resposta,** escreva um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.wedo.swiss/sp/acs`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.wedo.swiss/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Contacte a equipa de apoio ao [cliente WEDO](mailto:info@wedo.swiss) para obter estes valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. A aplicação WEDO espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    | Nome | Atributo de origem|
    | ------------ | --------- |
    | e-mail | Usuário. email |
    | firstName | user.firstName |
    | lastName | user.lasttName |
    | userName | user.userName |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na secção **SET UP WEDO,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao WEDO.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **WEDO**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-wedo-sso"></a>Configure WEDO SSO

Siga estes passos para ativar o Azure AD SSO no WEDO.

1. Faça log in [WEDO](https://login.wedo.swiss/). Precisa de ter um papel de **administrador.**
1. Nas definições de Perfil, selecione o menu **Autenticação** nas **definições**da rede da secção .
1. Na página de **Autenticação SAML,** execute os seguintes passos:

   ![Ligação de autenticação SAML](media/wedo-tutorial/network-security-authentification.png)

   a. Ativar **a autenticação SAML**.

   b. Selecione o separador de **metadados do Fornecedor de Identidade (XML).**

   c. Abra o **xml** de metadados da Federação descarregado do portal Azure para o Notepad e copie o conteúdo dos metadados XML e cole-o na caixa de texto **x.509 Certificate.**

   d. Clique em **Guardar**

### <a name="create-wedo-test-user"></a>Criar o utilizador de teste WEDO

Nesta secção, você vai criar um utilizador de teste no WEDO chamado Bob Simon. As informações devem corresponder à *Create a Azure AD test user*.

1. A partir da definição de Perfil no WEDO, selecione **Utilizadores** da secção de definições da *Rede.*
1. Clique em **Adicionar utilizador**.
1. No popup do utilizador Adicionar, preencha as informações do utilizador

    a. Primeiro nome `B`.

    b. Apelido `Simon`.

    c. Insira o e-mail `username@companydomain.extension`. Por exemplo, `B.Simon@contoso.com`. É obrigatório ter e-mail com o mesmo domínio que o nome curto da sua empresa.

    d. Tipo de utilizador `User`.

    e. Clique em **criar usuário**.

    f. Na página *'Seleccionar'* de equipas, clique em **Guardar**.

    g.  Na página de *utilizador convidar,* clique **em Sim**.

1. Validar o utilizador usando o link que recebeu por e-mail

> [!NOTE]
> Se pretender criar um utilizador falso (o e-mail acima não existe na sua rede), contacte o [nosso suporte](mailto:info@wedo.swiss) para validar o utilizador*.

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no azulejo WEDO no Painel de Acesso, deverá ser automaticamente inscrito no WEDO para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente WEDO com Azure AD](https://aad.portal.azure.com/)

- [O que é o controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o WEDO com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
