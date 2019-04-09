---
title: 'Tutorial: Integração do Active Directory do Azure com InsideView | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e InsideView.
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
ms.openlocfilehash: fa04edeabc12d3e4eef197d755c58464dbd78d7f
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009330"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Tutorial: Integração do Active Directory do Azure com InsideView

Neste tutorial, saiba como integrar InsideView com o Azure Active Directory (Azure AD).
Integrar InsideView no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao InsideView.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para InsideView (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com InsideView, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* InsideView logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta InsideView **IDP** iniciada SSO

## <a name="adding-insideview-from-the-gallery"></a>Adicionando InsideView da Galeria

Para configurar a integração do InsideView com o Azure AD, terá de adicionar InsideView a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar InsideView a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **InsideView**, selecione **InsideView** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![InsideView na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com InsideView com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no InsideView deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com InsideView, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar InsideView Single Sign-On](#configure-insideview-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste InsideView](#create-insideview-test-user)**  - para ter um equivalente da Eduarda Almeida na InsideView que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com InsideView, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **InsideView** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![InsideView domínio e URLs únicas início de sessão em informações](common/idp-reply.png)

    Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:  `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de resposta real. Contacte [equipa de suporte de cliente InsideView](mailto:support@insideview.com) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (bruto)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificateraw.png)

6. Sobre o **configurar InsideView** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-insideview-single-sign-on"></a>Configurar InsideView Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa InsideView como um administrador.

1. Na barra de ferramentas na parte superior, clique em **administrador**, **SingleSignOn definições**e, em seguida, clique em **SAML adicionar**.
   
   ![Início de sessão único SAML nas configurações](./media/insideview-tutorial/ic794135.png "início de sessão único SAML nas definições")

1. Na **adicionar uma nova SAML** secção, execute os seguintes passos:

    ![Adicionar um novo SAML](./media/insideview-tutorial/ic794136.png "adicionar um novo SAML")

    a. Na **nome do STS** caixa de texto, escreva um nome para a sua configuração.

    b. Na **ponto final não solicitadas de SamlP/WS-Fed** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    c. Abra o seu certificado com codificação base 64, que transferiu do portal do Azure, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado STS** caixa de texto.

    d. Na **mapeamento de Id de utilizador do Crm** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. Na **mapeamento de E-Mail de Crm** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. Na **mapeamento de nome de Crm** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    g. Na **Crm lastName mapeamento** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.  

    h. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com.

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para InsideView.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **InsideView**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **InsideView**.

    ![A ligação de InsideView na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-insideview-test-user"></a>Criar utilizador de teste InsideView

Para ativar a utilizadores do Azure AD iniciar sessão no InsideView, eles têm de ser aprovisionados para InsideView. No caso de InsideView, o aprovisionamento é uma tarefa manual.

Para obter os utilizadores ou contatos criados no InsideView, contacte [equipa de suporte de InsideView](mailto:support@insideview.com).

> [!NOTE]
> Pode utilizar quaisquer outras InsideView utilizador conta criação ferramentas ou APIs fornecidas pelo InsideView para aprovisionar contas de utilizador do Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico InsideView no painel de acesso, deve ser automaticamente sessão iniciada no InsideView para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
