---
title: 'Tutorial: integração do Azure Active Directory com o asana | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o asana.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5531a7c1a95e472239c639e3307623fc4ccedd37
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73157863"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Tutorial: integração do Azure Active Directory com o asana

Neste tutorial, você aprenderá a integrar o asana com o Azure Active Directory (Azure AD).
A integração do asana ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao asana.
* Você pode permitir que seus usuários sejam conectados automaticamente ao asana (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao asana, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do asana

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O asana dá suporte ao SSO iniciado por **SP**

* O asana dá suporte ao [provisionamento **automatizado** de usuários](asana-provisioning-tutorial.md)

## <a name="adding-asana-from-the-gallery"></a>Adicionando o asana da Galeria

Para configurar a integração do asana ao Azure AD, você precisará adicionar o asana da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o asana da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **asana**, selecione **asana** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Asana na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o asana, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no asana.

Para configurar e testar o logon único do Azure AD com o asana, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do asana](#configure-asana-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do asana](#create-asana-test-user)** – para ter um equivalente de Brenda Simon no asana que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o asana, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **asana** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do asana](common/sp-identifier.png)

    a. Na caixa de texto **URL de logon** , digite URL: `https://app.asana.com/`

    b. Na caixa de texto **identificador (ID da entidade)** , digite URL: `https://app.asana.com/`

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download do certificado](common/certificatebase64.png)

6. Na seção **Configurar asana** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-asana-single-sign-on"></a>Configurar o logon único do asana

1. Em uma janela de navegador diferente, faça logon em seu aplicativo asana. Para configurar o SSO no asana, acesse as configurações do espaço de trabalho clicando no nome do espaço de trabalho no canto superior direito da tela. Em seguida, clique em **\<nome do espaço de trabalho\> configurações**.

    ![Configurações de SSO do asana](./media/asana-tutorial/tutorial_asana_09.png)

2. Na janela **configurações da organização** , clique em **Administração**. Em seguida, clique em **os membros devem fazer logon por meio do SAML** para habilitar a configuração de SSO. Execute as seguintes etapas:

    ![Configurar as definições da organização de logon único](./media/asana-tutorial/tutorial_asana_10.png)  

    a. Na caixa de texto **URL da página de entrada** , Cole a **URL de logon**.

    b. Clique com o botão direito do mouse no certificado baixado de portal do Azure e abra o arquivo de certificado usando o bloco de notas ou seu editor de texto preferido. Copie o conteúdo entre o início e o título do certificado final e cole-o na caixa de texto **certificado X. 509** .

3. Clique em **Guardar**. Acesse o [Guia do asana para configurar o SSO](https://asana.com/guide/help/premium/authentication#gl-saml) se precisar de mais assistência.

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao asana.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **asana**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **asana**.

    ![O link do asana na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-asana-test-user"></a>Criar usuário de teste do asana

O objetivo desta seção é criar um usuário chamado Brenda Simon no asana. O asana dá suporte ao provisionamento automático de usuário, que está habilitado por padrão. Você pode encontrar mais detalhes [aqui](asana-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuário.

**Se você precisar criar o usuário manualmente, execute as seguintes etapas:**

Nesta seção, você criará um usuário chamado Brenda Simon no asana.

1. No **asana**, vá para a seção **equipes** no painel esquerdo. Clique no botão sinal de adição.

    ![Criando um usuário de teste do Azure AD](./media/asana-tutorial/tutorial_asana_12.png)

2. Digite o email do usuário como **Brenda. simon\@contoso.com** na caixa de texto e, em seguida, selecione **convidar**.

3. Clique em **Enviar convite**. O novo usuário receberá um email em sua conta de email. o usuário precisará criar e validar a conta.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do asana no painel de acesso, você deverá ser conectado automaticamente ao asana para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar provisionamento de usuário](asana-provisioning-tutorial.md)
