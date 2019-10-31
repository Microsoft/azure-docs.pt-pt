---
title: 'Tutorial: integração do Azure Active Directory com o Cisco adguardable | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Cisco de forma abrangente.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 675dca98-f119-4463-8350-d6a45d5601e3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee91ca53e32bfdc387dc20054493d02d506a75da
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158635"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella"></a>Tutorial: integração de Azure Active Directory com o Cisco de proteção

Neste tutorial, você aprenderá a integrar o Cisco com o Azure Active Directory (Azure AD).
A integração do Cisco de proteção ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Cisco de proteção.
* Você pode permitir que seus usuários façam logon automaticamente no Cisco de proteção (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Cisco, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Cisco de alta proteção

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Cisco comportable dá suporte ao **SP e** ao SSO iniciado pelo IDP

## <a name="adding-cisco-umbrella-from-the-gallery"></a>Adicionando o Cisco de abrangência da Galeria

Para configurar a integração do Cisco de proteção ao Azure AD, você precisa adicionar o Cisco exgerition da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Cisco de proteção contra a Galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Cisco**exbase, selecione **Cisco** comparation no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![O Cisco é um meio na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o [nome do aplicativo], com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do [nome do aplicativo].

Para configurar e testar o logon único do Azure AD com o [nome do aplicativo], você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Cisco](#configure-cisco-umbrella-single-sign-on)** proverso-para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar um usuário de teste do Cisco](#create-cisco-umbrella-test-user)** conjuntamente – para ter um equivalente de Brenda Simon no Cisco de abrangência que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o [nome do aplicativo], execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Cisco de abrangência** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , o usuário não precisa executar nenhuma etapa, pois o aplicativo já está previamente integrado ao Azure.

    ![Informações de logon único de domínio e URLs do Cisco de abrangência](common/both-preintegrated-signon.png)

    a. Se você quiser configurar o aplicativo no modo **SP** iniciado, execute as seguintes etapas:

    b. Clique em **definir URLs adicionais**.

    c. Na caixa de texto **URL de logon** , digite uma URL: `https://login.umbrella.com/sso`

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **XML de metadados** das opções especificadas de acordo com seu requisito e salve-o em seu computador.

    ![O link de download do certificado](common/metadataxml.png)

6. Na seção **Configurar o Cisco** de forma abrangente, copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-cisco-umbrella-single-sign-on"></a>Configurar o logon único do Cisco de abrangência

1. Em uma janela de navegador diferente, faça logon em seu site de empresa do Cisco outformativo como administrador.

2. No lado esquerdo do menu, clique em **administrador** e navegue até **autenticação** e clique em **SAML**.

    ![O administrador](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_admin.png)

3. Escolha **outro** e clique em **Avançar**.

    ![O outro](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_other.png)

4. Na página **metadados de abrangência da Cisco**, clique em **Avançar**.

    ![Os metadados](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_metadata.png)

5. Na guia **carregar metadados** , se você tivesse o SAML pré-configurado, selecione **clique aqui para alterá-los** e siga as etapas abaixo.

    ![O próximo](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_next.png)

6. Na **opção A: carregar arquivo XML**, carregue o arquivo **XML de metadados de Federação** que você baixou do portal do Azure e depois de carregar os metadados, os valores abaixo são preenchidos automaticamente e, em seguida, clique em **Avançar**.

    ![O choosefile](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_choosefile.png)

7. Na seção **validar configuração do SAML** , clique em **testar sua configuração do SAML**.

    ![O teste](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_test.png)

8. Clique em **GUARDAR**.

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo-lhe acesso ao Cisco de proteção.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Cisco de proteção**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **Cisco Exguardation**.

    ![O link de proteção da Cisco na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-cisco-umbrella-test-user"></a>Criar um usuário de teste do Cisco prochuva

Para permitir que os usuários do Azure AD façam logon no Cisco, eles devem ser provisionados no Cisco de proteção.  
No caso do Cisco de forma abrangente, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Em uma janela de navegador diferente, faça logon em seu site de empresa do Cisco outformativo como administrador.

2. No lado esquerdo do menu, clique em **administrador** e navegue até **contas**.

    ![A conta](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_account.png)

3. Na página **contas** , clique em **Adicionar** no lado superior direito da página e execute as etapas a seguir.

    ![O usuário](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_createuser.png)

    a. No **primeiro campo nome** , insira o nome como **Brenda**.

    b. No campo **sobrenome** , insira o sobrenome, como **Simon**.

    c. Na **função escolher administrador delegado**, selecione sua função.
  
    d. No campo **endereço de email** , insira o EmailAddress do usuário, como **brendafernandes\@contoso.com**.

    e. No campo **senha** , insira sua senha.

    f. No campo **Confirmar senha** , insira novamente sua senha.

    g. Clique em **criar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco Cisco no painel de acesso, você deverá ser conectado automaticamente ao conjunto de segurança da Cisco para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
