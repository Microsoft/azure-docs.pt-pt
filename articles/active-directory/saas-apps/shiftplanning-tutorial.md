---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Humanidade | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Humanity.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: e1099f271291c6d86610237454e08e06b5fe48c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522166"
---
# <a name="tutorial-azure-active-directory-integration-with-humanity"></a>Tutorial: Azure Ative Directy integração com a Humanidade

Neste tutorial, aprende-se a integrar a Humanidade com o Azure Ative Directory (Azure AD).
Integrar a Humanidade com a AD Azure proporciona-lhe os seguintes benefícios:

* Podes controlar em Azure AD que tem acesso à Humanidade.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Humanity (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Humanidade, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada única da humanidade

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Humanidade apoia **SP** iniciado SSO

## <a name="adding-humanity-from-the-gallery"></a>Adicionando humanidade da galeria

Para configurar a integração da Humanidade em Azure AD, você precisa adicionar a Humanidade da galeria à sua lista de aplicações geridas saaS.

**Para adicionar a Humanidade da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva Humanity**, selecione **Humanidade** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![Humanidade na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on with Humanity com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Humanidade.

Para configurar e testar o Azure AD com a Humanidade, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único da Humanidade](#configure-humanity-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Humanity test user](#create-humanity-test-user)** - ter uma contrapartida de Britta Simon in Humanity que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar a Azure AD um único sign-on com a Humanidade, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **humanity,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Domínio da humanidade e URLs informações únicas de súmis](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://company.humanity.com/includes/saml/`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://company.humanity.com/app/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte a [equipa de apoio ao Cliente da Humanidade](https://www.humanity.com/support/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar a Humanidade,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-humanity-single-sign-on"></a>Configurar a humanidade Sign-On única

1. Numa janela diferente do navegador web, inicie sessão no site da empresa **Humanity** como administrador.

2. No menu em cima, clique em **Administração.**

    ![Administração](./media/shiftplanning-tutorial/iC786619.png "Administrador")
3. Em **Integração**, clique **em 'Sign-On' único**.

    ![O Screenshot mostra single Sign-On selecionados a partir do menu Integração.](./media/shiftplanning-tutorial/iC786620.png "Início de Sessão Único")

4. Na secção **'Sign-On' único,** execute os seguintes passos:

    ![A screenshot mostra a secção single Sign-On onde pode introduzir os valores descritos.](./media/shiftplanning-tutorial/iC786905.png "Início de Sessão Único")

    a. Selecione **SAML Ativado**.

    b. Selecione **Permitir o início da palavra-passe**.

    c. Na caixa de texto **URL DO EMITENTE SAML,** cole o valor URL de **login,** que copiou do portal Azure.

    d. Na caixa de texto **url de logout remoto,** cole o valor **URL logout,** que copiou do portal Azure.

    e. Abra o certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **do Certificado X.509.**

    f. Clique **em Guardar Definições**.

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso à Humanidade.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **Humanity**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Humanity**.

    ![O link humanidade na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-humanity-test-user"></a>Criar utilizador de teste da Humanidade

Para permitir que os utilizadores de Azure AD acedam à Humanidade, devem ser adsejados na Humanidade. No caso da Humanidade, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Faça login no site da empresa **Humanity** como administrador.

2. Clique **em Administração.**

    ![Administração](./media/shiftplanning-tutorial/iC786619.png "Administrador")

3. Clique **em Staff**.

    ![Pessoal](./media/shiftplanning-tutorial/ic786623.png "Pessoal")

4. Em **Ações**, clique **em Adicionar Empregados.**

    ![Adicionar colaboradores](./media/shiftplanning-tutorial/iC786624.png "Adicionar colaboradores")

5. Na secção **Adicionar Colaboradores,** execute os seguintes passos:

    ![Salvar funcionários](./media/shiftplanning-tutorial/iC786625.png "Salvar funcionários")

    a. Digite o **Primeiro Nome,** **Apelido** e **E-mail** de uma conta AD válida Azure que pretende apresentar nas caixas de texto relacionadas.

    b. Clique **em Salvar Funcionários.**

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador da Humanidade ou APIs fornecidas pela Humanity para fornecer contas de utilizador Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo da Humanidade no Painel de Acesso, deverá ser automaticamente inscrito na Humanidade para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)