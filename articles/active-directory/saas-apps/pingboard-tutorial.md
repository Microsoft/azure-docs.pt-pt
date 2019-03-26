---
title: 'Tutorial: Integração do Active Directory do Azure com Pingboard | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Pingboard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 45b542cb644666a6bf1df3dc76cbf54693330810
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438908"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Tutorial: Integração do Active Directory do Azure com Pingboard

Neste tutorial, saiba como integrar Pingboard com o Azure Active Directory (Azure AD).
Integrar Pingboard no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Pingboard.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Pingboard (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Pingboard, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Pingboard logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Pingboard **SP** e **IDP** iniciada SSO

* Suporta Pingboard [automatizada de aprovisionamento de utilizador](https://docs.microsoft.com/azure/active-directory/saas-apps/pingboard-provisioning-tutorial) 

## <a name="adding-pingboard-from-the-gallery"></a>Adicionando Pingboard da Galeria

Para configurar a integração do Pingboard com o Azure AD, terá de adicionar Pingboard a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Pingboard a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Pingboard**, selecione **Pingboard** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Pingboard na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Pingboard com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Pingboard deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Pingboard, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Pingboard Single Sign-On](#configure-pingboard-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Pingboard](#create-pingboard-test-user)**  - para ter um equivalente da Eduarda Almeida na Pingboard que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Pingboard, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Pingboard** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Pingboard domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL: `http://app.pingboard.com/sp`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<entity-id>.pingboard.com/auth/saml/consume`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Pingboard domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e URL de resposta real. Contacte [equipa de suporte de cliente Pingboard](https://support.pingboard.com/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

7. Sobre o **configurar Pingboard** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-pingboard-single-sign-on"></a>Configurar Pingboard Single Sign-On

1. Para configurar o SSO no lado de Pingboard, abra uma nova janela do browser e inicie sessão na sua conta de Pingboard. Tem de ser um administrador de Pingboard para configurar o início de sessão único.

2. No menu superior, selecione **aplicações > integrações**

    ![Configurar o início de sessão único](./media/pingboard-tutorial/Pingboard_integration.png)

3. Sobre o **integrações** página, encontre o **"Azure Active Directory"** mosaico e clique no mesmo.

    ![Integração de início de sessão único de Pingboard](./media/pingboard-tutorial/Pingboard_aad.png)

4. No modal que se segue clique **"Configurar"**

    ![Botão de configuração de Pingboard](./media/pingboard-tutorial/Pingboard_configure.png)

5. Na página seguinte, repare que "integração de SSO do Azure está ativada". Abra o arquivo XML de metadados baixado num bloco de notas e cole o conteúdo **metadados de IDP**.

    ![Ecrã de configuração de Pingboard SSO](./media/pingboard-tutorial/Pingboard_sso_configure.png)

6. O ficheiro é validado, e se tudo estiver correto, início de sessão único irá agora ser ativada.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Pingboard.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Pingboard**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Pingboard**.

    ![A ligação de Pingboard na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-pingboard-test-user"></a>Criar utilizador de teste Pingboard

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no Pingboard. Pingboard suporta o aprovisionamento automático de utilizadores, que está por predefinição, ativada. Pode encontrar mais detalhes [aqui](pingboard-provisioning-tutorial.md) sobre como configurar o aprovisionamento automático de utilizadores.

**Se precisar de criar manualmente o utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa Pingboard como um administrador.

2. Clique em **"Adicionar Employee"** botão **Directory** página.

    ![Adicionar o funcionário](./media/pingboard-tutorial/create_testuser_add.png)

3. Sobre o **"Adicionar Employee"** caixa de diálogo página, execute os seguintes passos:

    ![Convidar pessoas](./media/pingboard-tutorial/create_testuser_name.png)

    a. Na **FullName** caixa de texto, como o tipo o nome completo do utilizador **Eduarda Almeida**.

    b. Na **E-Mail** caixa de texto, como o tipo de endereço de e-mail do utilizador **brittasimon@contoso.com**.

    c. Na **cargo** caixa de texto, escreva o cargo da Eduarda Almeida.

    d. Na **localização** lista pendente, selecione a localização da Eduarda Almeida.

    e. Clique em **Adicionar**.

4. Um ecrã de confirmação é exibido para confirmar a adição do utilizador.

    ![Confirmar](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > O titular da conta do Azure Active Directory recebe uma mensagem de e-mail e segue uma ligação para confirmar a respetiva conta até se tornar Active Directory.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Pingboard no painel de acesso, deve ser automaticamente sessão iniciada no Pingboard para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar o aprovisionamento do utilizador](https://docs.microsoft.com/azure/active-directory/saas-apps/pingboard-provisioning-tutorial)
