---
title: 'Tutorial: Integração do Active Directory do Azure com Teamphoria | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Teamphoria.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 11d0a0a67d1ce5049166cab3d9ca3e4903b6b460
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277209"
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>Tutorial: Integração do Active Directory do Azure com Teamphoria

Neste tutorial, saiba como integrar Teamphoria com o Azure Active Directory (Azure AD).
Integrar Teamphoria no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Teamphoria.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Teamphoria (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Teamphoria, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Teamphoria logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Teamphoria **SP** iniciada SSO

## <a name="adding-teamphoria-from-the-gallery"></a>Adicionando Teamphoria da Galeria

Para configurar a integração do Teamphoria com o Azure AD, terá de adicionar Teamphoria a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Teamphoria a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Teamphoria**, selecione **Teamphoria** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Teamphoria na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Teamphoria com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Teamphoria deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Teamphoria, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Teamphoria Single Sign-On](#configure-teamphoria-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Teamphoria](#create-teamphoria-test-user)**  - para ter um equivalente da Eduarda Almeida na Teamphoria que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Teamphoria, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Teamphoria** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Teamphoria domínio e URLs únicas início de sessão em informações](common/sp-intiated.png)

    Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão:  `https://<sub-domain>.teamphoria.com/login`

    > [!NOTE]
    > O valor de URL de início de sessão não é real. Terá de atualizar este valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente Teamphoria](https://www.teamphoria.com/) para obter o URL de início de sessão. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar Teamphoria** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-teamphoria-single-sign-on"></a>Configurar Teamphoria Single Sign-On

1. Para configurar o início de sessão único num **Teamphoria** lado, início de sessão para a sua aplicação Teamphoria como administrador.

1. Aceda a **definições de administrador** opção na barra de ferramentas da lado esquerda e, no separador configurar, clique em **início de sessão único** para abrir a janela de configuração de SSO.

    ![Configurar o início de sessão único](./media/teamphoria-tutorial/admin_sso_configure.png)

1. Clique em **adicionar novo fornecedor de identidade** opção no canto superior direito para abrir o formulário para adicionar as definições de SSO.

    ![Configurar o início de sessão único](./media/teamphoria-tutorial/add_new_identity_provider.png)

1. Introduza os detalhes nos campos, conforme descrito abaixo-

    ![Configurar o início de sessão único](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **NOME A APRESENTAR**: Introduza o nome a apresentar do plug-in na página de administração.

    b. **NOME DO BOTÃO**: O nome do separador que será apresentado na página de início de sessão para iniciar sessão através do SSO.

    c. **CERTIFICADO**: Abra o certificado transferido anteriormente a partir do portal do Azure no bloco de notas, copie o conteúdo do mesmo e cole-o aqui na caixa.

    d. **PONTO DE ENTRADA**: Colar o **URL de início de sessão** copiou anteriormente a partir do portal do Azure.

    e. Mude a opção para **ON** e clique em **guardar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Teamphoria.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Teamphoria**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Teamphoria**.

    ![A ligação de Teamphoria na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-teamphoria-test-user"></a>Criar utilizador de teste Teamphoria

Para habilitar os utilizadores do Azure AD iniciar sessão no Teamphoria, tem de ser aprovisionados em Teamphoria. No caso de Teamphoria, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa Teamphoria como um administrador.

1. Clique em **administrador** definições na barra de ferramentas à esquerda e, no **GERIR** separador clique em **utilizadores** para abrir a página de administração para os utilizadores.

    ![Adicionar o funcionário](./media/teamphoria-tutorial/admin_manage_users.png)

1. Clique nas **MANUAL CONVIDAR** opção.

    ![Convidar pessoas](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. Nesta página, realize após a ação.

    ![Convidar pessoas](./media/teamphoria-tutorial/manual_user_invite.png)

    a. Na **endereço de E-Mail** caixa de texto, introduza o **endereço de e-mail** do utilizador, como BrittaSimon.

    b. Na **nome próprio** caixa de texto, introduza o nome próprio do utilizador, como **Eduarda**.

    c. Na **SOBRENOME** caixa de texto, introduza o apelido do utilizador, como **Simon**.

    d. Clique em **convite 1 utilizador**. Utilizador tem de aceitar o convite a ser criados no sistema.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Teamphoria no painel de acesso, deve ser automaticamente sessão iniciada no Teamphoria para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

