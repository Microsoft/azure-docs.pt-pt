---
title: 'Tutorial: integração do Azure Active Directory com o Samanage | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Samanage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 741efbae84e18c811beb6c0579c1949c5ddf619c
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160114"
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Tutorial: integração do Azure Active Directory com o Samanage

Neste tutorial, você aprenderá a integrar o Samanage com o Azure Active Directory (Azure AD).
A integração do Samanage ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Samanage.
* Você pode permitir que seus usuários sejam conectados automaticamente ao Samanage (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Samanage, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Samanage

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Samanage dá suporte ao SSO iniciado por **SP**

## <a name="adding-samanage-from-the-gallery"></a>Adicionando o Samanage da Galeria

Para configurar a integração do Samanage ao Azure AD, você precisará adicionar o Samanage da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Samanage da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Samanage**, selecione **Samanage** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Samanage na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Samanage, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Samanage.

Para configurar e testar o logon único do Azure AD com o Samanage, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Samanage](#configure-samanage-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do Samanage](#create-samanage-test-user)** – para ter um equivalente de Brenda Simon no Samanage que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Samanage, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Samanage** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Samanage](common/sp-identifier.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais, que são explicados posteriormente no tutorial. Para obter mais detalhes, contate a [equipe de suporte ao cliente do Samanage](https://www.samanage.com/support). Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download do certificado](common/certificatebase64.png)

6. Na seção **Configurar Samanage** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-samanage-single-sign-on"></a>Configurar o logon único do Samanage

1. Em uma janela diferente do navegador da Web, faça logon em seu site de empresa do Samanage como administrador.

2. Clique em **painel** e selecione **instalação** no painel de navegação esquerdo.
   
    ![Dashboard](./media/samanage-tutorial/tutorial_samanage_001.png "Dashboard")

3. Clique em **logon único**.
   
    ![Logon único](./media/samanage-tutorial/tutorial_samanage_002.png "Logon único")

4. Navegue para **fazer logon usando o SAML** seção, execute as seguintes etapas:
   
    ![Logon usando SAML](./media/samanage-tutorial/tutorial_samanage_003.png "Logon usando SAML")
 
    a. Clique em **habilitar logon único com SAML**.  
 
    b. Na caixa de texto **URL do provedor de identidade** , Cole o valor do identificador do **Azure ad** que você copiou do portal do Azure.    
 
    c. Confirme se a **URL de logon** corresponde à URL de **logon** da seção de **configuração básica do SAML** no portal do Azure.
 
    d. Na caixa de texto **URL de logout** , insira o valor da **URL de logout** que você copiou de portal do Azure.
 
    e. Na caixa de texto **emissor SAML** , digite o URI da ID do aplicativo definido no provedor de identidade.
 
    f. Abra seu certificado codificado de base 64 baixado de portal do Azure no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **colar seu certificado de identidade x. 509 abaixo** .
 
    g. Clique em **criar usuários se eles não existirem no Samanage**.
 
    h. Clique em **Atualizar**.

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao Samanage.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Samanage**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Samanage**.

    ![O link do Samanage na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-samanage-test-user"></a>Criar usuário de teste do Samanage

Para permitir que os usuários do Azure AD façam logon no Samanage, eles devem ser provisionados no Samanage.  
No caso do Samanage, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do Samanage como administrador.

2. Clique em **painel** e selecione **instalação** na panorâmica de navegação à esquerda.
   
    ![Configurar](./media/samanage-tutorial/tutorial_samanage_001.png "Configurar")

3. Clique na guia **usuários**
   
    ![Utilizadores](./media/samanage-tutorial/tutorial_samanage_006.png "Utilizadores")

4. Clique em **novo usuário**.
   
    ![Novo usuário](./media/samanage-tutorial/tutorial_samanage_007.png "Novo usuário")

5. Digite o **nome** e o **endereço de email** de uma conta de Azure Active Directory que você deseja provisionar e clique em **criar usuário**.
   
    ![Criar usuário](./media/samanage-tutorial/tutorial_samanage_008.png "Criar usuário")
   
   >[!NOTE]
   >O titular da conta Azure Active Directory receberá um email e seguirá um link para confirmar sua conta antes que ela se torne ativa. Você pode usar qualquer outra ferramenta de criação de conta de usuário Samanage ou APIs fornecidas pelo Samanage para provisionar Azure Active Directory contas de usuário.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do Samanage no painel de acesso, você deverá ser conectado automaticamente ao Samanage para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

