---
title: 'Tutorial: integração do Azure Active Directory com o administrador de acesso à Internet do Zscaler | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e o administrador de acesso à Internet do Zscaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ea555097-bf62-45dd-9b45-b75c50324a69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a149527c6e00972991bf0b18e6f7c599799a0c91
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161033"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>Tutorial: integração do Azure Active Directory com o administrador de acesso à Internet do Zscaler

Neste tutorial, você aprenderá a integrar o administrador do Zscaler Internet Access com o Azure Active Directory (Azure AD).
A integração do administrador de acesso à Internet do Zscaler ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao administrador de acesso à Internet do Zscaler.
* Você pode permitir que seus usuários sejam automaticamente conectados ao Zscaler Internet Access Administrator (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Zscaler Internet Access Administrator, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do administrador de acesso à Internet Zscaler

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O administrador de acesso à Internet Zscaler dá suporte ao SSO iniciado pelo **IDP**

## <a name="adding-zscaler-internet-access-administrator-from-the-gallery"></a>Adicionar o administrador de acesso à Internet do Zscaler por meio da Galeria

Para configurar a integração do administrador de acesso à Internet do Zscaler ao Azure AD, você precisará adicionar o administrador de acesso à Internet do Zscaler da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o administrador de acesso à Internet do Zscaler por meio da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Zscaler Internet Access Administrator**, selecione **Zscaler Internet Access Administrator** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Administrador de acesso à Internet Zscaler na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Zscaler Internet Access Administrator, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do administrador de acesso à Internet do Zscaler.

Para configurar e testar o logon único do Azure AD com o administrador de acesso à Internet do Zscaler, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do administrador de acesso à Internet do Zscaler](#configure-zscaler-internet-access-administrator-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do administrador de acesso à Internet Zscaler](#create-zscaler-internet-access-administrator-test-user)** – para ter um equivalente de Brenda Simon no Zscaler Internet Access Administrator que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o administrador de acesso à Internet do Zscaler, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **administrador de acesso à Internet do Zscaler** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na página **Configurar logon único com SAML** , clique no botão **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Informações de logon único de domínio e URLs do administrador de acesso à Internet Zscaler](common/idp-intiated.png)

    a. Na caixa de texto **identificador** , digite uma URL de acordo com seu requisito:

    | |
    |--|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` |
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. Na caixa de texto **URL de resposta** , digite uma URL de acordo com seu requisito:

    | |
    |--|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` |
    | `https://admin.zscalertwo.net/adminsso.do` |
    | `https://admin.zscalerthree.net/adminsso.do` |
    | `https://admin.zscloud.net/adminsso.do` |
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. O aplicativo de administrador de acesso à Internet Zscaler espera que as asserções SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos na seção **atributos de usuário & declarações** na página de integração de aplicativos. Na **página Configurar logon único com SAML**, clique no botão **Editar** para abrir **atributos de usuário &** caixa de diálogo declarações.

    ![O link do atributo](./media/zscaler-internet-access-administrator-tutorial/tutorial_zscaler-internet_attribute.png)

6. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , configure o atributo de token SAML, conforme mostrado na imagem acima, e execute as seguintes etapas:

    | Nome  | Atributo de origem  |
    | ---------| ------------ |
    | Função     | User. assignedroles |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![imagem](./common/new-save-attribute.png)
    
    ![imagem](./common/new-attribute-details.png)

    b. Na lista **atributo de origem** , selelct o valor do atributo.

    c. Clique em **OK**.

    d. Clique em **Guardar**.

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) para saber como configurar a função no Azure AD

7. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download do certificado](common/certificatebase64.png)

8. Na seção **Configurar o administrador de acesso à Internet do Zscaler** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-zscaler-internet-access-administrator-single-sign-on"></a>Configurar o logon único do administrador de acesso à Internet Zscaler

1. Em uma janela diferente do navegador da Web, faça logon em sua interface do usuário do administrador de acesso à Internet do Zscaler.

2. Acesse **administração > gerenciamento de administradores** e execute as seguintes etapas e clique em salvar:

    ![Administrar](./media/zscaler-internet-access-administrator-tutorial/AdminSSO.png "Administração")

    a. Marque **habilitar autenticação SAML**.

    b. Clique em **carregar**para carregar o certificado de autenticação SAML do Azure que você baixou de portal do Azure no **certificado SSL público**.

    c. Opcionalmente, para segurança adicional, adicione os detalhes do **emissor** para verificar o emissor da resposta SAML.

3. Na interface do usuário do administrador, execute as seguintes etapas:

    ![Administração](./media/zscaler-internet-access-administrator-tutorial/ic800207.png)

    a. Passe o mouse sobre o menu de **ativação** próximo à parte inferior esquerda.

    b. Clique em **Ativar**.

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao administrador de acesso à Internet do Zscaler.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **administrador de acesso à Internet Zscaler**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **administrador de acesso à Internet do Zscaler**.

    ![O link do administrador de acesso à Internet Zscaler na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Criar usuário de teste do administrador de acesso à Internet Zscaler

O objetivo desta seção é criar um usuário chamado Brenda Simon no administrador de acesso à Internet do Zscaler. O acesso à Internet do Zscaler não dá suporte ao provisionamento just-in-time para SSO do administrador. Você precisa criar manualmente uma conta de administrador.
Para obter as etapas sobre como criar uma conta de administrador, consulte a documentação do Zscaler:

https://help.zscaler.com/zia/adding-admins

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco Zscaler Internet Access Administrator no painel de acesso, você deverá ser automaticamente conectado à interface do usuário do administrador de acesso à Internet do Zscaler para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
