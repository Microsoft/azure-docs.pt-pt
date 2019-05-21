---
title: 'Tutorial: Integração do Active Directory do Azure com o UserVoice | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e UserVoice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: dbd7189b1761a9ea88ce32dae3d7b45a88301ff6
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65905628"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Tutorial: Integração do Active Directory do Azure com o UserVoice

Neste tutorial, saiba como integrar o UserVoice com o Azure Active Directory (Azure AD).
Integrar o UserVoice com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao UserVoice.
* Pode permitir que os utilizadores ser automaticamente iniciada no UserVoice (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o UserVoice, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* UserVoice logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta do UserVoice **SP** iniciada SSO

## <a name="adding-uservoice-from-the-gallery"></a>Adicionando o UserVoice da Galeria

Para configurar a integração do UserVoice para o Azure AD, terá de adicionar UserVoice a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar UserVoice a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **UserVoice**, selecione **UserVoice** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![UserVoice na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o UserVoice com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no UserVoice deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o UserVoice, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o UserVoice Single Sign-On](#configure-uservoice-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do UserVoice](#create-uservoice-test-user)**  - para ter um equivalente da Eduarda Almeida no UserVoice que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o UserVoice, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **UserVoice** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![UserVoice do domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<tenantname>.UserVoice.com`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<tenantname>.UserVoice.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente de UserVoice](https://www.uservoice.com/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Na **certificado de assinatura SAML** secção, clique em **editar** botão para abrir **certificado de assinatura SAML** caixa de diálogo.

    ![Editar o certificado de assinatura de SAML](common/edit-certificate.png)

6. Na **certificado de assinatura SAML** secção, copie a **Thumbprint** e guarde-o no seu computador.

    ![Copie o valor do Thumbprint](common/copy-thumbprint.png)

7. Sobre o **configurar UserVoice** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-uservoice-single-sign-on"></a>Configurar o logon único de UserVoice

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa UserVoice como um administrador.

2. Na barra de ferramentas na parte superior, clique em **configurações**e, em seguida, selecione **Web portal** no menu.
   
    ![Secção de definições no lado de aplicação](./media/uservoice-tutorial/ic777519.png "definições")

3. Sobre o **Web portal** separador o **autenticação de utilizador** secção, clique em **editar** para abrir o **editar autenticação de utilizador** página de diálogo.
   
    ![Portal Web do guia](./media/uservoice-tutorial/ic777520.png "Web portal")

4. Sobre o **editar autenticação de utilizador** caixa de diálogo página, execute os seguintes passos:
   
    ![Editar autenticação de utilizador](./media/uservoice-tutorial/ic777521.png "editar autenticação de utilizador")
   
    a. Clique em **início de sessão único (SSO)**.
 
    b. Colar o **URL de início de sessão** valor, que copiou do portal do Azure para o **SSO remoto início de sessão** caixa de texto.

    c. Colar o **URL de fim de sessão** valor, que copiou do portal do Azure para o **caixa de texto de fim de sessão remoto SSO**.
 
    d. Colar o **Thumbprint** valor, que copiou do portal do Azure para o **impressão digital de certificado SHA1 atual** caixa de texto.
    
    e. Clique em **guardar as definições de autenticação**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao UserVoice.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **UserVoice**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **UserVoice**.

    ![A ligação de UserVoice na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-uservoice-test-user"></a>Criar utilizador de teste de UserVoice

Para ativar a utilizadores do Azure AD iniciar sessão no UserVoice, tem de ser aprovisionados no UserVoice. No caso do UserVoice, aprovisionamento é uma tarefa manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para Aprovisionar uma conta de utilizador, execute os seguintes passos:

1. Inicie sessão no seu **UserVoice** inquilino.

2. Aceda a **definições**.
   
    ![As definições](./media/uservoice-tutorial/ic777811.png "definições")

3. Clique em **gerais**.

4. Clique em **agentes e permissões**.
   
    ![Permissões e agentes](./media/uservoice-tutorial/ic777812.png "agentes e permissões")

5. Clique em **adicionar administradores**.
   
    ![Adicionar administradores](./media/uservoice-tutorial/ic777813.png "adicionar administradores")

6. Sobre o **convidar administradores** caixa de diálogo, execute os seguintes passos:
   
    ![Convidar admins](./media/uservoice-tutorial/ic777814.png "convidar os administradores")
   
    a. Na caixa de texto de mensagens de correio eletrónico, escreva o endereço de e-mail da conta que pretende aprovisionar e, em seguida, clique em **adicionar**.
   
    b. Clique em **convidar**.

> [!NOTE]
> Pode utilizar quaisquer outras UserVoice utilizador conta criação ferramentas ou APIs fornecidas pelo UserVoice para aprovisionar contas de utilizador do AAD.

### <a name="test-single-sign-on"></a>Testar início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico do UserVoice no painel de acesso, deve ser automaticamente sessão iniciada no UserVoice para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

