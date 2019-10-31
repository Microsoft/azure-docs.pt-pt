---
title: 'Tutorial: integração do Azure Active Directory com o Mozy Enterprise | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Mozy Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 0f1aca75045f674fa45f5ad00c14a37c1abe7aea
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160425"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Tutorial: integração do Azure Active Directory com o Mozy Enterprise

Neste tutorial, você aprenderá a integrar o Mozy Enterprise ao Azure Active Directory (Azure AD).
A integração do Mozy Enterprise ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Mozy Enterprise.
* Você pode permitir que seus usuários sejam automaticamente conectados ao Mozy Enterprise (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Mozy Enterprise, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Mozy Enterprise

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Mozy Enterprise dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Adicionando o Mozy Enterprise por meio da Galeria

Para configurar a integração do Mozy Enterprise ao Azure AD, você precisa adicionar o Mozy Enterprise da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Mozy Enterprise por meio da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Mozy Enterprise**, selecione **Mozy Enterprise** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Mozy Enterprise na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Mozy Enterprise, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Mozy Enterprise.

Para configurar e testar o logon único do Azure AD com o Mozy Enterprise, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Mozy Enterprise](#configure-mozy-enterprise-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do Mozy Enterprise](#create-mozy-enterprise-test-user)** – para ter um equivalente de Brenda Simon no Mozy Enterprise que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Mozy Enterprise, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Mozy Enterprise** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Mozy Enterprise](common/sp-signonurl.png)

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de logon real. Contate a [equipe de suporte do Mozy cliente corporativo](https://support.mozy.com/) para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download do certificado](common/certificatebase64.png)

6. Na seção **Configurar o Mozy Enterprise** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-mozy-enterprise-single-sign-on"></a>Configurar o logon único do Mozy Enterprise

1. Em uma janela diferente do navegador da Web, faça logon em seu site de empresa do Mozy Enterprise como administrador.

2. Na seção **configuração** , clique em **política de autenticação**.
   
    ![Política de autenticação](./media/mozy-enterprise-tutorial/ic777314.png "Política de autenticação")

3. Na seção **política de autenticação** , execute as seguintes etapas:
   
    ![Política de autenticação](./media/mozy-enterprise-tutorial/ic777315.png "Política de autenticação")
   
    a. Selecione **serviço de diretório** como **provedor**.
   
    b. Selecione **usar envio por push LDAP**.
   
    c. Clique na guia **autenticação SAML** .
   
    d. Cole a **URL de logon**, que você copiou do portal do Azure para a caixa de texto URL de **autenticação** .
   
    e. Cole o **identificador do Azure ad**, que você copiou do portal do Azure para a caixa de texto **ponto de extremidade SAML** .
   
    f. Abra seu certificado codificado em base-64 baixado no bloco de notas, copie o conteúdo dele para a área de transferência e, em seguida, Cole o certificado inteiro na caixa de texto **certificado SAML** .
   
    g. Selecione **habilitar SSO para administradores para fazer logon com suas credenciais de rede**.
   
    h. Clique em **salvar alterações**.

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao Mozy Enterprise.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Mozy Enterprise**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Mozy Enterprise**.

    ![O link do Mozy Enterprise na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-mozy-enterprise-test-user"></a>Criar um usuário de teste do Mozy Enterprise

Para permitir que os usuários do AD do Azure façam logon no Mozy Enterprise, eles devem ser provisionados no Mozy Enterprise. No caso do Mozy Enterprise, o provisionamento é uma tarefa manual.

>[!NOTE]
>Você pode usar qualquer outra ferramenta de criação de conta de usuário Enterprise do Mozy ou APIs fornecidas pelo Mozy Enterprise para provisionar contas de usuário do AAD.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **Mozy Enterprise** .

2. Clique em **usuários**e, em seguida, clique em **Adicionar novo usuário**.
   
    ![Utilizadores](./media/mozy-enterprise-tutorial/ic777317.png "Utilizadores")
   
    >[!NOTE]
    >A opção **Adicionar novo usuário** só será exibida se o **Mozy** estiver selecionado como o provedor em **política de autenticação**. Se a autenticação SAML estiver configurada, os usuários serão adicionados automaticamente em seu primeiro logon por meio do logon único.
    
3. Na caixa de diálogo novo usuário, execute as seguintes etapas:
   
    ![Adicionar usuários](./media/mozy-enterprise-tutorial/ic777318.png "Adicionar usuários")
   
    a. Na lista **escolher um grupo** , selecione um grupo.
   
    b. Na lista o **tipo de usuário** , selecione um tipo.
   
    c. Na caixa de texto **username** , digite o nome do usuário do Azure AD.
   
    d. Na caixa de texto **email** , digite o endereço de email do usuário do Azure AD.
   
    e. Selecione **Enviar email de instrução do usuário**.
   
    f. Clique em **Adicionar usuário (s)** .

     >[!NOTE]
     > Depois de criar o usuário, um email será enviado para o usuário do Azure AD que inclui um link para confirmar a conta antes que ela se torne ativa.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco Mozy Enterprise no painel de acesso, você deve entrar automaticamente no Mozy Enterprise para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

