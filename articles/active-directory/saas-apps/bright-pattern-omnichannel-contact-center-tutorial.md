---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o padrão brilhante Omnichannel o centro de contato | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e o Omnichannel de padrão brilhante no centro de contatos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: de1e0091-ca10-4e7e-8014-f4579d8eabf6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27cda1f1a797ca0cb8e1b9d1c4cd7498c22ddde5
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081925"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bright-pattern-omnichannel-contact-center"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Omnichannel de padrão brilhante do centro de contato

Neste tutorial, você aprenderá a integrar o Omnichannel de padrão brilhante ao centro de contato com o Azure Active Directory (Azure AD). Ao integrar o padrão brilhante Omnichannel o centro de contato com o Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao centro de contatos Omnichannel de padrão brilhante.
* Habilite seus usuários a entrar automaticamente no Omnichannel de contato de padrão brilhante com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Omnichannel de contato com o padrão brilhante.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.



* Padrão brilhante Omnichannel o centro de contato dá suporte ao **SP e** ao SSO iniciado pelo IDP
* Padrão brilhante Omnichannel o centro de contato dá suporte ao provisionamento **de usuário just-in-time**


## <a name="adding-bright-pattern-omnichannel-contact-center-from-the-gallery"></a>Adicionando o Omnichannel de padrão brilhante do centro de contato da Galeria

Para configurar a integração do padrão brilhante do Omnichannel Contact Center ao Azure AD, você precisa adicionar o Omnichannel de padrão brilhante à sua lista de aplicativos SaaS gerenciados por meio da galeria.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Omnichannel padrão brilhante** na caixa de pesquisa.
1. Selecione **padrão brilhante Omnichannel o centro de contatos** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bright-pattern-omnichannel-contact-center"></a>Configurar e testar o logon único do Azure AD para o padrão brilhante Omnichannel o centro de contatos

Configure e teste o SSO do Azure AD com o Omnichannel de padrão brilhante do centro de contato usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Omnichannel de contatos de padrão brilhante.

Para configurar e testar o SSO do Azure AD com o padrão brilhante Omnichannel Contact Center, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar padrão brilhante Omnichannel o SSO do centro de contato](#configure-bright-pattern-omnichannel-contact-center-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do padrão brilhante Omnichannel Contact Center](#create-bright-pattern-omnichannel-contact-center-test-user)** – para ter um equivalente de B. Simon no Omnichannel de contatos de padrão brilhante que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **padrão brilhante Omnichannel Contact Center** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `<SUBDOMAIN>_sso`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.brightpattern.com/agentdesktop/sso/redirect`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.brightpattern.com/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Contate a [equipe de suporte ao cliente do Omnichannel com o padrão brilhante](mailto:support@brightpattern.com) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Padrão brilhante Omnichannel aplicativo do centro de contato espera as asserções do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/edit-attribute.png)

1. Além disso, o aplicativo de padrão brilhante Omnichannel Contact Center espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seu requisito.

    | Nome | Espaço de Nomes  |
    | ---------------| --------------- |
    | firstName | user.givenname |
    | lastName | User. sobrenome |
    | e-mail | user.mail |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar o centro de contatos Omnichannel de padrão brilhante** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao centro de contatos de padrão brilhante Omnichannel.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Omnichannel de padrão brilhante centro de contatos**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-bright-pattern-omnichannel-contact-center-sso"></a>Configurar padrão brilhante Omnichannel SSO do centro de contato

Para configurar o logon único no lado do **Omnichannel de padrão brilhante** , você precisa enviar o **certificado (Base64)** baixado e as URLs copiadas apropriadas do portal do Azure para a [equipe de suporte do Omnichannel de contato com o padrão brilhante](mailto:support@brightpattern.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-bright-pattern-omnichannel-contact-center-test-user"></a>Criar um usuário de teste do padrão brilhante Omnichannel Contact Center

Nesta seção, um usuário chamado B. Simon é criado no centro de contatos Omnichannel de padrão brilhante. O padrão brilhante Omnichannel o centro de contato dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no centro de contatos Omnichannel de padrão brilhante, um novo será criado após a autenticação.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco de padrão brilhante Omnichannel do centro de contato no painel de acesso, você deverá entrar automaticamente no Omnichannel de contato de padrão brilhante para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o padrão brilhante Omnichannel o centro de contato com o Azure AD](https://aad.portal.azure.com/)

