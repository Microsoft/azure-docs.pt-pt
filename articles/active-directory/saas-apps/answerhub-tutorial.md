---
title: 'Tutorial: integração do Azure Active Directory com o AnswerHub | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o AnswerHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a124832bd42a0a144ebc6000b818fb825aa422ef
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73152986"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Tutorial: integração do Azure Active Directory com o AnswerHub

Neste tutorial, você aprenderá a integrar o AnswerHub com o Azure Active Directory (Azure AD).
A integração do AnswerHub ao Azure AD oferece estes benefícios:

* Você pode usar o Azure AD para controlar quem tem acesso ao AnswerHub.
* Você pode permitir que os usuários entrem automaticamente no AnswerHub com suas contas do Azure AD (logon único).
* Você pode gerenciar suas contas de um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [logon único para aplicativos no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao AnswerHub, você precisará do seguinte:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá iniciar uma [avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Uma assinatura do AnswerHub com logon único habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O AnswerHub dá suporte ao SSO iniciado por SP.

## <a name="add-answerhub-from-the-gallery"></a>Adicionar o AnswerHub da Galeria

Para configurar a integração do AnswerHub ao Azure AD, você precisará adicionar o AnswerHub da Galeria aos seus aplicativos SaaS gerenciados.

**Para adicionar o AnswerHub da Galeria:**

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**.

    ![Botão de Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um aplicativo, selecione **novo aplicativo** na parte superior da janela.

    ![Botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **AnswerHub**. Selecione **AnswerHub** na lista de resultados e, em seguida, selecione **Adicionar**.

     ![AnswerHub na lista de resultados](common/search-new-app.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o AnswerHub usando um usuário de teste chamado Brenda Simon.
Para o logon único, você precisa estabelecer um vínculo entre um usuário do Azure AD e o usuário correspondente no AnswerHub.

Para configurar e testar o logon único do Azure AD com o AnswerHub, você precisa concluir estas tarefas:

1. [Configure o logon único do Azure ad](#configure-azure-ad-single-sign-on) para permitir que os usuários usem o recurso.
2. [Configure o logon único do AnswerHub](#configure-answerhub-single-sign-on) para definir as configurações de logon único no lado do aplicativo.
3. [Crie um usuário de teste do Azure ad](#create-an-azure-ad-test-user) chamado Brenda Simon.
4. [Atribua o usuário de teste do Azure ad](#assign-the-azure-ad-test-user) para permitir que o Brenda Simon use o logon único do Azure AD.
5. Crie um usuário de teste do AnswerHub que corresponda ao e esteja vinculado ao usuário de teste do Azure AD.
6. [Teste o logon único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você configura o logon único do Azure AD no portal do Azure.

**Para configurar o logon único do Azure AD com o AnswerHub:**

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **AnswerHub** , selecione **logon único**.

    ![Botão logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Caixa de diálogo método de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , selecione o ícone Editar para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Configurar logon único com a página SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , conclua as seguintes etapas:

    ![Seção de configuração básica do SAML](common/sp-identifier.png)

    a. Na caixa **URL de logon** , insira uma URL que tenha esse padrão: `https://<company>.answerhub.com`

    b. Na caixa **identificador (ID da entidade)** , insira uma URL que tenha esse padrão: `https://<company>.answerhub.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contate a [equipe de suporte do AnswerHub](mailto:success@answerhub.com) para obter os valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , selecione o link **baixar** ao lado de **certificado (Base64)** , de acordo com seus requisitos, e salve o certificado em seu computador.

    ![Link de download de certificado](common/certificatebase64.png)

6. Na seção **Configurar AnswerHub** , copie a URL ou URLs apropriadas com base em seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

   Você pode copiar essas URLs:
    - URL de logon

    - Identificador do Azure AD

    - URL de logout

### <a name="configure-answerhub-single-sign-on"></a>Configurar o logon único do AnswerHub

Nesta seção, você configura o logon único para o AnswerHub.  

**Para configurar o logon único do AnswerHub:**

1. Em uma janela diferente do navegador da Web, entre no site da empresa do AnswerHub como um administrador.

    > [!NOTE]
    > Se precisar de ajuda para configurar o AnswerHub, entre em contato com a [equipe de suporte do AnswerHub](mailto:success@answerhub.com.).

2. Vá para **Administração**.

3. Na guia **usuários e grupos** , no painel esquerdo, na seção **configurações sociais** , selecione **configuração do SAML**.

4. Na guia **configuração do IDP** , conclua estas etapas:

    ![Guia usuários & grupos](./media/answerhub-tutorial/ic785172.png "Instalação do SAML")  
  
    a. Na caixa **URL de logon do IDP** , Cole a **URL de logon** que você copiou do portal do Azure.
  
    b. Na caixa **URL de logout do IDP** , Cole a **URL de logout** que você copiou do portal do Azure.

    c. Na caixa **formato do identificador de nome do IDP** , insira o valor do **identificador** selecionado na seção **atributos de usuário** na portal do Azure.
  
    d. Selecione **chaves e certificados**.

5. Na seção **chaves e certificados** , conclua estas etapas:

    ![Seção chaves e certificados](./media/answerhub-tutorial/ic785173.png "Chaves e certificados")  

    a. Abra o certificado codificado em base64 que você baixou do portal do Azure no bloco de notas, copie seu conteúdo e, em seguida, Cole o conteúdo na caixa **chave pública IDP (formato X509)** .
  
    b. Selecione **Guardar**.

6. Na guia **configuração do IDP** , selecione **salvar** novamente.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você cria um usuário de teste chamado Brenda Simon no portal do Azure.

**Para criar um usuário de teste do Azure AD:**

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.

    ![Selecionar Azure Active Directory, usuários, todos os usuários](common/users.png)

2. Selecione **novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do usuário, conclua estas etapas.

    ![Propriedades do utilizador](common/user-properties.png)

    a. Na caixa **nome** , digite **brendafernandes**.
  
    b. Na caixa **nome de usuário** , digite **brendafernandes\@< yourcompanydomain. Extension >** .  
    Por exemplo, BrittaSimon@contoso.com.

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você configura o usuário Brenda Simon para usar o logon único do Azure AD, concedendo acesso ao usuário ao AnswerHub.

**Para atribuir o usuário de teste do Azure AD:**

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **AnswerHub**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **AnswerHub**.

    ![Lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![Selecionar usuários e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![Adicionar painel de atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista **usuários** e, em seguida, selecione o botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista. 

7. Selecione o botão **selecionar** na parte inferior da tela.

8. Na caixa de diálogo **Adicionar atribuição** , selecione **atribuir**.

### <a name="create-an-answerhub-test-user"></a>Criar um usuário de teste do AnswerHub

Para permitir que os usuários do Azure AD entrem no AnswerHub, você precisará adicioná-los no AnswerHub. No AnswerHub, essa tarefa é feita manualmente.

**Para configurar uma conta de usuário:**

1. Entre no site da empresa do **AnswerHub** como um administrador.

2. Vá para **Administração**.

3. Selecione a guia **usuários & grupos** .

4. No painel esquerdo, na seção **gerenciar usuários** , selecione **criar ou importar usuários**e, em seguida, selecione **usuários & grupos**.

   ![Guia usuários & grupos](./media/answerhub-tutorial/ic785175.png "Grupos de & de usuários")

5. Nas caixas apropriadas, insira o **endereço de email**, o **nome de usuário**e a **senha** de uma conta válida do Azure AD que você deseja adicionar e, em seguida, selecione **salvar**.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação de conta de usuário ou API fornecida pelo AnswerHub para configurar contas de usuário do Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao selecionar o bloco AnswerHub no painel de acesso, você deverá entrar automaticamente no AnswerHub para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para integração de aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

