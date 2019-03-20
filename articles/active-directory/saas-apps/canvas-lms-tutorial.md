---
title: 'Tutorial: Integração do Active Directory do Azure com o Canvas | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e a tela.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: bfed291c-a33e-410d-b919-5b965a631d45
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7556e93e6e7b7e9bdb1f19f5a6e5b3c15c7b910
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57894075"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas"></a>Tutorial: Integração do Active Directory do Azure com o Canvas

Neste tutorial, saiba como integrar a tela com o Azure Active Directory (Azure AD).
Integração baseada em telas com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso à tela.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para a tela (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Canvas, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Tela de logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Tela suporta **SP** iniciada SSO

## <a name="adding-canvas-from-the-gallery"></a>Adicionando a tela da Galeria

Para configurar a integração da tela com o Azure AD, terá de adicionar baseadas em telas a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar baseadas em telas a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **tela**, selecione **tela** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Tela na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com tela com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na tela deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Canvas, precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar a tela de início de sessão único](#configure-canvas-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste baseada em telas](#create-canvas-test-user)**  - para ter um equivalente da Eduarda Almeida na tela que está ligada à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Canvas, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **tela** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Domínio de tela e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<tenant-name>.instructure.com`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente baseada em telas](https://community.canvaslms.com/community/help) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Na **certificado de assinatura SAML** secção, clique em **editar** botão para abrir **certificado de assinatura SAML** caixa de diálogo.

    ![Editar o certificado de assinatura de SAML](common/edit-certificate.png)

6. Na **certificado de assinatura SAML** secção, copie a **THUMBPRINT** e guarde-o no seu computador.

    ![Copie o valor do Thumbprint](common/copy-thumbprint.png)

7. Sobre o **configurar a tela** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-canvas-single-sign-on"></a>Configurar a tela de logon único

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa tela como administrador.

2. Aceda a **cursos \> contas geridas \> Microsoft**.

    ![Canvas](./media/canvas-lms-tutorial/ic775990.png "Canvas")

3. No painel de navegação à esquerda, selecione **autenticação**e, em seguida, clique em **Adicionar nova configuração de SAML**.

    ![Autenticação](./media/canvas-lms-tutorial/ic775991.png "autenticação")

4. Na página de integração atual, execute os seguintes passos:

    ![Integração atual](./media/canvas-lms-tutorial/ic775992.png "integração atual")

    a. Na **ID de entidade do IdP** caixa de texto, cole o valor de **Azure Ad identificador** que copiou do portal do Azure.

    b. Na **URL de registo** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.

    c. Na **URL de fim de registo** caixa de texto, cole o valor de **URL de fim de sessão** que copiou do portal do Azure.

    d. Na **ligação de palavra-passe de alteração** caixa de texto, cole o valor de **alterar o URL da palavra-passe** que copiou do portal do Azure.

    e. Na **impressão digital de certificado** caixa de texto, colar a **Thumbprint** valor do certificado que copiou do portal do Azure.

    f. Partir do **atributo de início de sessão** lista, selecione **NameID**.

    g. Partir do **formato de identificador** lista, selecione **emailAddress**.

    h. Clique em **guardar as definições de autenticação**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso à tela.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **tela**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **tela**.

    ![A ligação de tela na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-canvas-test-user"></a>Criar utilizador de teste baseada em telas

Para ativar a utilizadores do Azure AD iniciar sessão na tela, tem de ser aprovisionados para tela. No caso de tela, o aprovisionamento de utilizadores é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **tela** inquilino.

2. Aceda a **cursos \> contas geridas \> Microsoft**.

   ![Canvas](./media/canvas-lms-tutorial/ic775990.png "Canvas")

3. Clique em **Utilizadores**.

   ![Os utilizadores](./media/canvas-lms-tutorial/ic775995.png "utilizadores")

4. Clique em **adicionar novo utilizador**.

   ![Os utilizadores](./media/canvas-lms-tutorial/ic775996.png "utilizadores")

5. Em Adicionar uma página de diálogo do novo utilizador, execute os seguintes passos:

   ![Adicionar utilizador](./media/canvas-lms-tutorial/ic775997.png "adicionar utilizador")

   a. Na **FullName** caixa de texto, introduza o nome de utilizador, como **BrittaSimon**.

   b. Na **E-Mail** caixa de texto, introduza o e-mail do utilizador, como **brittasimon\@contoso.com**.

   c. Na **início de sessão** caixa de texto, introduza o endereço de e-mail do utilizador do Azure AD, como **brittasimon\@contoso.com**.

   d. Selecione **o utilizador sobre a criação de conta de E-Mail**.

   e. Clique em **adicionar utilizador**.

> [!NOTE]
> Pode usar qualquer outras tela utilizador conta ferramentas de criação ou APIs fornecidos pela tela, para aprovisionar contas de utilizador do AAD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de tela no painel de acesso, deve ser automaticamente conectado para a tela para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

