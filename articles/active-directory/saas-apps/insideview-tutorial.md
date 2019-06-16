---
title: 'Tutorial: Integração do Active Directory do Azure com InsideView | Documentos da Microsoft'
description: Neste tutorial, irá aprender como configurar o início de sessão único entre o Azure Active Directory e InsideView.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: 2149b8410104b39652b176895a31b42e094265f5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100096"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Tutorial: Integração do Active Directory do Azure com InsideView

Neste tutorial, irá aprender como integrar InsideView com o Azure Active Directory (Azure AD).
Esta integração oferece estes benefícios:

* Pode utilizar o Azure AD para controlar quem tem acesso ao InsideView.
* Pode permitir que os utilizadores ter automaticamente sessão iniciada no InsideView (início de sessão único) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central: portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com InsideView, tem de ter:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Uma subscrição de InsideView com início de sessão único ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, irá configurar e testar o Azure AD início de sessão único num ambiente de teste.

* InsideView suporta SSO iniciado por IdP.

## <a name="add-insideview-from-the-gallery"></a>Adicionar InsideView a partir da Galeria

Para configurar a integração do InsideView com o Azure AD, terá de adicionar InsideView a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Na [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais** > **todas as aplicações**:

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **nova aplicação** na parte superior da janela:

    ![Selecionar novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **InsideView**. Selecione **InsideView** nos resultados da pesquisa e, em seguida, selecione **Add**.

    ![Resultados da pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, irá configurar e testar o Azure AD início de sessão único com InsideView com um utilizador de teste com o nome Eduarda Almeida.
Para ativar o início de sessão único, tem de estabelecer uma relação entre um utilizador do Azure AD e o utilizador correspondente no InsideView.

Para configurar e testar o Azure AD início de sessão único com InsideView, tem de concluir estes passos:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  para ativar a funcionalidade para os seus utilizadores.
2. **[Configurar InsideView início de sessão único](#configure-insideview-single-sign-on)**  no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar o Azure AD início de sessão único para o utilizador.
5. **[Criar um utilizador de teste InsideView](#create-an-insideview-test-user)**  associado a representação do Azure AD do utilizador.
6. **[Testar início de sessão único](#test-single-sign-on)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, irá ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com InsideView, siga estes passos:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicações do InsideView, selecione **início de sessão único**:

    ![Selecione início de sessão único](common/select-sso.png)

2. Na **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único:

    ![Selecionar um método de início de sessão único](common/select-saml-option.png)

3. Na **definir a segurança de início de sessão único com o SAML** página, selecione a **editar** ícone para abrir o **configuração básica de SAML** caixa de diálogo:

    ![Ícone editar](common/edit-urls.png)

4. Na **configuração básica de SAML** diálogo caixa, siga os passos seguintes.

    ![Caixa de diálogo de configuração de SAML básica](common/idp-reply.png)

    Na **URL de resposta** , introduza um URL neste padrão:

    `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > Este valor é um marcador de posição. Tem de utilizar o URL de resposta real. Contacte os [equipa de suporte de InsideView](mailto:support@insideview.com) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** caixa de diálogo no portal do Azure.

5. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, selecione o **transferir** ligação junto a **certificado (bruto)** acordo com suas necessidades e guarde o certificado no seu computador:

    ![Ligação de transferência do certificado](common/certificateraw.png)

6. Na **configurar InsideView** secção, copie os URLs adequados, com base nos seus requisitos:

    ![Copie os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de início de sessão**.

    1. **Azure AD Identifier**.

    1. **URL de fim de sessão**.

### <a name="configure-insideview-single-sign-on"></a>Configurar InsideView início de sessão único

1. Numa nova janela de browser web, inicie sessão no site da sua empresa InsideView como um administrador.

1. Na parte superior da janela, selecione **administrador**, **SingleSignOn definições**e, em seguida **SAML adicionar**.
   
   ![SAML únicas configurações de logon](./media/insideview-tutorial/ic794135.png "únicas início de sessão em definições de SAML")

1. Na **adicionar uma nova SAML** secção, siga os passos seguintes.

    ![Adicionar uma secção nova SAML](./media/insideview-tutorial/ic794136.png "adicionar uma secção de SAML novo")

    1. Na **nome do STS** , introduza um nome para a sua configuração.

    1. Na **ponto final não solicitadas de SamlP/WS-Fed** caixa, cole a **URL de início de sessão** valor que copiou do portal do Azure.

    1. Abra o certificado não processado que transferiu a partir do portal do Azure. Copie o conteúdo do certificado para a área de transferência e, em seguida, cole o conteúdo para o **certificado STS** caixa.

    1. Na **mapeamento de Id de utilizador do Crm** , introduza **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** .

    1. Na **mapeamento de E-Mail de Crm** , introduza **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** .

    1. Na **mapeamento de nome de Crm** , introduza **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname** .

    1. Na **Crm lastName mapeamento** , introduza **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname** .  

    1. Selecione **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste com o nome Eduarda Almeida no portal do Azure.

1. No portal do Azure, selecione **do Azure Active Directory** no painel esquerdo, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**:

    ![Selecione todos os utilizadores](common/users.png)

2. Selecione **novo utilizador** na parte superior da janela:

    ![Selecione o novo utilizador](common/new-user.png)

3. Na **utilizador** diálogo caixa, siga os passos seguintes.

    ![Caixa de diálogo de utilizador](common/user-properties.png)

    1. Na **Name** , introduza **BrittaSimon**.
  
    1. Na **nome de utilizador** , introduza **BrittaSimon @\<yourcompanydomain >.\< extensão >** . (Por exemplo, BrittaSimon@contoso.com.)

    1. Selecione **mostrar palavra-passe**e, em seguida, anote o valor que está a **palavra-passe** caixa.

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar a Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para InsideView.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **InsideView**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **InsideView**.

    ![Lista de aplicações](common/all-applications.png)

3. No painel esquerdo, selecione **utilizadores e grupos**:

    ![Selecionar utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **adicionar utilizador**e, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Selecione adicionar utilizador](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista de utilizadores e, em seguida, clique o **selecionar** na parte inferior da janela.

6. Se um valor de função na asserção de SAML, que espera do **selecionar função** diálogo caixa, selecione a função adequada para o utilizador a partir da lista. Clique nas **selecione** na parte inferior da janela.

7. Na **adicionar atribuição** caixa de diálogo, selecione **atribuir**.

### <a name="create-an-insideview-test-user"></a>Criar um utilizador de teste InsideView

Para permitir que utilizadores do Azure AD iniciar sessão no InsideView, terá de adicioná-los ao InsideView. Tem de adicioná-los manualmente.

Para criar utilizadores ou contactos no InsideView, entre em contato com o [equipa de suporte de InsideView](mailto:support@insideview.com).

> [!NOTE]
> Pode utilizar qualquer ferramenta de criação de conta de utilizador ou API fornecidos pela InsideView para aprovisionar contas de utilizador do Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Agora precisa testar a configuração do Azure AD única início de sessão utilizando o painel de acesso.

Ao selecionar o mosaico InsideView no painel de acesso, deve ser automaticamente conectado à instância InsideView para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [aplicações de acesso e a utilização no portal minhas aplicações](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
