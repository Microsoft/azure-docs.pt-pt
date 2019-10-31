---
title: 'Tutorial: integração do Azure Active Directory com o Mitel Connect | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Mitel Connect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: 30a4dcbd15d8a47d99b3a61879a7e61938d3b133
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160547"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>Tutorial: integração do Azure Active Directory com o Mitel MiCloud Connect

Neste tutorial, você aprenderá a integrar o Mitel MiCloud a conectar-se ao Azure Active Directory (Azure AD). A integração do MiCloud Connect ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao MiCloud Connect apps usando suas credenciais corporativas.
* Você pode permitir que os usuários em sua conta sejam automaticamente conectados ao MiCloud Connect (logon único) com suas contas do Azure AD.


Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao MiCloud Connect, você precisa dos seguintes itens:

* Uma subscrição do Azure

  Se você não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Uma conta do Mitel MiCloud Connect

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO (logon único) do Azure AD.

* O Mitel Connect dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-mitel-connect-from-the-gallery"></a>Adicionando o Mitel Connect da Galeria

Para configurar a integração do Mitel Connect ao Azure AD, você precisará adicionar o Mitel Connect da Galeria à sua lista de aplicativos SaaS gerenciados no portal do Azure.

**Para adicionar o Mitel Connect por meio da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação do lado esquerdo, clique em **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Clique em **aplicativos empresariais** e em **todos os aplicativos**.

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Clique em **novo aplicativo**.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Digite **Mitel Connect** no campo de pesquisa, clique em **Mitel conectar** do painel de resultados e, em seguida, clique em **Adicionar**.

     ![Mitel conectar-se na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o MiCloud Connect, com base em um usuário de teste chamado **Brenda Simon**. Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do MiCloud Connect.

Para configurar e testar o logon único do Azure AD com o MiCloud Connect, você precisa concluir as seguintes etapas:

1. **[Configurar o MiCloud Connect para SSO com o Azure ad](#configure-micloud-connect-for-sso-with-azure-ad)** – para permitir que os usuários usem esse recurso e definam as configurações de SSO no lado do aplicativo.
2. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
3. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
4. **[Criar um usuário de teste do Mitel MiCloud Connect](#create-a-mitel-micloud-connect-test-user)** – para ter um equivalente de Brenda Simon em sua conta do MiCloud Connect que esteja vinculada à representação do usuário no Azure AD.
5. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Configurar o MiCloud Connect para SSO com o Azure AD

Nesta seção, você habilitará o logon único do Azure AD para o MiCloud Connect no portal do Azure e configurará sua conta do MiCloud Connect para permitir o SSO usando o Azure AD.

Para configurar o MiCloud Connect com o SSO para o Azure AD, é mais fácil abrir o portal do Azure e o portal da conta do Mitel lado a lado. Você precisará copiar algumas informações do portal do Azure para o portal de conta do Mitel e alguns do portal de conta do Mitel para a portal do Azure.


1. Para abrir a página de configuração no [portal do Azure](https://portal.azure.com/), faça o seguinte:

    a. Na página de integração de aplicativos do **Mitel Connect** , clique em **logon único**.

    ![Link configurar logon único](common/select-sso.png)

    b. Na caixa de diálogo **selecionar um método de logon único** , clique em **SAML**.

    ![Modo de seleção de logon único](common/select-saml-option.png)
    
    A página de logon baseada em SAML é exibida.

2. Para abrir a caixa de diálogo de configuração no portal de conta do Mitel, faça o seguinte:

    a. No menu do **sistema telefônico** , clique em **recursos complementares**.

    b. À direita de **logon único**, clique em **Ativar** ou **configurações**.
    
    A caixa de diálogo Conectar configurações de logon único é exibida.
    
3. Marque a caixa de seleção **habilitar logon único** .
    ](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png) de imagem ![


4. No portal do Azure, clique no ícone **Editar** na seção **configuração básica do SAML** .
    ](common/edit-urls.png) de imagem ![

    A caixa de diálogo configuração básica do SAML é exibida.

5.  Copie a URL do campo **identificador Mitel (ID da entidade)** no portal da conta do Mitel e cole-a no campo **identificador (ID da entidade)** no portal do Azure.

6. Copie a URL do campo **URL de resposta (URL de serviço do consumidor de asserção)** no portal da conta do Mitel e cole-a no campo **URL de resposta (URL de serviço do consumidor de asserção)** no portal do Azure.  
   ](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png) de imagem ![

7. Na caixa de texto **URL de logon** , digite uma das seguintes URLs:

    * **https://portal.shoretelsky.com** -usar o portal de conta do Mitel como seu aplicativo Mitel padrão
    * **https://teamwork.shoretel.com** -usar o trabalho em equipe como seu aplicativo Mitel padrão

    **Observação**: o aplicativo Mitel padrão é o aplicativo acessado quando um usuário clica no bloco do Mitel Connect no painel de acesso. Esse também é o aplicativo acessado ao fazer uma configuração de teste do Azure AD.

8. Clique em **salvar** na caixa de diálogo **configuração básica do SAML** na portal do Azure.

9. Na seção **certificado de autenticação SAML** na página de **logon baseada em SAML** no portal do Azure, clique em **baixar** ao lado de **certificado (Base64)** para baixar o certificado de **autenticação** e salvá-lo em seu computador.
    ](./media/mitel-connect-tutorial/Azure_SigningCert.png) de imagem ![

10. Abra o arquivo do certificado de autenticação em um editor de texto, copie todos os dados no arquivo e, em seguida, Cole os dados no campo **certificado de autenticação** no portal da conta do Mitel. 
    ](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png) de imagem ![

11. Na seção **Setup Mitel Connect** na página de **logon baseada em SAML** do portal do Azure, faça o seguinte:

    a. Copie a URL do campo **URL de logon** e cole-a no campo **URL de entrada** no portal de conta do Mitel.

    b. Copie a URL do campo **identificador do Azure ad** e cole-a no campo **ID da entidade** no portal da conta do Mitel.
    ](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png) de imagem ![

12. Clique em **salvar** na caixa de diálogo **conectar configurações de logon único** no portal de conta do Mitel.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

Nesta seção, você criará um usuário de teste chamado Brenda Simon no portal do Azure.

1. No portal do Azure, no painel esquerdo, clique em **Azure Active Directory**, clique em **usuários**e, em seguida, clique em **todos os usuários**.

    ![Os links "usuários e grupos" e "todos os usuários"](common/users.png)

2. Clique em **novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Na caixa de diálogo Propriedades do usuário, execute as seguintes etapas:

    ![A caixa de diálogo usuário](common/user-properties.png)

    a. No campo **nome** , digite **brendafernandes**.
  
    b. No campo **nome de usuário** , digite brendafernandes @\<yourcompanydomain\>.\>de extensão de\<.  
Por exemplo, BrittaSimon@contoso.com.

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao Mitel Connect.

1. No portal do Azure, clique em **aplicativos empresariais**e em **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, clique em **Mitel conectar**.

    ![O link do Mitel Connect na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique em **Adicionar usuário**e em **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista **usuários** e, em seguida, clique em **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, selecione a função apropriada para o usuário na lista na caixa de diálogo **selecionar função** e, em seguida, clique em **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique em **atribuir**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Criar um usuário de teste do Mitel MiCloud Connect

Nesta seção, você criará um usuário chamado Brenda Simon em sua conta do MiCloud Connect. Os usuários devem ser criados e ativados antes de usar o logon único.

Para obter detalhes sobre como adicionar usuários no portal de conta do Mitel, consulte o artigo [adicionando um usuário](https://docs.shoretel.com/connectcloud/Account/Users/AddUser) na base de dados de conhecimento do Mitel.

Crie um usuário em sua conta do MiCloud Connect com os seguintes detalhes:

  * **Nome:** Brenda Simon

* **Endereço de email comercial:** `brittasimon@<yourcompanydomain>.<extension>`   
(Exemplo: [brittasimon@contoso.com](mailto:brittasimon@contoso.com))

* **Nome de usuário:** `brittasimon@<yourcompanydomain>.<extension>`  
(Exemplo: [brittasimon@contoso.com](mailto:brittasimon@contoso.com); o nome de usuário é normalmente o mesmo que o endereço de email comercial do usuário)

**Observação:** O nome de usuário do MiCloud Connect do usuário deve ser idêntico ao endereço de email do usuário no Azure.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco Mitel Connect no painel de acesso, você deverá ser redirecionado automaticamente para entrar no aplicativo MiCloud Connect que você configurou como padrão no campo URL de **logon** . Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
