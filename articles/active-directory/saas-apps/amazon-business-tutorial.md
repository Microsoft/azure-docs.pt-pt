---
title: 'Tutorial: Integração do Azure Active Directory com o Amazon Business | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Amazon Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b2597a66-afd9-4f11-b14b-646b597bb6c2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7ac085beaa85a7ddf3a6c3bfc61820e8e5a63ea
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68496911"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>Tutorial: Integre o Amazon Business com o Azure Active Directory

Neste tutorial, você aprenderá a integrar o Amazon Business com o Azure Active Directory (Azure AD). Ao integrar o [Amazon Business](https://www.amazon.com/b2b/info/amazon-business?layout=landing) ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Amazon Business.
* Permita que os usuários sejam automaticamente conectados ao Amazon Business com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Uma assinatura habilitada para SSO (logon único) do Amazon Business. Acesse a página de [negócios do Amazon](https://www.amazon.com/business/register/org/landing?ref_=ab_reg_mlp) para criar uma conta de negócios do Amazon.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em uma conta de negócios do Amazon existente.

* Amazon Business dá suporte a **SP e** SSO iniciados pelo IDP
* O Amazon Business dá suporte ao provisionamento **de usuário just-in-time**

## <a name="adding-amazon-business-from-the-gallery"></a>Adicionando o Amazon Business por meio da Galeria

Para configurar a integração do Amazon Business ao Azure AD, você precisa adicionar o Amazon Business da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite empresa do **Amazon** na caixa de pesquisa.
1. Selecione **Amazon Business** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste o SSO do Azure AD com o Amazon Business usando um usuário de teste chamado **B. Simon**.

Para configurar e testar o SSO do Azure AD com o Amazon Business, conclua as seguintes etapas de compilação:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO de negócios da Amazon](#configure-amazon-business-sso)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
5. **[Criar usuário de teste do Amazon Business](#create-amazon-business-test-user)** – para ter um equivalente de B. Simon no Amazon Business que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Amazon Business** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar no modo iniciado pelo **IDP** , execute as seguintes etapas:

    1. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando um dos seguintes padrões:
    
       | | |
       |-|-|
       | `https://www.amazon.com`|
       | `https://www.amazon.co.jp`|
       | `https://www.amazon.de`|

    1. Na caixa de texto **URL de resposta** , digite uma URL usando um dos seguintes padrões:
    
       | | |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`|

       > [!NOTE]
       > O valor da URL de resposta não é real. Atualize esse valor com a URL de resposta real. Você obterá o `<idpid>` valor da seção de configuração de SSO do Amazon Business, que é explicada posteriormente no tutorial. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL:`https://www.amazon.com/`

1. A captura de tela a seguir mostra a lista de atributos padrão. Edite os atributos clicando no ícone **Editar** na seção **atributos de usuário & declarações** .

    ![Atributos](media/amazon-business-tutorial/map-attribute3.png)

1. Edite atributos e copie o valor do **namespace** desses atributos no bloco de notas.

    ![Atributos](media/amazon-business-tutorial/map-attribute4.png)

1. Além de acima, o aplicativo Amazon Business espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **atributos de usuário & declarações** na caixa de diálogo **declarações de grupo** , execute as seguintes etapas:

    a. Clique na **caneta** ao lado dos **grupos retornados na declaração**.

    ![image](./media/amazon-business-tutorial/config04.png)

    ![image](./media/amazon-business-tutorial/config05.png)

    b. Selecione **todos os grupos** na lista de botões de opção.

    c. Selecione a **ID do grupo** como atributo de **origem**.

    d. Marque a caixa **de seleção personalizar o nome da declaração de grupo** e insira o nome do grupo de acordo com o requisito da sua organização.

    e. Clique em **Guardar**.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize **metadados XML** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar o Amazon Business** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-amazon-business-sso"></a>Configurar o SSO de negócios do Amazon

1. Em uma janela diferente do navegador da Web, entre no site da empresa do Amazon Business como administrador.

1. Clique no **perfil do usuário** e selecione **configurações de negócios**.

    ![Perfil do Utilizador](media/amazon-business-tutorial/user-profile.png)

1. No assistente de **integração do sistema** , selecione **logon único (SSO)** .

    ![Início de Sessão Único (SSO)](media/amazon-business-tutorial/sso-settings.png)

1. No assistente de **configuração de SSO** , selecione o provedor de acordo com seus requisitos organizacionais e clique em **Avançar**.

    ![Grupo padrão](media/amazon-business-tutorial/default-group1.png)

1. No assistente de **novos padrões de conta de usuário** , selecione o **grupo padrão** e, em seguida, selecione **função de compra padrão** de acordo com a função de usuário em sua organização e clique em **Avançar**.

    ![Grupo padrão](media/amazon-business-tutorial/dafault-group2.png)

1. No assistente **carregar seu arquivo de metadados** , clique em **procurar** para carregar o arquivo **XML de metadados** , que você baixou do portal do Azure e clique em **carregar**.

    ![Dados de conexão](media/amazon-business-tutorial/connection-data1.png)

1. Depois de carregar o arquivo de metadados baixado, os campos na seção **dados de conexão** serão preenchidos automaticamente. Depois disso, clique em **Avançar**.

    ![Dados de conexão](media/amazon-business-tutorial/connection-data2.png)

1. No assistente **carregar sua instrução de atributo** , clique em **ignorar**.

    ![Atributos](media/amazon-business-tutorial/map-attribute1.png)

1. No assistente de **mapeamento de atributo** , adicione os campos de requisito clicando na opção **+ Adicionar um campo** . Adicione os valores de atributo, incluindo o namespace, que você copiou da seção **atributos de usuário & declarações** de portal do Azure para o campo **AttributeName SAML** e clique em **Avançar**.

    ![Atributos](media/amazon-business-tutorial/map-attribute2.png)

1. No assistente de **dados de conexão do Amazon** , clique em **Avançar**.

    ![Ligação](media/amazon-business-tutorial/amazon-connect.png)

1. Verifique o **status** das etapas que foram configuradas e clique em **Iniciar teste**.

    ![Ligação](media/amazon-business-tutorial/sso-connection1.png)

1. No assistente de **conexão de SSO de teste** , clique em **testar**.

    ![Ligação](media/amazon-business-tutorial/sso-connection2.png)

1. No assistente de **URL iniciada pelo IDP** , antes de clicar em **Ativar**, copie o valor atribuído a **idpid** e cole-o no parâmetro **idpid** na **URL de resposta** na seção de **configuração básica do SAML** no Azure Portal.

    ![Ligação](media/amazon-business-tutorial/sso-connection3.png)

1. No assistente **você está pronto para alternar para SSO ativo?** , marque a caixa de seleção **SSO totalmente testado e pronto para entrar em tempo real** e clique em **alternar para ativo**.

    ![Ligação](media/amazon-business-tutorial/sso-connection4.png)

1. Por fim, na seção **detalhes da conexão SSO** , o **status** é mostrado como **ativo**.

    ![Ligação](media/amazon-business-tutorial/sso-connection5.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

> [!NOTE]
> Os administradores precisam criar os usuários de teste em seu locatário, se necessário. As etapas a seguir mostram como criar um usuário de teste.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Criar um grupo de segurança do Azure AD no portal do Azure

1. Clique em **Azure Active Directory > todos os grupos**.

    ![Criar um grupo de segurança do Azure AD](./media/amazon-business-tutorial/all-groups-tab.png)

1. Clique em **novo grupo**:

    ![Criar um grupo de segurança do Azure AD](./media/amazon-business-tutorial/new-group-tab.png)

1. Preencha o **tipo de grupo**, **nome do grupo**, descrição do **grupo**, tipo de **Associação**. Clique na seta para selecionar membros e procure ou clique no membro que você deseja adicionar ao grupo. Clique em **selecionar** para adicionar os membros selecionados e, em seguida, clique em **criar**.

    ![Criar um grupo de segurança do Azure AD](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao Amazon Business.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Amazon Business**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

    >[!NOTE]
    > Se você não atribuir os usuários no Azure AD, obterá o seguinte erro.

    ![O link Adicionar usuário](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Atribuir o grupo de segurança do Azure AD no portal do Azure

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **empresa da Amazon**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **Amazon Business**.

    ![O link de negócios do Amazon na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique em **Adicionar usuário**.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Pesquise o grupo de segurança que você deseja usar e clique no grupo para adicioná-lo à seção selecionar membros. Clique em **selecionar**e em **atribuir**.

    ![Pesquisar grupo de segurança](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > Verifique as notificações na barra de menus para ser notificado de que o grupo foi atribuído com êxito ao aplicativo empresarial no portal do Azure.

### <a name="create-amazon-business-test-user"></a>Criar usuário de teste do Amazon Business

Nesta seção, um usuário chamado B. Simon é criado no Amazon Business. O Amazon Business dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no Amazon Business, um novo será criado após a autenticação.

### <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Amazon Business no painel de acesso, você deverá entrar automaticamente no Amazon Business para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
