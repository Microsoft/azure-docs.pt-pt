---
title: 'Tutorial: integração do Azure Active Directory com o Picturepark | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Picturepark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 08c5bd8da0dda74156b2d44c8106ed345ef749dd
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177014"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Tutorial: integração do Azure Active Directory com o Picturepark

Neste tutorial, você aprenderá a integrar o Picturepark com o Azure Active Directory (Azure AD).
A integração do Picturepark ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Picturepark.
* Você pode permitir que seus usuários sejam conectados automaticamente ao Picturepark (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Picturepark, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do Picturepark

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Picturepark dá suporte ao SSO iniciado por **SP**

## <a name="adding-picturepark-from-the-gallery"></a>Adicionando o Picturepark da Galeria

Para configurar a integração do Picturepark ao Azure AD, você precisará adicionar o Picturepark da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Picturepark da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Picturepark**, selecione **Picturepark** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Picturepark na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Picturepark, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Picturepark.

Para configurar e testar o logon único do Azure AD com o Picturepark, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Picturepark](#configure-picturepark-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do Picturepark](#create-picturepark-test-user)** – para ter um equivalente de Brenda Simon no Picturepark que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Picturepark, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Picturepark** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Picturepark](common/sp-identifier.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<companyname>.picturepark.com`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão:

    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contate a [equipe de suporte ao cliente do Picturepark](https://picturepark.com/company/picturepark-customer-support) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Na seção **certificado de autenticação SAML** , clique no botão **Editar** para abrir a caixa de diálogo **certificado de autenticação SAML** .

    ![Editar certificado de autenticação SAML](common/edit-certificate.png)

6. Na seção **certificado de autenticação SAML** , copie a **impressão digital** e salve-a no computador.

    ![Copiar valor de impressão digital](common/copy-thumbprint.png)

7. Na seção **Configurar Picturepark** , copie as URLs apropriadas de acordo com seu requisito. Para a **URL de logon**, use o valor com o seguinte padrão: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ é a ID de locatário da assinatura do Azure AD.

    ![Copiar URLs de configuração](./media/picturepark-tutorial/configurls.png)

    a. Identificador do Azure AD

    b. URL de logout

### <a name="configure-picturepark-single-sign-on"></a>Configurar o logon único do Picturepark

1. Em uma janela diferente do navegador da Web, entre no site da empresa do Picturepark como um administrador.

2. Na barra de ferramentas na parte superior, clique em **Ferramentas administrativas**e em **console de gerenciamento**.
   
    ![Console de gerenciamento](./media/picturepark-tutorial/ic795062.png "Console de gerenciamento")

3. Clique em **autenticação**e em **provedores de identidade**.
   
    ![Autenticação](./media/picturepark-tutorial/ic795063.png "Autenticação")

4. Na seção **configuração do provedor de identidade** , execute as seguintes etapas:
   
    ![Configuração do provedor de identidade](./media/picturepark-tutorial/ic795064.png "Configuração do provedor de identidade")
   
    a. Clique em **Adicionar**.
  
    b. Digite um nome para a sua configuração.
   
    c. Selecione **definir como padrão**.
   
    d. Na caixa de texto **URI do emissor** , Cole o valor da **URL de logon** que você copiou do portal do Azure.
   
    e. Na caixa de texto **Imprimir miniatura do emissor confiável** , Cole o valor da **impressão digital** que você copiou da seção **certificado de autenticação SAML** . 

5. Clique em **JoinDefaultUsersGroup**.

6. Para definir o atributo **EmailAddress** na caixa de texto **declaração** , digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` e clique em **salvar**.

      ![Configuração](./media/picturepark-tutorial/ic795065.png "Configuração")

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no portal do Azure chamado Brenda Simon.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.

    ![Os links "usuários e grupos" e "todos os usuários"](common/users.png)

2. Selecione **novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo usuário](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao Picturepark.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Picturepark**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Picturepark**.

    ![O link do Picturepark na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-picturepark-test-user"></a>Criar usuário de teste do Picturepark

Para permitir que os usuários do AD do Azure entrem no Picturepark, eles devem ser provisionados no Picturepark. No caso do Picturepark, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no seu locatário do **Picturepark** .

1. Na barra de ferramentas na parte superior, clique em **Ferramentas administrativas**e em **usuários**.
   
    ![Utilizadores](./media/picturepark-tutorial/ic795067.png "Utilizadores")

1. Na guia **visão geral dos usuários** , clique em **novo**.
   
    ![Gerenciamento de usuários](./media/picturepark-tutorial/ic795068.png "Gerenciamento de usuários")

1. Na caixa de diálogo **criar usuário** , execute as seguintes etapas de um usuário válido do Azure Active Directory que você deseja provisionar:
   
    ![Criar usuário](./media/picturepark-tutorial/ic795069.png "Criar usuário")
   
    a. Na caixa de texto **endereço de email** , digite o **endereço de email** do usuário `BrittaSimon@contoso.com`.  
   
    b. Nas caixas de texto **senha** e **Confirmar senha** , digite a **senha** de brendafernandes. 
   
    c. Na caixa de texto **nome** , digite o **nome** do usuário **Brenda**. 
   
    d. Na caixa de texto **sobrenome** , digite o **sobrenome** do usuário **Simon**.
   
    e. Na caixa de texto **empresa** , digite o **nome da empresa** do usuário. 
   
    f. Na caixa de texto **país** , selecione o **país/região** do usuário.
  
    g. Na caixa de texto **zip** , digite o **CEP** da cidade.
   
    h. Na caixa de texto **cidade** , digite o **nome da cidade** do usuário.

    i. Selecione um **idioma**.
   
    j. Clique em **Criar**.

>[!NOTE]
>Você pode usar qualquer outra ferramenta de criação de conta de usuário Picturepark ou APIs fornecidas pelo Picturepark para provisionar contas de usuário do Azure AD.
>

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do Picturepark no painel de acesso, você deverá ser conectado automaticamente ao Picturepark para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

