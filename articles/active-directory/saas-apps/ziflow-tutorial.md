---
title: 'Tutorial: Integração do Active Directory do Azure com Ziflow | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Ziflow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 02c09744130bec55eed4181b0d4ba958aec59e69
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65905371"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Tutorial: Integração do Active Directory do Azure com Ziflow

Neste tutorial, saiba como integrar Ziflow com o Azure Active Directory (Azure AD).
Integrar Ziflow no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Ziflow.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Ziflow (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Ziflow, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Ziflow logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Ziflow **SP** iniciada SSO

## <a name="adding-ziflow-from-the-gallery"></a>Adicionando Ziflow da Galeria

Para configurar a integração do Ziflow com o Azure AD, terá de adicionar Ziflow a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Ziflow a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Ziflow**, selecione **Ziflow** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Ziflow na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Ziflow com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Ziflow deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Ziflow, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Ziflow Single Sign-On](#configure-ziflow-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Ziflow](#create-ziflow-test-user)**  - para ter um equivalente da Eduarda Almeida na Ziflow que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Ziflow, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Ziflow** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Ziflow domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > Os valores anteriores não são reais. Atualizar o valor ID exclusivo do identificador e o URL de início de sessão com o valor real, o que é explicado mais tarde no tutorial.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar Ziflow** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-ziflow-single-sign-on"></a>Configurar Ziflow Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no Ziflow como um administrador de segurança.

2. Clique em Avatar no canto superior direito e, em seguida, clique em **gerir conta**.

    ![Gerir a configuração de Ziflow](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

3. No canto superior esquerdo, clique em **Single Sign-On**.

    ![Início de sessão de configuração de Ziflow](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

4. Sobre o **Single Sign-On** página, execute os seguintes passos:

    ![Ziflow configuração única](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Selecione **tipo** como **SAML2.0**.

    b. Na **URL de início de sessão** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    c. Carregar o certificado codificado de base 64 que transferiu do portal do Azure, para o **X509 certificado de assinatura**.

    d. Na **URL de fim de sessão** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.

    e. Do **definições de configuração para o seu fornecedor de identificador** secção, copie o valor de ID exclusivo realçado e acrescentá-lo com o identificador e o URL de início de sessão a **configuração básica de SAML** no Azure Portal.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Ziflow.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Ziflow**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Ziflow**.

    ![A ligação de Ziflow na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-ziflow-test-user"></a>Criar utilizador de teste Ziflow

Para ativar a utilizadores do Azure AD iniciar sessão no Ziflow, tem de ser aprovisionados em Ziflow. Ziflow, aprovisionamento é uma tarefa manual.

Para Aprovisionar uma conta de utilizador, execute os seguintes passos:

1. Inicie sessão no Ziflow como um administrador de segurança.

2. Navegue para **pessoas** na parte superior.

    ![Pessoas de configuração de Ziflow](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Clique em **Add** e, em seguida, clique em **adicionar utilizador**.

    ![Adicionar o utilizador Ziflow configuração](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. Sobre o **adicionar um utilizador** pop-up, execute os seguintes passos:

    ![Adicionar o utilizador Ziflow configuração](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. Na **E-Mail** texto, introduza o e-mail do utilizador, como brittasimon@contoso.com.

    b. Na **nome próprio** texto, introduza o nome de utilizador, como a Eduarda.

    c. Na **Apelido** texto, digite o apelido do utilizador, como Simon.

    d. Selecione a sua função de Ziflow.

    e. Clique em **adicionar 1 utilizador**.

    > [!NOTE]
    > O titular da conta do Azure Active Directory recebe uma mensagem de e-mail e segue uma ligação para confirmar a respetiva conta até se tornar Active Directory.

### <a name="test-single-sign-on"></a>Testar início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Ziflow no painel de acesso, deve ser automaticamente sessão iniciada no Ziflow para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

