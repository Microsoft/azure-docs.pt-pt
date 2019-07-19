---
title: 'Tutorial: Integração de Azure Active Directory com o JFrog de artefato | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e o artefato JFrog.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 767cb0fc-048c-412b-a8ad-fe52daeeb02d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ed6c2ced87fb3ce51c44f63042c3eb496c1c2f9
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68327172"
---
# <a name="tutorial-integrate-jfrog-artifactory-with-azure-active-directory"></a>Tutorial: Integrar o JFrog artefato com Azure Active Directory

Neste tutorial, você aprenderá a integrar o JFrog Artefatoy com o Azure Active Directory (Azure AD). Ao integrar o JFrog Artefatoy ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao JFrog artefato.
* Habilite seus usuários a serem conectados automaticamente ao JFrog artefato com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do JFrog Artefatory.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O artefato JFrog é compatível com o SSO iniciado por **SP e IDP**
* O JFrog Artefatory dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-jfrog-artifactory-from-the-gallery"></a>Adicionando o JFrog artefato da Galeria

Para configurar a integração do JFrog Artefatory ao Azure AD, você precisará adicionar o JFrog artefato da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **JFrog artefato** na caixa de pesquisa.
1. Selecione **JFrog artefato** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste o SSO do Azure AD com o JFrog Artefatory usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no JFrog artefato.

Para configurar e testar o SSO do Azure AD com o JFrog artefato, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO de artefato de JFrog](#configure-jfrog-artifactory-sso)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
5. **[Criar usuário de teste do JFrog artefatory](#create-jfrog-artifactory-test-user)** – para ter um equivalente de B. Simon no artefato JFrog que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **JFrog artefatory** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , insira os valores para os seguintes campos:

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão:`<servername>.jfrog.io`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:`https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://<servername>.jfrog.io/<servername>/webapp/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Contate a [equipe de suporte ao cliente do JFrog artefatoy](https://support.jfrog.com) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. O aplicativo JFrog de artefatos espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo atributos de usuário.

    ![image](common/edit-attribute.png)

1. Além de acima, o aplicativo de artefatos JFrog espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **atributos de usuário & declarações** na caixa de diálogo **declarações de grupo (versão prévia)**  , execute as seguintes etapas:

    a. Clique na **caneta** ao lado dos **grupos retornados na declaração**.

    ![image](./media/jfrog-artifactory-tutorial/config04.png)

    ![image](./media/jfrog-artifactory-tutorial/config05.png)

    b. Selecione **todos os grupos** na lista de botões de opção.

    c. Clique em **Guardar**.

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize **certificado (bruto)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificateraw.png)

6. Na seção **Configurar o artefato JFrog** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-jfrog-artifactory-sso"></a>Configurar SSO do JFrog de artefatos

Para configurar o logon único no lado do **JFrog artefato** , você precisa enviar o **certificado (bruto)** baixado e as URLs copiadas apropriadas de portal do Azure para a [equipe de suporte do JFrog artefatoy](https://support.jfrog.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao JFrog artefato.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **JFrog artefato**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-jfrog-artifactory-test-user"></a>Criar usuário de teste do JFrog Artefatory

Nesta seção, um usuário chamado B. Simon é criado no artefato JFrog. O JFrog Artefatory dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no JFrog Artefatoy, um novo será criado após a autenticação.

### <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco de artefatos JFrog no painel de acesso, você deverá ser automaticamente conectado ao artefato JFrog para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

