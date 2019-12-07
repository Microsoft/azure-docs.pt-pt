---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Moxtra | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Moxtra.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3e53ba11744b0e78287ffc46c4aac7b99b16b23
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74889639"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-moxtra"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Moxtra

Neste tutorial, você aprenderá a integrar o Moxtra com o Azure Active Directory (Azure AD). Ao integrar o Moxtra ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Moxtra.
* Habilite seus usuários a serem conectados automaticamente ao Moxtra com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Moxtra.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Moxtra dá suporte ao SSO iniciado por **SP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-moxtra-from-the-gallery"></a>Adicionando o Moxtra da Galeria

Para configurar a integração do Moxtra ao Azure AD, você precisará adicionar o Moxtra da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Moxtra** na caixa de pesquisa.
1. Selecione **Moxtra** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-moxtra"></a>Configurar e testar o logon único do Azure AD para o Moxtra

Configure e teste o SSO do Azure AD com o Moxtra usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Moxtra.

Para configurar e testar o SSO do Azure AD com o Moxtra, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do Moxtra](#configure-moxtra-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Moxtra](#create-moxtra-test-user)** – para ter um equivalente de B. Simon em Moxtra que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Moxtra** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon** , digite uma URL: `https://www.moxtra.com/service/#login`

1. O aplicativo Moxtra espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo atributos de usuário.

    ![imagem](common/edit-attribute.png)

1. Além de acima, o aplicativo Moxtra espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção declarações do usuário, na caixa de diálogo atributos de usuário, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | Nome | Atributo de origem|
    | ------------------- | -------------------- |    
    | nomepróprio | user.givenname |
    | apelido | User. sobrenome |
    | idpid    | < O identificador do Azure AD >

    > [!Note]
    > O valor do atributo **idpid** não é real. Você pode obter o valor real na seção **Configurar Moxtra** da etapa 8. 

    1. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    1. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    1. Deixe o **namespace** em branco.

    1. Selecione origem como **atributo**.

    1. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    1. Clique em **Ok**

    1. Clique em **Guardar**.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na seção **Configurar Moxtra** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao Moxtra.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Moxtra**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-moxtra-sso"></a>Configurar o SSO do Moxtra

1. Em outra janela do navegador, faça logon em seu site de empresa do Moxtra como administrador.

2. Na barra de ferramentas à esquerda, clique em **console do administrador > logon único do SAML**e clique em **novo**.
   
    ![Configurar o início de sessão único](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

3. Na página **SAML** , execute as seguintes etapas:
   
    ![Configurar o início de sessão único](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. Na caixa de texto **nome** , digite um nome para a sua configuração (por exemplo: *SAML*). 
  
    b. Na caixa de texto **ID da entidade do IDP** , Cole o valor do identificador do **Azure ad** que você copiou do portal do Azure. 
 
    c. Na caixa de texto **URL de logon** , Cole o valor da **URL de logon** que você copiou do portal do Azure. 
 
    d. Na caixa de texto **AuthnContextClassRef** , digite **urn: Oasis: names: TC: SAML: 2.0: AC: classes: Password**. 
 
    e. Na caixa de texto **formato de NameID** , digite **urn: Oasis: names: TC: SAML: 1.1: NameID-Format: EmailAddress**. 
 
    f. Abra o certificado que você baixou de portal do Azure no bloco de notas, copie o conteúdo e cole-o na caixa de texto **certificado** .    
 
    g. Na caixa de texto domínio de email do SAML, digite seu domínio de email do SAML.    
  
    >[!NOTE]
    >Para ver as etapas para verificar o domínio, clique no "**i**" abaixo.

    h. Clique em **Atualizar**.

### <a name="create-moxtra-test-user"></a>Criar usuário de teste do Moxtra

O objetivo desta seção é criar um usuário chamado B. Simon em Moxtra.

**Para criar um usuário chamado B. Simon em Moxtra, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do Moxtra como administrador.

1. Na barra de ferramentas à esquerda, clique em **console do administrador > gerenciamento de usuário**e, em seguida, **adicione usuário**.
   
    ![Configurar o início de sessão único](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. Na caixa de diálogo **Adicionar usuário** , execute as seguintes etapas:
  
    a. Na caixa de texto **nome** , digite **B**.
  
    b. Na caixa de texto **sobrenome** , digite **Simon**.
  
    c. Na caixa de texto **email** , digite o endereço de email do B. Simon igual a em portal do Azure.
  
    d. Na caixa de texto **divisão** , digite **dev**.
  
    e. Na caixa de texto **Departamento** , digite **-** o.
  
    f. Selecione **administrador**.
  
    g. Clique em **Adicionar**.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Moxtra no painel de acesso, você deverá ser conectado automaticamente ao Moxtra para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Moxtra com o Azure AD](https://aad.portal.azure.com/)

