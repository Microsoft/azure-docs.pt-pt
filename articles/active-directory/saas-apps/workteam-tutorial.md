---
title: 'Tutorial: Integração do Active Directory do Azure com Workteam | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Workteam.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41df17a1-ba69-414f-8ec3-11079b030df6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 697f5c06d2c1d6b669cfa244f0328f4fb86aeea2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086840"
---
# <a name="tutorial-azure-active-directory-integration-with-workteam"></a>Tutorial: Integração do Active Directory do Azure com Workteam

Neste tutorial, saiba como integrar Workteam com o Azure Active Directory (Azure AD).
Integrar Workteam no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Workteam.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Workteam (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Workteam, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Workteam logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Workteam **SP** e **IDP** iniciada SSO

## <a name="adding-workteam-from-the-gallery"></a>Adicionando Workteam da Galeria

Para configurar a integração do Workteam com o Azure AD, terá de adicionar Workteam a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Workteam a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Workteam**, selecione **Workteam** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Workteam na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Workteam com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Workteam deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Workteam, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Workteam Single Sign-On](#configure-workteam-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Workteam](#create-workteam-test-user)**  - para ter um equivalente da Eduarda Almeida na Workteam que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Workteam, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Workteam** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Na **configuração básica de SAML** secção, se desejar configurar a aplicação na **IDP** modo iniciado, o utilizador não tem de efetuar qualquer passo dado que a aplicação já está pré-integrada com o Azure.

    ![Workteam domínio e URLs únicas início de sessão em informações](common/preintegrated.png)

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Workteam domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL:  `https://app.workte.am`

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

7. Sobre o **configurar Workteam** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-workteam-single-sign-on"></a>Configurar Workteam Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no Workteam como um administrador de segurança.

2. No canto superior direito, clique em **logótipo de perfil** e, em seguida, clique em **definições de organização**. 

    ![Definições de Workteam](./media/workteam-tutorial/tutorial_workteam_settings.png)

3. Sob **autenticação** secção, clique em **logótipo definições**.

     ![Workteam azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

4. Sobre o **definições de SAML** página, execute os seguintes passos:

     ![Workteam saml](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. Selecione **SAML IdP** como **AD Azure**.

    b. Na **SAML único início de sessão no URL do serviço** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    c. Na **ID de entidade de SAML** caixa de texto, cole o valor de **do Azure AD identificador**, que copiou do portal do Azure.

    d. No bloco de notas, abra a **certificado com codificação base 64** que transferiu a partir do portal do Azure, copie o seu conteúdo e, em seguida, cole-o para o **certificado de assinatura de SAML (Base64)** caixa.

    e. Clique em **OK**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Workteam.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Workteam**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Workteam**.

    ![A ligação de Workteam na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-workteam-test-user"></a>Criar utilizador de teste Workteam

Para ativar a utilizadores do Azure AD iniciar sessão no Workteam, tem de ser aprovisionados em Workteam. Workteam, aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no Workteam como um administrador de segurança.

2. Na parte superior ao centro do **definições de organização** página, clique em **utilizadores** e, em seguida, clique em **novo utilizador**.

    ![Utilizador Workteam](./media/workteam-tutorial/tutorial_workteam_user.png)

3. Sobre o **novo funcionário** página, execute os seguintes passos:

    ![Workteam newuser](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. Na **Name** texto, introduza o nome de utilizador, como **Brittasimon**.

    b. Na **E-Mail** texto, introduza o e-mail do utilizador, como **Brittasimon\@contoso.com**.

    c. Clique em **OK**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Workteam no painel de acesso, deve ser automaticamente sessão iniciada no Workteam para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

