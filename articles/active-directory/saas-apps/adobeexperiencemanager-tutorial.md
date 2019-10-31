---
title: 'Tutorial: integração do Azure Active Directory com o Adobe Experience Manager | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Adobe Experience Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f39751f40b32c5da24e13d75d2607d7da0a57ad3
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73154108"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Tutorial: integração do Azure Active Directory com o Adobe Experience Manager

Neste tutorial, você aprenderá a integrar o Adobe Experience Manager ao Azure Active Directory (Azure AD).
A integração do Adobe Experience Manager ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Adobe Experience Manager.
* Você pode permitir que seus usuários sejam automaticamente conectados ao Adobe Experience Manager (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Adobe Experience Manager, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Adobe Experience Manager

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Adobe Experience Manager é compatível com o SSO iniciado por **SP e IDP**

* O Adobe Experience Manager dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Adicionando o Adobe Experience Manager da Galeria

Para configurar a integração do Adobe Experience Manager ao Azure AD, você precisará adicionar o Adobe Experience Manager da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Adobe Experience Manager da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Adobe Experience Manager**, selecione **Adobe Experience Manager** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Adobe Experience Manager na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o [nome do aplicativo], com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do [nome do aplicativo].

Para configurar e testar o logon único do Azure AD com o [nome do aplicativo], você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Adobe Experience Manager](#configure-adobe-experience-manager-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do Adobe Experience Manager](#create-adobe-experience-manager-test-user)** – para ter um equivalente de Brenda Simon no Adobe Experience Manager que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o [nome do aplicativo], execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Adobe Experience Manager** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Adobe Experience Manager](common/idp-intiated.png)

    a. Na caixa de texto **identificador** , digite um valor exclusivo que você define em seu servidor AEM também.

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > O valor da URL de resposta não é real. Atualize o valor da URL de resposta com a URL de resposta real. Para obter esse valor, entre em contato com a [equipe de suporte ao cliente do Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) para obter esse valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    ![Informações de logon único de domínio e URLs do Adobe Experience Manager](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon** , digite a URL do servidor do Adobe Experience Manager.

6. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download do certificado](common/certificatebase64.png)

7. Na seção **Configurar o Adobe Experience Manager** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-adobe-experience-manager-single-sign-on"></a>Configurar o logon único do Adobe Experience Manager

1. Em outra janela do navegador, abra o portal de administração do **Adobe Experience Manager** .

2. Selecione **configurações** > **segurança** > **usuários**.

    ![Configurar o botão salvar do logon único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

3. Selecione **administrador** ou qualquer outro usuário relevante.

    ![Configurar botão salvar de logon único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

4. Selecione **configurações de conta** > **gerenciar TrustStore**.

    ![Configurar botão salvar de logon único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

5. Em **Adicionar certificado do arquivo cer**, clique em **Selecionar arquivo de certificado**. Navegue até e selecione o arquivo de certificado, que você já baixou do portal do Azure.

    ![Configurar botão salvar de logon único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

6. O certificado é adicionado ao TrustStore. Observe o alias do certificado.

    ![Configurar botão salvar de logon único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

7. Na página **usuários** , selecione **Autenticação-serviço**.

    ![Configurar botão salvar de logon único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

8. Selecione **configurações de conta** > **criar/gerenciar repositório de chaves**. Crie um repositório de chaves fornecendo uma senha.

    ![Configurar botão salvar de logon único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

9. Volte para a tela de administração. Em seguida, selecione **configurações** > **operações** > **console Web**.

    ![Configurar botão salvar de logon único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Isso abre a página de configuração.

    ![Configurar o botão salvar do logon único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

10. Localize o **manipulador de autenticação do Adobe granito SAML 2,0**. Em seguida, selecione o ícone **Adicionar** .

    ![Configurar botão salvar de logon único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

11. Execute as seguintes ações nesta página.

    ![Configurar botão salvar de logon único](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. Na caixa **caminho** , digite **/** .

    b. Na caixa **URL do IDP** , insira o valor da **URL de logon** que você copiou do portal do Azure.

    c. Na caixa **alias do certificado IDP** , insira o valor do **alias do certificado** que você adicionou no TrustStore.

    d. Na caixa **ID da entidade fornecida pela segurança** , insira o valor exclusivo do **identificador do Azure ad** que você configurou no portal do Azure.

    e. Na caixa **URL de serviço do consumidor de asserção** , insira o valor da **URL de resposta** que você configurou no portal do Azure.

    f. Na caixa **senha do repositório de chaves** , digite a **senha** que você definiu no keystore.

    g. Na caixa **ID de atributo de usuário** , insira a **ID de nome** ou outra ID de usuário relevante no seu caso.

    h. Selecione **AutoCriar CRX usuários**.

    i. Na caixa **URL de logout** , insira o valor da **URL de logout** exclusivo obtido na portal do Azure.

    j. Selecione **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no portal do Azure chamado Brenda Simon.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.

    ![Os links "usuários e grupos" e "todos os usuários"](common/users.png)

2. Selecione **novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo usuário](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite **brendafernandes\@yourcompanydomain. Extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao Adobe Experience Manager.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Adobe Experience Manager**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Adobe Experience Manager**.

    ![O link do Adobe Experience Manager na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-adobe-experience-manager-test-user"></a>Criar usuário de teste do Adobe Experience Manager

Nesta seção, você criará um usuário chamado Brenda Simon no Adobe Experience Manager. Se você tiver selecionado a opção criação automática de **usuários do CRX** , os usuários serão criados automaticamente após a autenticação bem-sucedida.

Se você quiser criar usuários manualmente, trabalhe com a [equipe de suporte do Adobe Experience manager](https://helpx.adobe.com/support/experience-manager.html) para adicionar os usuários na plataforma do Adobe Experience Manager.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do Adobe Experience Manager no painel de acesso, você deverá entrar automaticamente no Adobe Experience Manager para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
