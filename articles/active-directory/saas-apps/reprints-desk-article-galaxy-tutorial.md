---
title: 'Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com Reprints Desk - Article Galaxy [ Artigo Galáxia ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Reprints Desk - Article Galaxy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 00fd0b0e-8de1-4e64-8a9c-4b65c0e47fe0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d94fc0bc736d2136d4711ab02ae554605deeb35
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76761267"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-reprints-desk---article-galaxy"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com Reprints Desk - Artigo Galaxy

Neste tutorial, você vai aprender a integrar reprints Desk - Article Galaxy with Azure Ative Directory (Azure AD). Quando integrar reprints Desk - Article Galaxy com Azure AD, pode:

* Controle em Azure AD que tem acesso a Reprints Desk - Article Galaxy.
* Permita que os seus utilizadores sejam automaticamente inscritos no Reprints Desk - Article Galaxy com as suas contas Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Reprints Desk - Artigo Galaxy único sinal de subscrição (SSO) habilitado por subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Reprints Desk - Artigo Galaxy suporta **IDP** iniciado SSO

* Reprints Desk - Artigo Galaxy suporta o fornecimento de utilizadores **justo no tempo**

* [Assim que configurar o Reprints Desk - Article Galaxy pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se a partir do Acesso Condicional. Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-reprints-desk---article-galaxy-from-the-gallery"></a>Adicionar Reprints Desk - Artigo Galaxy da galeria

Para configurar a integração do Reprints Desk - Article Galaxy em Azure AD, você precisa adicionar Reprints Desk - Article Galaxy da galeria à sua lista de aplicações geridas saaS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. No Add da secção **de galeria,** escreva **Reprints Desk - Article Galaxy** na caixa de pesquisa.
1. Selecione **Reprints Desk - Article Galaxy** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-reprints-desk---article-galaxy"></a>Configure e teste Azure AD único sign-on para Reprints Desk - Artigo Galaxy

Configure e teste Azure AD SSO com Reprints Desk - Article Galaxy usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Reprints Desk - Article Galaxy.

Para configurar e testar o Azure AD SSO com reprints Desk - Article Galaxy, complete os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configure Reprints Desk Article Galaxy SSO](#configure-reprints-desk-article-galaxy-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Create Reprints Desk Article Galaxy test user](#create-reprints-desk-article-galaxy-test-user)** - para ter uma contrapartida de B.Simon em Reprints Desk - Artigo Galaxy que está ligado à representação do utilizador da AD Azure.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações do **Artigo Galaxy,** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , o aplicativo é pré-configurado e as URLs necessárias já foram preenchidas previamente com o Azure. O usuário precisa salvar a configuração clicando no botão **salvar** .


1. Reprints Desk - Aplicação do Artigo Galaxy espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos saml. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/default-attributes.png)

1. Além de acima, reprints Desk - Aplicação article Galaxy espera que poucos atributos sejam passados na resposta SAML que são mostradas abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de origem|
    | ------------ | --------- |
    | FirstName | user.givenname |
    | LastName | User. sobrenome |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na **Configuração Reprints Desk - Secção Article Galaxy,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use o único sign-on Azure, concedendo acesso a Reprints Desk - Article Galaxy.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Reprints Desk - Article Galaxy**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-reprints-desk-article-galaxy-sso"></a>Configure Reprints Desk Article Galaxy SSO

Para configurar um único sinal no **Reprints Desk - Lado do Artigo Galaxy,** você precisa enviar o descarregamento da **Federação Metadados XML** e URLs copiados apropriados do portal Azure para [Reprints Desk - Equipa](mailto:customersupport@reprintsdesk.com)de suporte do Artigo Galaxy . Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-reprints-desk-article-galaxy-test-user"></a>Criar reimpressões de reimpressão do utilizador do teste Galaxy

Nesta secção, um utilizador chamado B.Simon é criado em Reprints Desk - Article Galaxy. Reprints Desk - Article Galaxy suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para você nesta seção. Se um utilizador já não existir no Reprints Desk - Article Galaxy, um novo é criado após a autenticação.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar na Reprints Desk - Artigo Galaxy tile no Painel de Acesso, deve ser automaticamente inscrito no Reprints Desk - Article Galaxy para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente reimprimir desk - Artigo Galaxy com Anúncio Azure](https://aad.portal.azure.com/)

- [O que é o controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger reimpressões Desk - Artigo Galaxy com visibilidade avançada e controlos](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
