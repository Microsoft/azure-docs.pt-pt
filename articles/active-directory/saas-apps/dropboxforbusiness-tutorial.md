---
title: 'Tutorial: integração do Azure Active Directory com o Dropbox for Business | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Dropbox for Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae1e537665739c055121fd82cf14f733a90142e4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964247"
---
# <a name="tutorial-integrate-dropbox-for-business-with-azure-active-directory"></a>Tutorial: integrar o Dropbox for Business com o Azure Active Directory

Neste tutorial, você aprenderá a integrar o Dropbox for Business com o Azure Active Directory (Azure AD). Ao integrar o Dropbox for Business ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao dropbox for Business.
* Habilite seus usuários a serem conectados automaticamente ao dropbox for Business com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura habilitada para logon único (SSO) do Dropbox for Business.

## <a name="scenario-description"></a>Descrição do cenário

* Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Dropbox for Business dá suporte ao SSO iniciado pelo **SP**

* O Dropbox for Business dá suporte ao [provisionamento e ao desprovisionamento automatizados de usuários](dropboxforbusiness-tutorial.md)

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Adicionando o Dropbox for Business por meio da Galeria

Para configurar a integração do Dropbox for Business ao Azure AD, você precisará adicionar o Dropbox for Business por meio da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Dropbox for Business** na caixa de pesquisa.
1. Selecione **Dropbox for Business** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste o SSO do Azure AD com o Dropbox for Business usando um usuário de teste chamado **Brenda Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Dropbox for Business.

Para configurar e testar o SSO do Azure AD com o Dropbox for Business, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do Dropbox for Business](#configure-dropbox-for-business-sso)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar usuário de teste do Dropbox for Business](#create-dropbox-for-business-test-user)** – para ter um equivalente de Brenda Simon no Dropbox for Business que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Dropbox for Business** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na página **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://www.dropbox.com/sso/<id>`

    b. Na caixa de texto **identificador (ID da entidade)** , digite um valor: `Dropbox`

    > [!NOTE]
    > O valor da URL de logon anterior não é um valor real. Você atualizará o valor com a URL de logon real, que é explicada posteriormente no tutorial.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar o Dropbox for Business** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-dropbox-for-business-sso"></a>Configurar o SSO do Dropbox for Business

1. Para automatizar a configuração no Dropbox for Business, você precisa instalar a **extensão do navegador de entrada segura de meus aplicativos** clicando em **instalar a extensão**.

    ![Extensão de meus aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar o Dropbox for Business** , você será direcionado para o aplicativo Dropbox for Business. A partir daí, forneça as credenciais de administrador para entrar no Dropbox for Business. A extensão do navegador irá configurar automaticamente o aplicativo para você e automatizar as etapas de 3-8.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar o Dropbox for Business manualmente, abra uma nova janela do navegador da Web e vá para seu locatário do Dropbox for Business e faça logon em seu locatário do Dropbox for Business. e execute as seguintes etapas:

    ![Configure single sign-on](./media/dropboxforbusiness-tutorial/ic769509.png "Configurar o início de sessão único") (Configurar o início de sessão único)

4. Clique no **ícone de usuário** e selecione a guia **configurações** .

    ![Configure single sign-on](./media/dropboxforbusiness-tutorial/configure1.png "Configurar o início de sessão único") (Configurar o início de sessão único)

5. No painel de navegação à esquerda, clique em **console do administrador**.

    ![Configure single sign-on](./media/dropboxforbusiness-tutorial/configure2.png "Configurar o início de sessão único") (Configurar o início de sessão único)

6. No **console do administrador**, clique em **configurações** no painel de navegação esquerdo.

    ![Configure single sign-on](./media/dropboxforbusiness-tutorial/configure3.png "Configurar o início de sessão único") (Configurar o início de sessão único)

7. Selecione a opção **logon único** na seção **autenticação** .

    ![Configure single sign-on](./media/dropboxforbusiness-tutorial/configure4.png "Configurar o início de sessão único") (Configurar o início de sessão único)

8. Na seção **logon único** , execute as seguintes etapas:  

    ![Configure single sign-on](./media/dropboxforbusiness-tutorial/configure5.png "Configurar o início de sessão único") (Configurar o início de sessão único)

    a. Selecione **obrigatório** como uma opção do menu suspenso para o **logon único**.

    b. Clique em **Adicionar URL de entrada** e na caixa de **texto URL de entrada do provedor de identidade** , Cole o valor da URL de **logon** copiado do portal do Azure e, em seguida, selecione **concluído**.

    ![Configure single sign-on](./media/dropboxforbusiness-tutorial/configure6.png "Configurar o início de sessão único") (Configurar o início de sessão único)

    c. Clique em **carregar certificado**e, em seguida, navegue até o **arquivo de certificado codificado na base64** que você baixou do portal do Azure.

    d. Clique em **Copiar link** e cole o valor copiado na caixa de texto **URL de logon** da seção **domínio e URLs do Dropbox for Business** em portal do Azure.

    e. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado Brenda Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao dropbox for Business.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Dropbox for Business**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-dropbox-for-business-test-user"></a>Criar usuário de teste do Dropbox for Business

Nesta seção, um usuário chamado Brenda Simon é criado no Dropbox for Business. O Dropbox for Business dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no Dropbox for Business, um novo será criado após a autenticação.

>[!Note]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte ao cliente do Dropbox for Business](https://www.dropbox.com/business/contact)

### <a name="test-sso"></a>Testar SSO

Ao selecionar o bloco Dropbox for Business no painel de acesso, você deverá entrar automaticamente no Dropbox for Business para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
