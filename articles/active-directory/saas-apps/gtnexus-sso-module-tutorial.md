---
title: 'Tutorial: integração do Azure Active Directory com o sistema SSO do GTNexus | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o sistema de SSO GTNexus.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: e677a161-1662-4eb3-b48a-b2835470b59e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99f9ad340ea3119d9d08eebef3102af8ba2c014e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73157934"
---
# <a name="tutorial-azure-active-directory-integration-with-gtnexus-sso-system"></a>Tutorial: integração do Azure Active Directory com o sistema SSO do GTNexus

Neste tutorial, você aprenderá a integrar o sistema SSO GTNexus com o Azure Active Directory (Azure AD).
A integração do sistema SSO do GTNexus ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao sistema de SSO do GTNexus.
* Você pode permitir que seus usuários sejam conectados automaticamente ao sistema SSO do GTNexus (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao sistema de SSO do GTNexus, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do sistema SSO do GTNexus

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O sistema de SSO GTNexus dá suporte ao SSO iniciado pelo **IDP**

## <a name="adding-gtnexus-sso-system-from-the-gallery"></a>Adicionando o sistema SSO GTNexus da Galeria

Para configurar a integração do sistema de SSO do GTNexus ao Azure AD, você precisará adicionar o sistema SSO do GTNexus da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o GTNexus SSO System por meio da galeria, execute as seguintes etapas:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **sistema de SSO do GTNexus**, selecione **sistema de SSO do GTNexus** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Sistema de SSO GTNexus na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o sistema SSO do GTNexus, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do sistema de SSO do GTNexus.

Para configurar e testar o logon único do Azure AD com o sistema de SSO do GTNexus, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o logon único do sistema SSO do GTNexus](#configure-gtnexus-sso-system-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar usuário de teste do GTNEXUS SSO System](#create-gtnexus-sso-system-test-user)** – para ter um equivalente de Brenda Simon no sistema SSO GTNexus que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o sistema de SSO do GTNexus, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **sistema SSO do GTNexus** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:

    a. Clique em **carregamento de ficheiro de metadados**.

    ![image](common/upload-metadata.png)

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    ![image](common/browse-upload-metadata.png)

    c. Depois que o arquivo de metadados for carregado com êxito, os valores do **identificador** e da **URL de resposta** serão preenchidos automaticamente na caixa de texto da seção do sistema SSO do GTNexus:

    ![image](common/idp-intiated.png)

    > [!Note]
    > Se os valores do **identificador** e da **URL de resposta** não estiverem sendo polulated automáticos, preencha os valores manualmente de acordo com seu requisito.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download de certificado](common/metadataxml.png)

### <a name="configure-gtnexus-sso-system-single-sign-on"></a>Configurar o logon único do sistema SSO do GTNexus

Para configurar o logon único no lado do **sistema GTNEXUS SSO** , é necessário enviar o **XML de metadados de Federação** para a equipe de suporte do [sistema SSO do GTNexus](mailto:support@gtnexus.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite **brendafernandes\@yourcompanydomain. Extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao sistema de SSO GTNexus.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **sistema SSO GTNexus**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **GTNEXUS SSO System**.

    ![O link do sistema SSO do GTNexus na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-gtnexus-sso-system-test-user"></a>Criar usuário de teste do GTNexus SSO System

Nesta seção, você criará um usuário chamado Brenda Simon no sistema SSO do GTNexus. Trabalhe com a [equipe de suporte do GTNEXUS SSO system](mailto:support@gtnexus.com) para adicionar os usuários na plataforma de sistema do SSO do GTNexus. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco GTNexus SSO System no painel de acesso, você deverá ser automaticamente conectado ao sistema SSO do GTNexus para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
