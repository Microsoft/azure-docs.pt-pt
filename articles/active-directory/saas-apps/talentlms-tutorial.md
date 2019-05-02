---
title: 'Tutorial: Integração do Active Directory do Azure com TalentLMS | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e TalentLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c17c055b79b8b90bd56a1dcd6472cf74d2512137
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922319"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Tutorial: Integração do Active Directory do Azure com TalentLMS

Neste tutorial, saiba como integrar TalentLMS com o Azure Active Directory (Azure AD).
Integrar TalentLMS no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao TalentLMS.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para TalentLMS (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com TalentLMS, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* TalentLMS único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta TalentLMS **SP** iniciada SSO

## <a name="adding-talentlms-from-the-gallery"></a>Adicionando TalentLMS da Galeria

Para configurar a integração do TalentLMS com o Azure AD, terá de adicionar TalentLMS a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar TalentLMS a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **TalentLMS**, selecione **TalentLMS** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![TalentLMS na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com TalentLMS com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no TalentLMS deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com TalentLMS, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar TalentLMS Single Sign-On](#configure-talentlms-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste TalentLMS](#create-talentlms-test-user)**  - para ter um equivalente da Eduarda Almeida na TalentLMS que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com TalentLMS, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **TalentLMS** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![TalentLMS domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<tenant-name>.TalentLMSapp.com`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente TalentLMS](https://www.talentlms.com/contact) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Na **certificado de assinatura SAML** secção, clique em **editar** botão para abrir **certificado de assinatura SAML** caixa de diálogo.

    ![Editar o certificado de assinatura de SAML](common/edit-certificate.png)

6. Na **certificado de assinatura SAML** secção, copie a **THUMBPRINT** e guarde-o no seu computador.

    ![Copie o valor do Thumbprint](common/copy-thumbprint.png)

7. Sobre o **configurar TalentLMS** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-talentlms-single-sign-on"></a>Configurar TalentLMS Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa TalentLMS como um administrador.

1. No **definições de conta &** secção, clique no **utilizadores** separador.

    ![Definições de conta &](./media/talentlms-tutorial/IC777296.png "conta & definições")

1. Clique em **início de sessão único (SSO)**,

1. Na secção de início de sessão único, execute os seguintes passos:

    ![Início de sessão único](./media/talentlms-tutorial/IC777297.png "início de sessão único")

    a. Partir do **tipo de integração de SSO** lista, selecione **SAML 2.0**.

    b. Na **fornecedor de identidade (IDP)** caixa de texto, cole o valor de **do Azure AD identificador**, que copiou do portal do Azure.

    c. Colar o **Thumbprint** valor a partir do portal do Azure para o **impressão digital do certificado** caixa de texto.

    d.  Na **URL de início de sessão remoto** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    e. Na **URL de fim de sessão remoto** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.

    f. Preencha o seguinte:

    * Na **TargetedID** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * Na **nome próprio** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * Na **Apelido** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * Na **E-Mail** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para TalentLMS.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **TalentLMS**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **TalentLMS**.

    ![A ligação de TalentLMS na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-talentlms-test-user"></a>Criar utilizador de teste TalentLMS

Para ativar a utilizadores do Azure AD iniciar sessão no TalentLMS, tem de ser aprovisionados em TalentLMS. No caso de TalentLMS, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **TalentLMS** inquilino.

1. Clique em **usuários**e, em seguida, clique em **adicionar utilizador**.

1. Sobre o **adicionar utilizador** caixa de diálogo página, execute os seguintes passos:

    ![Adicionar utilizador](./media/talentlms-tutorial/IC777299.png "adicionar utilizador")  

    a. Na **nome próprio** caixa de texto, introduza o nome de utilizador, como **Eduarda**.

    b. Na **Apelido** caixa de texto, introduza o apelido do utilizador, como **Simon**.
 
    c. Na **endereço de E-Mail** caixa de texto, introduza o e-mail do utilizador, como `brittasimon\@contoso.com`.

    d. Clique em **adicionar utilizador**.

> [!NOTE]
> Pode utilizar quaisquer outras TalentLMS utilizador conta criação ferramentas ou APIs fornecidas pelo TalentLMS para aprovisionar contas de utilizador do AAD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico TalentLMS no painel de acesso, deve ser automaticamente sessão iniciada no TalentLMS para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

