---
title: 'Tutorial: integração do Azure Active Directory com o Kintone | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Kintone.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c2b947dc-e1a8-4f5f-b40e-2c5180648e4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 6786b44aca9ceed3cec5daf0f858a51e2dd12833
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227586"
---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>Tutorial: integração do Azure Active Directory com o Kintone

Neste tutorial, você aprenderá a integrar o Kintone com o Azure Active Directory (Azure AD).
A integração do Kintone ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Kintone.
* Você pode permitir que seus usuários sejam conectados automaticamente ao Kintone (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Kintone, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do Kintone

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Kintone dá suporte ao SSO iniciado por **SP**

## <a name="adding-kintone-from-the-gallery"></a>Adicionando o Kintone da Galeria

Para configurar a integração do Kintone ao Azure AD, você precisará adicionar o Kintone da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Kintone da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Kintone**, selecione **Kintone** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Kintone na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o Kintone, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Kintone.

Para configurar e testar o logon único do Azure AD com o Kintone, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Kintone](#configure-kintone-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do Kintone](#create-kintone-test-user)** – para ter um equivalente de Brenda Simon no Kintone que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Kintone, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Kintone** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Kintone](common/sp-identifier.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<companyname>.kintone.com`

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão:
    
    | |
    |--|
    | `https://<companyname>.cybozu.com` |
    | `https://<companyname>.kintone.com` |

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. Contate a [equipe de suporte ao cliente do Kintone](https://www.kintone.com/contact/) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Na seção **Configurar Kintone** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-kintone-single-sign-on"></a>Configurar o logon único do Kintone

1. Em uma janela diferente do navegador da Web, entre no site da empresa do **Kintone** como um administrador.

1. Clique no **ícone de configurações**.

    ![Definições](./media/kintone-tutorial/ic785879.png "Definições")

1. Clique em **usuários & administração do sistema**.

    ![Usuários & administração do sistema](./media/kintone-tutorial/ic785880.png "Usuários & administração do sistema")

1. Em **Administração do sistema \> segurança** , clique em **logon**.

    ![Início de sessão](./media/kintone-tutorial/ic785881.png "Iniciar sessão")

1. Clique em **habilitar autenticação SAML**.

    ![Autenticação SAML](./media/kintone-tutorial/ic785882.png "Autenticação SAML")

1. Na seção autenticação SAML, execute as seguintes etapas:

    ![Autenticação SAML](./media/kintone-tutorial/ic785883.png "Autenticação SAML")

    a. Na caixa de texto **URL de logon** , Cole o valor da **URL de logon** que você copiou do portal do Azure.

    b. Na caixa de texto **URL de logout** , Cole o valor da **URL de logout** que você copiou do portal do Azure.

    c. Clique em **procurar** para carregar o arquivo de certificado baixado de portal do Azure.

    d. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite `brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao Kintone.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Kintone**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Kintone**.

    ![O link do Kintone na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-kintone-test-user"></a>Criar usuário de teste do Kintone

Para permitir que os usuários do Azure AD entrem no Kintone, eles devem ser provisionados no Kintone. No caso do Kintone, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para provisionar uma conta de usuário, execute as seguintes etapas:

1. Entre no site da empresa do **Kintone** como um administrador.

1. Clique no **ícone de configurações**.

    ![Definições](./media/kintone-tutorial/ic785879.png "Definições")

1. Clique em **usuários & administração do sistema**.

    ![Administração do sistema de & de usuário](./media/kintone-tutorial/ic785880.png "Administração do sistema de & de usuário")

1. Em **Administração do usuário**, clique em **departamentos & usuários**.

    ![Departamento & usuários](./media/kintone-tutorial/ic785888.png "Departamento & usuários")

1. Clique em **novo usuário**.

    ![Novos usuários](./media/kintone-tutorial/ic785889.png "Novos usuários")

1. Na seção **novo usuário** , execute as seguintes etapas:

    ![Novos usuários](./media/kintone-tutorial/ic785890.png "Novos usuários")

    a. Digite um **nome de exibição**, **nome de logon**, **nova senha**, **Confirmar senha**, **endereço de email**e outros detalhes de uma conta válida do Azure AD que você deseja provisionar nas caixas de texto relacionadas.

    b. Clique em **Guardar**.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação de conta de usuário Kintone ou APIs fornecidas pelo Kintone para provisionar contas de usuário do Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Kintone no painel de acesso, você deverá ser conectado automaticamente ao Kintone para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
