---
title: 'Tutorial: integração de Azure Active Directory com a área restrita Salesforce | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e a área restrita do Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9d6d11b6b56483f954049fdc1858db31f35c14a
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76290008"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce-sandbox"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com a área restrita do Salesforce

Neste tutorial, você aprenderá a integrar a área restrita do Salesforce com o Azure Active Directory (Azure AD). Ao integrar a área restrita do Salesforce ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso à área restrita Salesforce.
* Habilite seus usuários a serem conectados automaticamente à área restrita Salesforce com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único (SSO) do Salesforce sandbox.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* A área restrita Salesforce é compatível com o SSO iniciado por **SP e IDP**
* A área restrita Salesforce dá suporte ao provisionamento **de usuário just in time**
* A área restrita Salesforce dá suporte ao [provisionamento **automatizado** de usuários](salesforce-sandbox-provisioning-tutorial.md)
* Depois de configurar a área restrita Salesforce, você pode impor controles de sessão, que protegem vazamento e pós-infiltração dos dados confidenciais de sua organização em tempo real. Os controles de sessão se estendem do acesso condicional. [Saiba como impor o controle de sessão com Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Adicionando a área restrita Salesforce da Galeria

Para configurar a integração da área restrita do Salesforce ao Azure AD, você precisa adicionar a área restrita Salesforce da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **área restrita Salesforce** na caixa de pesquisa.
1. Selecione a **área restrita Salesforce** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce-sandbox"></a>Configurar e testar o logon único do Azure AD para a área restrita do Salesforce

Configure e teste o SSO do Azure AD com a área restrita do Salesforce usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado na área restrita do Salesforce.

Para configurar e testar o SSO do Azure AD com a área restrita do Salesforce, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    * **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO da área restrita Salesforce](#configure-salesforce-sandbox-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    * **[Criar usuário de teste do Salesforce sandbox](#create-salesforce-sandbox-test-user)** – para ter um equivalente de B. Simon na área restrita Salesforce que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos da **área restrita Salesforce** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , se você tiver um **arquivo de metadados do provedor de serviços** e quiser configurar no modo iniciado pelo **IDP** , execute as seguintes etapas:

    a. Clique em **carregamento de ficheiro de metadados**.

    ![Carregar arquivo de metadados](common/upload-metadata.png)

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    ![escolher arquivo de metadados](common/browse-upload-metadata.png)

    > [!NOTE]
    > Você obterá o arquivo de metadados do provedor de serviços no portal de administração da área restrita do Salesforce, que é explicado posteriormente no tutorial.

    c. Depois que o arquivo de metadados for carregado com êxito, o valor da **URL de resposta** será preenchido automaticamente na caixa de texto **URL de resposta** .

    ![imagem](common/both-replyurl.png)

    > [!Note]
    > Se o valor da **URL de resposta** não obtiver polulated automático, preencha o valor manualmente de acordo com seu requisito.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **XML de metadados** das opções especificadas de acordo com seu requisito e salve-o em seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Na seção **Configurar a área restrita Salesforce** , copie as URLs apropriadas de acordo com seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso à área restrita Salesforce.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Salesforce sandbox**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-salesforce-sandbox-sso"></a>Configurar SSO da área restrita Salesforce

1. Abra uma nova guia no navegador e entre na sua conta de administrador da área restrita Salesforce.

2. Clique na **configuração** no **ícone configurações** no canto superior direito da página.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/configure1.png)

3. Role para baixo até as **configurações** no painel de navegação esquerdo, clique em **identidade** para expandir a seção correspondente. Em seguida, clique em **configurações de logon único**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. Na página **configurações de logon único** , clique no botão **Editar** .

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/configure3.png)

