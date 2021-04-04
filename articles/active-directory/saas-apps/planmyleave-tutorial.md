---
title: 'Tutorial: Integração do Diretório Ativo Azure com o PlanMyLeave | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o PlanMyLeave.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: c143a5af40097039c70c58adba4cb21f687d3fa6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92893187"
---
# <a name="tutorial-azure-active-directory-integration-with-planmyleave"></a>Tutorial: Integração do Diretório Ativo Azure com o PlanMyLeave

Neste tutorial, aprende-se a integrar o PlanMyLeave com o Azure Ative Directory (Azure AD).
A integração do PlanMyLeave com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao PlanMyLeave.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no PlanMyLeave (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o PlanMyLeave, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por PlanMyLeave

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* PlanMyLeave suporta **SSO** iniciado sp

* PlanMyLeave suporta **provisão de** utilizadores just in time

## <a name="adding-planmyleave-from-the-gallery"></a>Adicionar PlanMyLeave da galeria

Para configurar a integração do PlanMyLeave no AD Azure, é necessário adicionar o PlanMyLeave da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar PlanMyLeave da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **PlanMyLeave**, selecione **PlanMyLeave** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![PlanMyLeave na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o PlanMyLeave com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de saúde a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no PlanMyLeave.

Para configurar e testar o Azure AD com o PlanMyLeave, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure PlanMyLeave Single Sign-On](#configure-planmyleave-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create PlanMyLeave test user](#create-planmyleave-test-user)** - para ter uma contraparte de Britta Simon em PlanMyLeave que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o PlanMyLeave, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **PlanMyLeave,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![PlanMyLeave Domain e URLs informações únicas de súbdilho](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<company-name>.planmyleave.com/Login.aspx`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<company-name>.planmyleave.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente PlanMyLeave](mailto:support@planmyleave.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configurar PlanMyLeave,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-planmyleave-single-sign-on"></a>Configurar planMyLeave single Sign-On

1. Numa janela diferente do navegador web, inicie sessão no seu inquilino PlanMyLeave como administrador.

2. Ir para a **Configuração do Sistema**. Em seguida, na secção **de Gestão de Segurança** clique nas **definições de SAML da Empresa** .

    ![Screenshot que mostra a página "Configuração do Sistema" com a secção "Gestão de Segurança" realçada e a ação "Company S A M L Settings" selecionada.](./media/planmyleave-tutorial/tutorial_planmyleave_002.png) 

3. Na secção **Definições SAML,** clique no ícone do editor.

    ![Screenshot que mostra a secção "S A M L Settings" com o ícone "editor" selecionado no topo direito da secção.](./media/planmyleave-tutorial/tutorial_planmyleave_003.png)

4. Na secção **'Definições SAML' de atualização,** execute os seguintes passos:

    ![Configurar Sign-On única no lado da aplicação](./media/planmyleave-tutorial/tutorial_planmyleave_004.png)

    a.  Na caixa de texto **do URL de login,** cole URL de **login** que copiou do portal Azure.

    b.  Abra os metadados descarregados, copie o valor **X509Certificate** e, em seguida, cole-os na caixa de texto **certificate.**

    c. Set "**Is Enable**" to "**Yes**".

    d. Clique em **Guardar**. 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador****brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon utilize a Azure single sign-on, concedendo acesso ao PlanMyLeave.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **PlanMyLeave**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **PlanMyLeave**.

    ![O link PlanMyLeave na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-planmyleave-test-user"></a>Criar utilizador de teste PlanMyLeave

Nesta secção, um utilizador chamado Britta Simon é criado no PlanMyLeave. O PlanMyLeave suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no PlanMyLeave, um novo é criado após a autenticação.

> [!NOTE]
> Se precisar de criar um utilizador manualmente, tem de contactar a equipa de [suporte do PlanMyLeave.](mailto:support@planmyleave.com)

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo PlanMyLeave no Painel de Acesso, deverá ser automaticamente inscrito no PlanMyLeave para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)