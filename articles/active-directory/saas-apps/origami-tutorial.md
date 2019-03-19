---
title: 'Tutorial: Integração do Active Directory do Azure com o Origami | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Origami e o Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a28bb0ba-b564-46ba-accc-e587699295d4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 1a3e01b7275b7d8329a9fc3bfc90e20398fdf38b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57845108"
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Tutorial: Integração do Active Directory do Azure com o Origami

Neste tutorial, saiba como integrar o Origami com o Azure Active Directory (Azure AD).
Integrar o Origami no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Origami.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Origami (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Origami, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Origami logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta origami **SP** iniciada SSO

## <a name="adding-origami-from-the-gallery"></a>Adicionando Origami da Galeria

Para configurar a integração do Origami com o Azure AD, terá de adicionar Origami a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Origami a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Origami**, selecione **Origami** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Origami na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Origami com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Origami deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Origami, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Origami Single Sign-On](#configure-origami-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Origami](#create-origami-test-user)**  - para ter um equivalente da Eduarda Almeida na Origami que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Origami, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Origami** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Origami domínio e URLs únicas início de sessão em informações](common/sp-signonurl.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://live.origamirisk.com/origami/account/login?account=<companyname>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente Origami](https://wordpress.org/support/theme/origami) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar Origami** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-origami-single-sign-on"></a>Configurar Origami início de sessão único

1. Inicie sessão para a conta de Origami com direitos de administrador.

2. No menu na parte superior, clique em **administrador**.
   
    ![Configurar o início de sessão único](./media/origami-tutorial/tutorial_origami_51.png)

3. Na página de diálogo de início de sessão único na configuração, execute os seguintes passos:
   
    ![Configurar o início de sessão único](./media/origami-tutorial/tutorial_origami_531.png)

    a. Selecione **ativar o início de sessão único em**.

    b. Na **início de sessão no URL do fornecedor de identidade da página** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    c. Na **URL de página de fim de sessão do fornecedor de identidade** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.

    d. Clique em **procurar** para carregar o certificado que transferiu do portal do Azure.

    e. Clique em **guardar alterações**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Origami.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Origami**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Origami**.

    ![A ligação de Origami na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-origami-test-user"></a>Criar utilizador de teste Origami

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Origami. 

1. Inicie sessão para a conta de Origami com direitos de administrador.

2. No menu na parte superior, clique em **administrador**.
   
    ![Configurar o início de sessão único](./media/origami-tutorial/tutorial_origami_51.png)

3. Sobre o **utilizadores e de segurança** caixa de diálogo, clique em **utilizadores**.
   
    ![Configurar o início de sessão único](./media/origami-tutorial/tutorial_origami_54.png)

4. Clique em **adicionar novo utilizador**.
   
    ![Configurar o início de sessão único](./media/origami-tutorial/tutorial_origami_55.png)

5. Na caixa de diálogo Adicionar novo utilizador, execute os seguintes passos:
   
    ![Configurar o início de sessão único](./media/origami-tutorial/tutorial_origami_56.png)

    a. Na **nome de utilizador** caixa de texto, introduza o e-mail do utilizador, como **brittasimon\@contoso.com**.

    b. Na **palavra-passe** caixa de texto, digite uma palavra-passe.

    c. Na **Confirmar palavra-passe** caixa de texto, escreva a palavra-passe novamente.

    d. Na **nome próprio** caixa de texto, introduza o nome de utilizador, como **Eduarda**.

    e. Na **sobrenome** caixa de texto, introduza o apelido do utilizador, como **Simon**.

    f. Clique em **Guardar**.
   
    ![Configurar o início de sessão único](./media/origami-tutorial/tutorial_origami_57.png)

6. Atribua **funções de utilizador** e **acesso para cliente** ao usuário. 
   
    ![Configurar o início de sessão único](./media/origami-tutorial/tutorial_origami_58.png)

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Origami no painel de acesso, deve ser automaticamente sessão iniciada no Origami para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

