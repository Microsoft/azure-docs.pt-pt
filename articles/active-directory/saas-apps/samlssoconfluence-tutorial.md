---
title: 'Tutorial: integração do Azure Active Directory com o SSO do SAML para Confluence da Resolution GmbH | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SSO do SAML para Confluence da Resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 6b47d483-d3a3-442d-b123-171e3f0f7486
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9676a71940569b26d6b0b6bfef767108ae57d953
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161213"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Tutorial: integração do Azure Active Directory com o SSO do SAML para Confluence da Resolution GmbH

Neste tutorial, você aprenderá a integrar o SSO do SAML para Confluence da Resolution GmbH com o Azure Active Directory (Azure AD).
A integração do SSO do SAML para Confluence da Resolution GmbH com o Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao SSO do SAML para Confluence da Resolution GmbH.
* Você pode permitir que seus usuários façam logon automaticamente no SSO do SAML para Confluence da Resolution GmbH (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o SSO do SAML para Confluence da Resolution GmbH, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do SSO do SAML para Confluence da Resolution GmbH

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* SSO do SAML para Confluence da Resolution GmbH dá suporte a **SP** **iniciado por** um SSO

## <a name="adding-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>Adicionando o SSO do SAML para Confluence da Resolution GmbH da Galeria

Para configurar a integração do SSO do SAML para Confluence da Resolution GmbH ao Azure AD, você precisa adicionar o SSO do SAML para Confluence da Resolution GmbH da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SSO do SAML para Confluence da Resolution GmbH da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SSO do SAML para Confluence da Resolution GmbH**, selecione **SSO do SAML para Confluence da Resolution GmbH** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![SSO do SAML para Confluence da Resolution GmbH na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o SSO do SAML para Confluence da Resolution GmbH com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SSO do SAML para Confluence da Resolution GmbH.

Para configurar e testar o logon único do Azure AD com o SSO do SAML para Confluence da Resolution GmbH, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar logon único do SSO do SAML para Confluence da Resolution GmbH](#configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do SSO do SAML para Confluence da Resolution GmbH](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** – para ter um equivalente de Brenda Simon no SSO do SAML para Confluence da Resolution GmbH que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o SSO do SAML para Confluence da Resolution GmbH, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SSO do SAML para Confluence da Resolution GmbH** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas, se você quiser configurar o aplicativo no modo iniciado pelo **IDP** :

    ![Informações de logon único de domínio e URLs do SSO do SAML para Confluence da Resolution GmbH](common/idp-intiated.png)

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo SP:

    ![Informações de logon único de domínio e URLs do SSO do SAML para Confluence da Resolution GmbH](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Contate a [equipe de suporte ao cliente do SSO do SAML para Confluence da Resolution GmbH](https://www.resolution.de/go/support) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download do certificado](common/metadataxml.png)

### <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on"></a>Configurar logon único do SSO do SAML para Confluence da Resolution GmbH

1. Em uma janela diferente do navegador da Web, faça logon no **portal de administração do SSO do SAML para Confluence da Resolution GmbH** como administrador.

2. Focalize engrenagem e clique nos **Complementos**.
    
    ![Configurar logon único](./media/samlssoconfluence-tutorial/addon1.png)

3. Você será redirecionado para a página de acesso de administrador. Insira a senha e clique no botão **confirmar** .

    ![Configurar logon único](./media/samlssoconfluence-tutorial/addon2.png)

4. Na guia **ATLASSIAN MARKETPLACE** , clique em **Localizar novos**Complementos. 

    ![Configurar logon único](./media/samlssoconfluence-tutorial/addon.png)

5. Pesquise **logon único do SAML (SSO) para Confluence** e clique no botão **instalar** para instalar o novo plug-in SAML.

    ![Configurar logon único](./media/samlssoconfluence-tutorial/addon7.png)

6. A instalação do plug-in será iniciada. Clique em **Fechar**.

    ![Configurar logon único](./media/samlssoconfluence-tutorial/addon8.png)

    ![Configurar logon único](./media/samlssoconfluence-tutorial/addon9.png)

7.  Clique em **Gerir**.

    ![Configurar logon único](./media/samlssoconfluence-tutorial/addon10.png)
    
8. Clique em **Configurar** para configurar o novo plug-in.

    ![Configurar logon único](./media/samlssoconfluence-tutorial/addon11.png)

9. Esse novo plug-in também pode ser encontrado na guia **usuários & segurança** .

    ![Configurar logon único](./media/samlssoconfluence-tutorial/addon3.png)
    
10. Na página **configuração do plugin logon único do SAML** , clique no botão **Adicionar novo IDP** para definir as configurações do provedor de identidade.

    ![Configurar logon único](./media/samlssoconfluence-tutorial/addon4.png)

11. Na página **escolha seu provedor de identidade SAML** , execute as seguintes etapas:

    ![Configurar logon único](./media/samlssoconfluence-tutorial/addon5a.png)
 
    a. Defina o **Azure ad** como o tipo IDP.
    
    b. Adicione o **nome** do provedor de identidade (por exemplo, Azure AD).
    
    c. Adicione a **Descrição** do provedor de identidade (por exemplo, Azure AD).
    
    d. Clique em **Seguinte**.
    
12. Na página **configuração do provedor de identidade** , clique no botão **Avançar** .

    ![Configurar logon único](./media/samlssoconfluence-tutorial/addon5b.png)

13. Na página **importar metadados do IDP do SAML** , execute as seguintes etapas:

    ![Configurar logon único](./media/samlssoconfluence-tutorial/addon5c.png)

    a. Clique no botão **carregar arquivo** e escolha o arquivo XML de metadados que você baixou na etapa 5.

    b. Clique no botão **importar** .
    
    c. Aguarde brevemente até que a importação seja realizada com sucesso.
    
    d. Clique no botão **Avançar** .
    
14. Na página **atributo e transformação de ID de usuário** , clique no botão **Avançar** .

    ![Configurar logon único](./media/samlssoconfluence-tutorial/addon5d.png)
    
15. Na página **criação e atualização de usuário** , clique em **Salvar & ao lado** de salvar configurações.   
    
    ![Configurar logon único](./media/samlssoconfluence-tutorial/addon6a.png)
    
16. Na página **testar suas configurações** , clique em **ignorar teste & configurar manualmente** para ignorar o teste do usuário por enquanto. Isso será executado na próxima seção e exigirá algumas configurações no portal do Azure. 
    
    ![Configurar logon único](./media/samlssoconfluence-tutorial/addon6b.png)
    
17. Na caixa de diálogo exibida, a leitura **ignorando o meio de teste significa...** , clique em **OK**.
    
    ![Configurar logon único](./media/samlssoconfluence-tutorial/addon6c.png)

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao SSO do SAML para Confluence da Resolution GmbH.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **SSO do SAML para Confluence da Resolution GmbH**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **SSO do SAML para Confluence da Resolution GmbH**.

    ![Link do SSO do SAML para Confluence da Resolution GmbH na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>Criar usuário de teste do SSO do SAML para Confluence da Resolution GmbH

Para permitir que os usuários do Azure AD façam logon no SSO do SAML para Confluence da Resolution GmbH, eles devem ser provisionados no SSO do SAML para Confluence da Resolution GmbH.  
No SSO do SAML para Confluence da Resolution GmbH, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do SSO do SAML para Confluence da Resolution GmbH como administrador.

2. Passe o mouse em engrenagem e clique no **Gerenciamento de usuários**.

    ![Adicionar funcionário](./media/samlssoconfluence-tutorial/user1.png) 

3. Na seção usuários, clique na guia **Adicionar usuários** . Na página da caixa de diálogo **"adicionar um usuário"** , execute as seguintes etapas:

    ![Adicionar funcionário](./media/samlssoconfluence-tutorial/user2.png) 

    a. Na caixa de texto **username** , digite o email do usuário, como Brenda Simon.

    b. Na caixa de texto **nome completo** , digite o nome completo do usuário, como Brenda Simon.

    c. Na caixa de texto **email** , digite o endereço de email do usuário, como Brittasimon@contoso.com.

    d. Na caixa de texto **senha** , digite a senha para Brenda Simon.

    e. Clique em **Confirmar senha** reinsira a senha.
    
    f. Clique no botão **Adicionar** .

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco SSO do SAML para Confluence da Resolution GmbH no painel de acesso, você deverá ser conectado automaticamente ao SSO do SAML para Confluence da Resolution GmbH para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

