---
title: 'Tutorial: integração do Azure Active Directory com arquivos Flatter | Microsoft Docs'
description: Saiba como configurar o logon único entre arquivos Azure Active Directory e Flatter.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f86fe5e3-0e91-40d6-869c-3df6912d27ea
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 13012474e34af80c84b034703a3b34f0208036bc
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73156264"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Tutorial: integração do Azure Active Directory com arquivos Flatter

Neste tutorial, você aprenderá a integrar arquivos Flatter com o Azure Active Directory (Azure AD).
A integração de arquivos Flatter ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso aos arquivos Flatter.
* Você pode permitir que seus usuários façam logon automaticamente no Flatter Files (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Flatter files, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Flatter files

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* Arquivos Flatter dão suporte ao SSO iniciado pelo **IDP**

## <a name="adding-flatter-files-from-the-gallery"></a>Adicionando arquivos Flatter da Galeria

Para configurar a integração do Flatter files ao Azure AD, você precisa adicionar arquivos Flatter da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar arquivos Flatter da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Flatter files**, selecione **Flatter files** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Arquivos Flatter na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Flatter files, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Flatter files.

Para configurar e testar o logon único do Azure AD com o Flatter files, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Flatter files](#configure-flatter-files-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do Flatter files](#create-flatter-files-test-user)** – para ter um equivalente de Brenda Simon em arquivos Flatter que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Flatter files, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Flatter files** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , o usuário não precisa executar nenhuma etapa, pois o aplicativo já está previamente integrado ao Azure.

    ![Informações de logon único de domínio e URLs do Flatter files](common/preintegrated.png)

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download do certificado](common/certificatebase64.png)

6. Na seção **Configurar arquivos do Flatter** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-flatter-files-single-sign-on"></a>Configurar o logon único de arquivos Flatter

1. Faça logon no aplicativo Flatter files como administrador.

2. Clique em **painel**. 
   
    ![Configurar logon único](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

3. Clique em **configurações**e, em seguida, execute as seguintes etapas na guia **empresa** : 
   
    ![Configurar logon único](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. Selecione **usar SAML 2,0 para autenticação**.
    
    b. Clique em **Configurar SAML**.

4. Na caixa de diálogo **configuração do SAML** , execute as seguintes etapas: 
   
    ![Configurar logon único](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. Na caixa de texto **domínio** , digite seu domínio registrado.
   
   > [!NOTE]
   > Se você ainda não tiver um domínio registrado, entre em contato com a equipe de suporte do Flatter files via [support@flatterfiles.com](mailto:support@flatterfiles.com). 
    
    b. Na caixa de texto **URL do provedor de identidade** , Cole o valor da URL de **logon** que você copiou o formulário portal do Azure.
   
    c.  Abra seu certificado codificado em base 64 no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **certificado do provedor de identidade** .

    d. Clique em **Atualizar**.

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso aos arquivos Flatter.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **arquivos Flatter**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Flatter arquivos**.

    ![O link de arquivos Flatter na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-flatter-files-test-user"></a>Criar usuário de teste de arquivos Flatter

O objetivo desta seção é criar um usuário chamado Brenda Simon em Flatter files.

**Para criar um usuário chamado Brenda Simon em Flatter files, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **Flatter files** como administrador.

2. No painel de navegação à esquerda, clique em **configurações**e, em seguida, clique na guia **usuários** .
   
    ![Criar um usuário de arquivos Flatter](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Clique em **Adicionar usuário**. 

4. Na caixa de diálogo **Adicionar usuário** , execute as seguintes etapas:
   
    ![Criar um usuário de arquivos Flatter](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. Na caixa de texto **nome** , digite **Brenda**.
   
    b. Na caixa de texto **sobrenome** , digite **Simon**. 
   
    c. Na caixa de texto **endereço de email** , digite o endereço de email de brenda no portal do Azure.
   
    d. Clique em **Enviar**.   


### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco arquivos Flatter no painel de acesso, você deverá entrar automaticamente nos arquivos Flatter para os quais você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

