---
title: 'Tutorial: Integração do Active Directory do Azure com Rollbar | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Rollbar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57537e54-9388-4272-a610-805ce45a451f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/15/2019
ms.author: jeedes
ms.openlocfilehash: 7f6e6b262dccd5b68c5a55c3d96a894205df8933
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64695894"
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Tutorial: Integração do Active Directory do Azure com Rollbar

Neste tutorial, saiba como integrar Rollbar com o Azure Active Directory (Azure AD).
Integrar Rollbar no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Rollbar.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Rollbar (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Rollbar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Rollbar logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Rollbar **SP e IDP** iniciada SSO

## <a name="adding-rollbar-from-the-gallery"></a>Adicionando Rollbar da Galeria

Para configurar a integração do Rollbar com o Azure AD, terá de adicionar Rollbar a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Rollbar a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Rollbar**, selecione **Rollbar** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Rollbar na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Rollbar com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Rollbar deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Rollbar, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Rollbar Single Sign-On](#configure-rollbar-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Rollbar](#create-rollbar-test-user)**  - para ter um equivalente da Eduarda Almeida na Rollbar que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Rollbar, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Rollbar** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Rollbar domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva o URL: `https://saml.rollbar.com`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://rollbar.com/<accountname>/saml/sso/azure/`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Rollbar domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://rollbar.com/<accountname>/saml/login/azure/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de resposta e o URL de início de sessão real. Contacte [equipa de suporte de cliente Rollbar](mailto:support@rollbar.com) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

7. Sobre o **configurar Rollbar** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-rollbar-single-sign-on"></a>Configurar Rollbar Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Rollbar como um administrador.

1. Clique nas **definições de perfil** no canto superior direito e clique em **definições de nome de conta**.

    ![Configuração](./media/rollbar-tutorial/general.png)

1. Clique em **fornecedor de identidade** em segurança.

    ![Configuração](./media/rollbar-tutorial/configure1.png)

1. Na **fornecedor de identidade de SAML** secção, execute os seguintes passos:

    ![Configuração](./media/rollbar-tutorial/configure2.png)

    a. Selecione **AZURE** partir do **fornecedor de identidade de SAML** lista pendente.

    b. A abrir o ficheiro de metadados no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **metadados de SAML** caixa de texto.

    c. Clique em **Guardar**.

1. Depois de clicar em salvar botão, a tela será semelhante a esta:

    ![Configuração](./media/rollbar-tutorial/configure3.png)

    > [!NOTE]
    > Para concluir o passo seguinte, primeiro tem de adicionar-se como um utilizador para a aplicação de Rollbar no Azure.
    >

    a. Se quiser exigir que todos os utilizadores autenticar através do Azure, em seguida, clique em **iniciar sessão através do seu fornecedor de identidade** novamente à autenticação através do Azure.  

    b.  Assim que forem retornados na tela, selecione o **requerem o início de sessão através do fornecedor de identidade de SAML** caixa de verificação.

    b. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo `brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Rollbar.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Rollbar**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Rollbar**.

    ![A ligação de Rollbar na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-rollbar-test-user"></a>Criar utilizador de teste Rollbar

Para ativar a utilizadores do Azure AD iniciar sessão no Rollbar, tem de ser aprovisionados em Rollbar. No caso de Rollbar, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa Rollbar como um administrador.

1. Clique nas **definições de perfil** no canto superior direito e clique em **definições de nome de conta**.

    ![Utilizador](./media/rollbar-tutorial/general.png)

1. Clique em **Utilizadores**.

    ![Adicionar o funcionário](./media/rollbar-tutorial/user1.png)

1. Clique em **convidar os membros da Equipe**.

    ![Convidar pessoas](./media/rollbar-tutorial/user2.png)

1. Na caixa de texto, introduza o nome de utilizador, como **brittasimon\@contoso.com** e clique em **Add/convite**.

    ![Convidar pessoas](./media/rollbar-tutorial/user3.png)

1. Utilizador recebe um convite e depois de aceitar ele criado no sistema.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Rollbar no painel de acesso, deve ser automaticamente sessão iniciada no Rollbar para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

