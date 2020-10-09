---
title: 'Tutorial: Integração do Azure Ative Directory com Tela / Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Canvas.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: jeedes
ms.openlocfilehash: 6c75103f74ee648c46b805df9495bc668ef4f5bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761571"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas"></a>Tutorial: Integração do Diretório Ativo Azure com Tela

Neste tutorial, aprende-se a integrar a Tela com o Azure Ative Directory (Azure AD).
A integração da Tela com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Canvas.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Tela (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com a Tela, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição única ativada por insusição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Lona suporta **SSO** iniciado SP

## <a name="adding-canvas-from-the-gallery"></a>Adicionando tela da galeria

Para configurar a integração do Canvas em Azure AD, precisa adicionar Canvas da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Tela da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Tela**, selecione **Tela** do painel de resultados e, em seguida, clique em Adicionar o botão **Adicionar** a aplicação.

     ![Tela na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com uma única placatura com tela baseada num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Canvas.

Para configurar e testar o Azure AD com uma única placatura com tela, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure a tela sign-on única](#configure-canvas-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Canvas test user](#create-canvas-test-user)** - para ter uma contraparte de Britta Simon em Tela que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com tela, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Canvas,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Informações de súmis de domínio de lona e URLs únicas](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<tenant-name>.instructure.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente da Canvas](https://community.canvaslms.com/community/help) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na secção **Certificado de Assinatura SAML,** clique em Editar o botão **Editar** para abrir o diálogo **do Certificado de Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

6. Na secção **Certificado de Assinatura SAML,** copie a **IMPRESSÃO DIGITAL** e guarde-a no seu computador.

    ![Valor da impressão digital do polegar da cópia](common/copy-thumbprint.png)

7. Na secção **Configuração de Tela,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-canvas-single-sign-on"></a>Configurar tela única Sign-On

1. Numa janela diferente do navegador web, inicie sessão no site da empresa Denação como administrador.

2. Ir a **Cursos \> Contas Geridas \> Microsoft**.

    ![Telas](./media/canvas-lms-tutorial/ic775990.png "Telas")

3. No painel de navegação à esquerda, selecione **Autenticação**e, em seguida, clique em **Adicionar Novo SAML Config**.

    ![Autenticação](./media/canvas-lms-tutorial/ic775991.png "Autenticação")

4. Na página de Integração Atual, execute os seguintes passos:

    ![Integração Atual](./media/canvas-lms-tutorial/ic775992.png "Integração Atual")

    a. Na caixa de texto **IdP Entity ID,** cole o valor do **Identificador Ad Azure** que copiou do portal Azure.

    b. Na caixa de texto **URL,** cole o valor do URL de **login** que copiou do portal Azure .

    c. Na caixa de texto **URL log out,** cole o valor do **URL logout** que copiou do portal Azure.

    d. Na caixa de texto **Change Password Link,** cole o valor do URL de **palavra-passe** de alteração que copiou do portal Azure.

    e. Na caixa de texto **de impressão digital de certificado,** cole o valor de impressão **digital** do certificado que copiou do portal Azure.

    f. Na lista **de Atributos de Início** de Sessão, selecione **NameID**.

    exemplo, Na lista de **Identificação Formato,** selecione **emailAddress**.

    h. Clique **em Guardar Definições de Autenticação**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador** **brittasimon \@ yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a Canvas.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Canvas**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Canvas**.

    ![O link de tela na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-canvas-test-user"></a>Criar utilizador de teste de tela

Para permitir que os utilizadores de Azure AD iniciem sessão na Tela, devem ser a provisionados na Tela. No caso da Tela, o fornecimento do utilizador é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Faça login no seu inquilino **de Lona.**

2. Ir a **Cursos \> Contas Geridas \> Microsoft**.

   ![Telas](./media/canvas-lms-tutorial/ic775990.png "Telas")

3. Clique em **Utilizadores**.

   ![O Screenshot mostra o menu de Canvas com os Utilizadores selecionados.](./media/canvas-lms-tutorial/ic775995.png "Utilizadores")

4. Clique **em Adicionar Novo Utilizador**.

   ![A screenshot mostra o adicionar um novo botão de utilizador.](./media/canvas-lms-tutorial/ic775996.png "Utilizadores")

5. Na página de diálogo Adicionar um novo utilizador, execute os seguintes passos:

   ![Adicionar utilizador](./media/canvas-lms-tutorial/ic775997.png "Adicionar Utilizador")

   a. Na caixa de texto **'Nome Completo',** insira o nome de utilizador como **BrittaSimon**.

   b. Na caixa de sms **do Email,** introduza o e-mail do utilizador como **o \@ brittasimon contoso.com**.

   c. Na caixa de texto **do Login,** insira o endereço de e-mail Azure AD do utilizador como ** \@ brittasimon contoso.com**.

   d. Selecione **Enviar por email o utilizador sobre esta criação de conta.**

   e. Clique em **Adicionar Utilizador**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador canvas ou APIs fornecidas pela Canvas para fornecer contas de utilizador Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo de Tela no Painel de Acesso, deverá ser automaticamente inscrito na Tela para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

