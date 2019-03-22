---
title: 'Tutorial: Integração do Active Directory do Azure com a plataforma de produtividade ClickUp | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e a plataforma de produtividade ClickUp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06853edd-e8da-4ad2-a4e6-5555d592cee5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: eababa0d83e1db4934fc8b2315e20407ccc77f27
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57892953"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Tutorial: Integração do Active Directory do Azure com a plataforma de produtividade ClickUp

Neste tutorial, saiba como integrar a plataforma de produtividade ClickUp com o Azure Active Directory (Azure AD).
Plataforma de produtividade ClickUp a integração com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao ClickUp plataforma de produtividade.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para a plataforma de produtividade de ClickUp (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com a plataforma de produtividade ClickUp, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Plataforma de produtividade ClickUp logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta a plataforma de produtividade ClickUp **SP** iniciada SSO

## <a name="adding-clickup-productivity-platform-from-the-gallery"></a>Adicionando a plataforma de produtividade ClickUp da Galeria

Para configurar a integração da plataforma de produtividade ClickUp com o Azure AD, terá de adicionar plataforma de produtividade ClickUp a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar plataforma de produtividade ClickUp a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **plataforma de produtividade ClickUp**, selecione **plataforma de produtividade ClickUp** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Plataforma de produtividade de ClickUp na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com a plataforma de produtividade de ClickUp com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na plataforma de produtividade ClickUp deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com a plataforma de produtividade ClickUp, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar ClickUp produtividade plataforma de início de sessão único](#configure-clickup-productivity-platform-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de plataforma de produtividade ClickUp](#create-clickup-productivity-platform-test-user)**  - para ter um equivalente da Eduarda Almeida na plataforma de produtividade de ClickUp que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com a plataforma de produtividade ClickUp, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **plataforma de produtividade ClickUp** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Domínio de plataforma de produtividade ClickUp e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL: `https://app.clickup.com/login/sso`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > O valor do identificador não é real. Atualize este valor com o identificador real, o que é explicado posteriormente neste tutorial.

5. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em botão Copiar para copiar **Url de metadados de Federação de aplicação** e guarde-o no seu computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

### <a name="configure-clickup-productivity-platform-single-sign-on"></a>Configurar ClickUp produtividade plataforma início de sessão único

1. Numa janela do browser web diferente, início de sessão no seu inquilino de plataforma de produtividade ClickUp como administrador.

2. Clique nas **perfil de utilizador** e selecione **definições**.

    ![Configuração de produtividade ClickUp](./media/clickup-productivity-platform-tutorial/configure1.png)

3. Selecione **Microsoft**, em Single Sign-On (SSO) do fornecedor.

    ![Configuração de produtividade ClickUp](./media/clickup-productivity-platform-tutorial/configure2.png)

4. Sobre o **configurar o Microsoft início de sessão único** página, execute os seguintes passos:

    ![Configuração de produtividade ClickUp](./media/clickup-productivity-platform-tutorial/configure3.png)

    a. Clique em **cópia** para copiar o valor de ID de entidade e colá-lo no **identificador (ID de entidade)** caixa de texto no **configuração básica de SAML** secção no portal do Azure.
    
    b. Na **URL de metadados de Federação do Azure** caixa de texto, cole o valor de Url de metadados de Federação de aplicação, que copiou do portal do Azure e, em seguida, clique em **guardar**.

5. Para concluir a configuração, clique em **autenticar com a Microsoft para concluir a configuração** e autenticar com a conta microsoft.

    ![Configuração de produtividade ClickUp](./media/clickup-productivity-platform-tutorial/configure4.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso à plataforma de produtividade ClickUp.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **plataforma de produtividade ClickUp**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **plataforma de produtividade ClickUp**.

    ![A ligação de plataforma de produtividade ClickUp na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-clickup-productivity-platform-test-user"></a>Criar utilizador de teste de plataforma de produtividade ClickUp

1. Numa janela do browser web diferente, início de sessão no seu inquilino de plataforma de produtividade ClickUp como administrador.

2. Clique nas **perfil de utilizador** e selecione **utilizadores**.

    ![Configuração de produtividade ClickUp](./media/clickup-productivity-platform-tutorial/user1.png)

3. Introduza o endereço de e-mail do utilizador na caixa de texto e clique em **convidar**.

    ![Configuração de produtividade ClickUp](./media/clickup-productivity-platform-tutorial/user2.png)

    > [!NOTE]
    > Utilizador irá receber a notificação e ele tem de aceitar o convite para ativar a conta.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico da plataforma de produtividade ClickUp no painel de acesso, deve ser automaticamente conectado para a plataforma de produtividade de ClickUp para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

