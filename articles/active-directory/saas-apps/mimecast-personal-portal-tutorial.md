---
title: 'Tutorial: integração do Azure Active Directory com o portal pessoal do Mimecast | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o portal pessoal do Mimecast.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 259635613855e4d7687cf569c94bbd3dd04027fe
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160623"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Tutorial: integração do Azure Active Directory com o portal pessoal do Mimecast

Neste tutorial, você aprenderá a integrar o portal pessoal do Mimecast ao Azure Active Directory (Azure AD).
A integração do portal pessoal do Mimecast ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao portal pessoal do Mimecast.
* Você pode permitir que seus usuários sejam conectados automaticamente ao portal pessoal do Mimecast (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao portal pessoal do Mimecast, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do portal pessoal do Mimecast

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O portal pessoal do Mimecast dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Adicionando o portal pessoal do Mimecast da Galeria

Para configurar a integração do portal pessoal do Mimecast ao Azure AD, você precisará adicionar o Mimecast Personal portal à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Mimecast Personal portal da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **portal pessoal do Mimecast**, selecione **portal pessoal do Mimecast** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Portal pessoal do Mimecast na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o portal pessoal do Mimecast, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do portal pessoal do Mimecast.

Para configurar e testar o logon único do Azure AD com o portal pessoal do Mimecast, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do portal pessoal do Mimecast](#configure-mimecast-personal-portal-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do portal pessoal do Mimecast](#create-mimecast-personal-portal-test-user)** – para ter um equivalente de Brenda Simon no portal pessoal do Mimecast que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o portal pessoal do Mimecast, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **portal pessoal do Mimecast** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do portal pessoal do Mimecast](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de logon** , digite uma URL: 

    | Região  |  Valor | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Estados Unidos   | `https://us-api.mimecast.com/login/saml`|
    | África do Sul    | `https://za-api.mimecast.com/login/saml`|
    | Austrália       | `https://au-api.mimecast.com/login/saml`|
    | Fora        | `https://jer-api.mimecast.com/login/saml`|

    b. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão:

    | Região  |  Valor | 
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Estados Unidos   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | África do Sul    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Austrália       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Fora        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. Na caixa de texto **URL de resposta** , digite uma URL: 

    | Região  |  Valor | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Estados Unidos   | `https://us-api.mimecast.com/login/saml`|
    | África do Sul    | `https://za-api.mimecast.com/login/saml`|
    | Austrália       | `https://au-api.mimecast.com/login/saml`|
    | Fora        | `https://jer-api.mimecast.com/login/saml`|

    > [!NOTE]
    > O valor do identificador não é real. Atualize o valor com o identificador real. Contate a [equipe de suporte ao cliente do portal pessoal do Mimecast](https://www.mimecast.com/customer-success/technical-support/) para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download do certificado](common/certificatebase64.png)

6. Na seção **Configurar o portal pessoal do Mimecast** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-mimecast-personal-portal-single-sign-on"></a>Configurar o logon único do portal pessoal do Mimecast

1. Em uma janela diferente do navegador da Web, faça logon em seu portal pessoal do Mimecast como administrador.

2. Vá para **serviços \> aplicativos**.
   
    ![Aplicações](./media/mimecast-personal-portal-tutorial/ic794998.png "Aplicações")

3. Clique em **perfis de autenticação**.
   
    ![Perfis de autenticação](./media/mimecast-personal-portal-tutorial/ic794999.png "Perfis de autenticação")

4. Clique em **novo perfil de autenticação**.
   
    ![Novo perfil de autenticação](./media/mimecast-personal-portal-tutorial/ic795000.png "Novo perfil de autenticação")

5. Na seção **perfil de autenticação** , execute as seguintes etapas:
   
    ![Perfil de autenticação](./media/mimecast-personal-portal-tutorial/ic795001.png "Perfil de autenticação")
   
    a. Na caixa de texto **Descrição** , digite um nome para a sua configuração.
   
    b. Selecione **impor autenticação SAML para o portal pessoal do Mimecast**.
   
    c. Como **provedor**, selecione **Azure Active Directory**.
   
    d. Na caixa de texto **URL do emissor** , Cole o valor do **identificador do Azure ad**que você copiou do portal do Azure.
   
    e. Na caixa de texto **URL de logon** , Cole o valor da **URL de logon**copiado de portal do Azure.
   
    f. Na caixa de texto **URL de logout** , Cole o valor da **URL de logout**copiado de portal do Azure.

    g. Abra seu certificado codificado em **base-64** no bloco de notas baixado de portal do Azure, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **certificado do provedor de identidade (metadados)** .

    h. Selecione **Permitir logon único**.
   
    i. Clique em **Guardar**.

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao portal pessoal do Mimecast.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **portal pessoal do Mimecast**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **portal pessoal do Mimecast**.

    ![O link do portal pessoal do Mimecast na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-mimecast-personal-portal-test-user"></a>Criar usuário de teste do portal pessoal do Mimecast

Para permitir que os usuários do AD do Azure façam logon no portal pessoal do Mimecast, eles devem ser provisionados no portal pessoal do Mimecast. No caso do portal pessoal do Mimecast, o provisionamento é uma tarefa manual.

Você precisa registrar um domínio antes de poder criar usuários.

**Para configurar o provisionamento de usuário, execute as seguintes etapas:**

1. Entre no **portal pessoal do Mimecast** como administrador.

2. Vá para **diretórios \> interno**.
   
    ![Diretórios](./media/mimecast-personal-portal-tutorial/ic795003.png "Diretórios")

3. Clique em **registrar novo domínio**.
   
    ![Registrar novo domínio](./media/mimecast-personal-portal-tutorial/ic795004.png "Registrar novo domínio")

4. Depois que o novo domínio tiver sido criado, clique em **novo endereço**.
   
    ![Novo endereço](./media/mimecast-personal-portal-tutorial/ic795005.png "Novo endereço")

5. Na caixa de diálogo novo endereço, execute as seguintes etapas de uma conta válida do Azure AD que você deseja provisionar:
   
    ![Guarde](./media/mimecast-personal-portal-tutorial/ic795006.png "Guardar")
   
    a. Na caixa de texto **endereço de email** , digite o **endereço de email** do usuário como **brendafernandes\@contoso.com**.
    
    b. Na caixa de texto **nome global** , digite o **nome de usuário** como **brendafernandes**.

    c. Nas caixas de texto **senha**e **Confirmar senha** , digite a **senha** do usuário.
   
    b. Clique em **Guardar**.

>[!NOTE]
>Você pode usar qualquer outra ferramenta de criação de conta de usuário do portal pessoal do Mimecast ou APIs fornecidas pelo portal pessoal do Mimecast para provisionar contas de usuário do Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do portal pessoal do Mimecast no painel de acesso, você deverá ser conectado automaticamente ao portal pessoal do Mimecast para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

