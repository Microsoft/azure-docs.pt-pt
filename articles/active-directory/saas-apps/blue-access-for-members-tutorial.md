---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com acesso azul para Membros (BAM) | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e o acesso azul para Membros (BAM).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2680ada0-88e4-46bd-8f08-e9e7ae8aafcd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7dd09f80721bb4180eb5f2c25f3315e6211c63cc
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74085472"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-blue-access-for-members-bam"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com acesso azul para Membros (BAM)

Neste tutorial, você aprenderá a integrar o acesso azul para Membros (BAM) com o Azure Active Directory (Azure AD). Ao integrar o acesso azul para os membros (BAM) com o Azure AD, você pode:

* Controle no Azure AD que tem acesso ao BAM (acesso azul para membros).
* Habilite seus usuários a entrar automaticamente no acesso azul para Membros (BAM) com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) habilitado para Membros (acesso a azul).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.


* O acesso azul para Membros (BAM) dá suporte ao SSO iniciado pelo **IDP**




## <a name="adding-blue-access-for-members-bam-from-the-gallery"></a>Adicionando acesso azul para Membros (BAM) da Galeria

Para configurar a integração do acesso azul para os membros (BAM) ao Azure AD, você precisa adicionar acesso azul para Membros (BAM) da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **acesso azul para Membros (BAM)** na caixa de pesquisa.
1. Selecione **acesso azul para Membros (BAM)** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-blue-access-for-members-bam"></a>Configurar e testar o logon único do Azure AD para acesso azul para Membros (BAM)

Configure e teste o SSO do Azure AD com acesso azul para Membros (BAM) usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no acesso azul para Membros (BAM).

Para configurar e testar o SSO do Azure AD com o acesso azul para Membros (BAM), conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO (acesso azul para membros) do Bam](#configure-blue-access-for-members-bam-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do acesso Blue for Members (BAM)](#create-blue-access-for-members-bam-test-user)** – para ter um equivalente de B. Simon no acesso azul para Membros (BAM) vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **acesso azul para Membros (BAM)** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `<Custom Domain Value>`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<CUSTOMURL>/affwebservices/public/saml2assertionconsumer`

    c. Clique em **definir URLs adicionais**.

    d. Na caixa de texto **estado de retransmissão** , digite uma URL usando o seguinte padrão: `https://<CUSTOMURL>/BAMSSOServlet/sso/BamInboundSsoServlet`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e o estado de retransmissão reais. Contate a [equipe de suporte ao cliente do acesso Blue para Membros (BAM)](https://www.bcbstx.com/contact-us) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. O aplicativo BAM (acesso azul para membros) espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além de acima, o aplicativo BAM (acesso azul para membros) espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.

    | Nome |  Atributo de origem|
    | ------------ | --------- |
    | ClientID | `<ClientID>` |
    | UID | `<UID>` |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar o acesso azul para Membros (BAM)** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao BAM (acesso azul para membros).

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **acesso azul para Membros (BAM)** .
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-blue-access-for-members-bam-sso"></a>Configurar o SSO (acesso azul para membros) do BAM

Para configurar o logon único no lado do **Bam (acesso azul para membros)** , você precisa enviar o XML de **metadados de Federação** baixado e URLs copiadas apropriadas de portal do Azure para a [equipe de suporte do Bam (acesso azul para membros)](https://www.bcbstx.com/contact-us). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-blue-access-for-members-bam-test-user"></a>Criar usuário de teste do acesso azul para Membros (BAM)

Nesta seção, você criará um usuário chamado B. Simon no acesso azul para Membros (BAM). Trabalhe com a [equipe de suporte do Bam (acesso azul para membros)](https://www.bcbstx.com/contact-us) para adicionar os usuários na plataforma de acesso azul para Membros (BAM). Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco acesso azul para Membros (BAM) no painel de acesso, você deverá entrar automaticamente no acesso azul para Membros (BAM) para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o acesso azul para Membros (BAM) com o Azure AD](https://aad.portal.azure.com/)