5. Selecione **SAML habilitado**e, em seguida, clique em **salvar**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Para configurar suas configurações de logon único do SAML, clique em **novo do arquivo de metadados**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Clique em **escolher arquivo** para carregar o arquivo XML de metadados que você baixou do portal do Azure e clique em **criar**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. Na página **configurações de logon único do SAML** , os campos são preenchidos automaticamente e clique em salvar.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. Na página **configurações de logon único** , clique no botão **baixar metadados** para baixar o arquivo de metadados do provedor de serviços. Use esse arquivo na seção de **configuração básica do SAML** no portal do Azure para configurar as URLs necessárias, conforme explicado acima.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/configure4.png)

10. Se você quiser configurar o aplicativo no modo iniciado pelo **SP** , estes são os pré-requisitos para isso:

    a. Você deve ter um domínio verificado.

    b. Você precisa configurar e habilitar seu domínio na área restrita Salesforce, as etapas para isso são explicadas posteriormente neste tutorial.

    c. Na portal do Azure, na seção **configuração básica do SAML** , clique em **definir URLs adicionais** e execute a seguinte etapa:
  
    ![Informações de logon único de domínio e URLs do Salesforce sandbox](common/both-signonurl.png)

    Na caixa de texto **URL de logon** , digite o valor usando o seguinte padrão: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Esse valor deve ser copiado do portal de área restrita do Salesforce depois que você habilitar o domínio.

11. Na seção **certificado de autenticação SAML** , clique em **XML de metadados de Federação** e salve o arquivo XML em seu computador.

    ![O link de download de certificado](common/metadataxml.png)

12. Abra uma nova guia no navegador e entre na sua conta de administrador da área restrita Salesforce.

13. Clique na **configuração** no **ícone configurações** no canto superior direito da página.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/configure1.png)

14. Role para baixo até as **configurações** no painel de navegação esquerdo, clique em **identidade** para expandir a seção correspondente. Em seguida, clique em **configurações de logon único**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. Na página **configurações de logon único** , clique no botão **Editar** .

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/configure3.png)

16. Selecione **SAML habilitado**e, em seguida, clique em **salvar**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Para configurar suas configurações de logon único do SAML, clique em **novo do arquivo de metadados**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Clique em **escolher arquivo** para carregar o arquivo XML de metadados e clique em **criar**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. Na página **configurações de logon único do SAML** , os campos são preenchidos automaticamente, digite o nome da configuração (por exemplo: *SPSSOWAAD_Test*), na caixa de texto **nome** e clique em salvar.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Para habilitar seu domínio na área restrita Salesforce, execute as seguintes etapas:

    > [!NOTE]
    > Antes de habilitar o domínio, você precisa criar o mesmo na área restrita Salesforce. Para obter mais informações, consulte [definindo seu nome de domínio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). Depois que o domínio for criado, verifique se ele está configurado corretamente.

21. No painel de navegação à esquerda na área restrita Salesforce, clique em **configurações da empresa** para expandir a seção relacionada e clique em **meu domínio**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. Na seção **configuração de autenticação** , clique em **Editar**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. Na seção **configuração de autenticação** , como **serviço de autenticação**, selecione o nome da configuração de logon único do SAML que você definiu durante a configuração de SSO na área restrita Salesforce e clique em **salvar**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-salesforce-sandbox-test-user"></a>Criar usuário de teste do Salesforce sandbox

Nesta seção, um usuário chamado Brenda Simon é criado na área restrita Salesforce. A área restrita Salesforce dá suporte ao provisionamento just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir na área restrita Salesforce, um novo será criado quando você tentar acessar a área restrita Salesforce. A área restrita Salesforce também dá suporte ao provisionamento automático de usuário. você pode encontrar mais detalhes [aqui](salesforce-sandbox-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuário.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Salesforce sandbox no painel de acesso, você deverá ser conectado automaticamente à área restrita Salesforce para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a área restrita Salesforce com o Azure AD](https://aad.portal.azure.com/)

- [O que é o controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/protect-salesforce)

- [Configurar provisionamento de usuário](salesforce-sandbox-provisioning-tutorial.md)

- [Como proteger a área restrita Salesforce com controles e visibilidade avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
