---
title: 'Tutorial: integração do Azure Active Directory com o Adobe Sign | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Adobe Sign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 788c1292b844088f171479b40fc566ff5cfc8a57
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73154035"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Tutorial: integração do Azure Active Directory com o Adobe Sign

Neste tutorial, você aprenderá a integrar o Adobe Sign com o Azure Active Directory (Azure AD).
A integração do Adobe Sign ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Adobe Sign.
* Você pode permitir que seus usuários sejam automaticamente conectados ao Adobe Sign (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Adobe Sign, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Adobe Sign

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Adobe Sign dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-adobe-sign-from-the-gallery"></a>Adicionando o Adobe Sign da Galeria

Para configurar a integração do Adobe Sign ao Azure AD, você precisa adicionar o Adobe Sign da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Adobe Sign da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Adobe Sign**, selecione **Adobe Sign** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Adobe entrar na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Adobe Sign com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Adobe Sign.

Para configurar e testar o logon único do Azure AD com o Adobe Sign, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Adobe Sign](#configure-adobe-sign-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do Adobe Sign](#create-adobe-sign-test-user)** – para ter um equivalente de Brenda Simon no Adobe Sign que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Adobe Sign, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Adobe Sign** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Adobe Sign](common/sp-identifier.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<companyname>.echosign.com/`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão: `https://<companyname>.echosign.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contate a [equipe de suporte ao cliente do Adobe Sign](https://helpx.adobe.com/in/contact/support.html) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download do certificado](common/certificatebase64.png)

6. Na seção **Configurar o sinal do Adobe** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-adobe-sign-single-sign-on"></a>Configurar o logon único do Adobe Sign

1. Antes da configuração, entre em contato com a [equipe de suporte ao cliente do Adobe Sign](https://helpx.adobe.com/in/contact/support.html) para adicionar seu domínio na lista de permissões do Adobe Sign. Veja como adicionar o domínio:

    a. A [equipe de suporte ao cliente do Adobe Sign](https://helpx.adobe.com/in/contact/support.html) envia um token gerado aleatoriamente. Para seu domínio, o token será semelhante ao seguinte: **Adobe-Sign-Verification = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publique o token de verificação em um registro de texto DNS e notifique a [equipe de suporte ao cliente do Adobe Sign](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Isso pode levar alguns dias ou mais. Observe que os atrasos de propagação de DNS significam que um valor publicado no DNS pode não estar visível por uma hora ou mais. O administrador de ti deve ter conhecimento sobre como publicar esse token em um registro de texto DNS.
    
    c. Ao notificar a [equipe de suporte ao cliente do Adobe Sign](https://helpx.adobe.com/in/contact/support.html) por meio do tíquete de suporte, depois que o token for publicado, ele validará o domínio e o adicionará à sua conta.
    
    d. Em geral, veja como publicar o token em um registro DNS:

    * Entre na sua conta de domínio
    * Localize a página para atualizar o registro DNS. Essa página pode ser chamada de gerenciamento de DNS, gerenciamento de servidor de nomes ou configurações avançadas.
    * Localize os registros TXT para seu domínio.
    * Adicione um registro TXT com o valor de token completo fornecido pela Adobe.
    * Guarde as alterações.

1. Em uma janela diferente do navegador da Web, entre no site da empresa do Adobe Sign como um administrador.

1. No menu SAML, selecione **configurações de conta** > **configurações de SAML**.
   
    ![Captura de tela da página de configurações do Adobe Sign SAML](./media/adobe-echosign-tutorial/ic789520.png "Conta")

1. Na seção **configurações do SAML** , execute as seguintes etapas:
  
   ![Captura de tela das configurações do SAML](./media/adobe-echosign-tutorial/ic789521.png "Configurações do SAML")
   
   ![Captura de tela das configurações do SAML](./media/adobe-echosign-tutorial/ic789522.png "Configurações do SAML")

   a. Em **modo SAML**, selecione **SAML obrigatório**.
   
   b. Selecione **permitir que os administradores de conta do EchoSign façam logon usando suas credenciais do EchoSign**.
   
   c. Em **criação de usuário**, selecione **adicionar automaticamente usuários autenticados por meio do SAML**.

   d. Cole o **identificador do Azure ad**copiado do portal do Azure na caixa de texto **ID da entidade IDP** .
    
   e. Cole a **URL de logon**, que você copiou de portal do Azure para a caixa de texto URL de logon do **IDP** .
   
   f. Cole a **URL de logout**, que você copiou do portal do Azure para a caixa de texto URL de logout do **IDP** .

   g. Abra o arquivo de **certificado (Base64)** baixado no bloco de notas. Copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **certificado IDP** .

   h. Selecione **salvar alterações**.

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao Adobe Sign.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Adobe Sign**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **Adobe Sign**.

    ![O link do Adobe Sign na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-adobe-sign-test-user"></a>Criar usuário de teste do Adobe Sign

Para permitir que os usuários do Azure AD entrem no Adobe Sign, eles devem ser provisionados no Adobe Sign. Esta é uma tarefa manual.

>[!NOTE]
>Você pode usar qualquer outra ferramenta de criação de conta de usuário do Adobe Sign ou APIs fornecidas pelo Adobe Sign para provisionar contas de usuário do Azure AD. 

1. Entre no site da empresa do **Adobe Sign** como um administrador.

2. No menu na parte superior, selecione **conta**. Em seguida, no painel esquerdo, selecione **usuários & grupos** > **criar um novo usuário**.
   
    ![Captura de tela do site da empresa do Adobe Sign, com conta, usuários & grupos e criar um novo usuário realçado](./media/adobe-echosign-tutorial/ic789524.png "Conta")
   
3. Na seção **criar novo usuário** , execute as seguintes etapas:
   
    ![Captura de tela da seção criar novo usuário](./media/adobe-echosign-tutorial/ic789525.png "Criar usuário")
   
    a. Digite o **endereço de email**, o **nome**e o **sobrenome** de uma conta válida do Azure AD que você deseja provisionar nas caixas de texto relacionadas.
   
    b. Selecione **criar usuário**.

>[!NOTE]
>O titular da conta Azure Active Directory recebe um email que inclui um link para confirmar a conta, antes que ela se torne ativa. 

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do Adobe Sign no painel de acesso, você deverá entrar automaticamente no sinal da Adobe para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

