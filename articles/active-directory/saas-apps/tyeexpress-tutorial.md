---
title: 'Tutorial: Integração do Azure Ative Directory com T&E Express [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o T&E Express.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: f44a67b4943c6897f82aedf47d493b36e5e2d3b9
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521898"
---
# <a name="tutorial-azure-active-directory-integration-with-te-express"></a>Tutorial: Integração do Diretório Ativo Azure com T&E Express

Neste tutorial, aprende-se a integrar o T&E Express com o Azure Ative Directory (Azure AD).
Integrar a T&E Express com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a T&E Express.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no T&E Express (Sign-on Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com t&E Express, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* T&assinatura única ativada do E Express

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* T&E Express apoia **IDP** iniciado SSO

## <a name="adding-te-express-from-the-gallery"></a>Adicionando T&E Express da galeria

Para configurar a integração do T&E Express em AZure AD, é necessário adicionar T&E Express da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar T&E Express da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, o tipo **T&E Express**, selecione T&E **Express** do painel de resultados e, em seguida, clique em Adicionar o botão **Adicionar** a aplicação.

    ![T&E Express na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com T&E Express com base num utilizador de teste chamado **Britta Simon**.
Para um único início de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em T&E Express.

Para configurar e testar o Azure AD com T&E Express, tem de completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure T&E Express Single Sign-On](#configure-te-express-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create T&utilizador de teste E Express](#create-te-express-test-user)** - para ter uma contraparte de Britta Simon em T&E Express que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com T&E Express, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **T&E Express,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na **configuração single Sign-On com página SAML,** execute os seguintes passos:

    ![T&E Express Domain e URLs informações únicas de início de súmito](common/idp-intiated.png)

    a. Na caixa de texto **identifier,** digite o valor como URL utilizando o seguinte padrão: `https://<domain>.tyeexpress.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<domain>.tyeexpress.com/authorize/samlConsume.aspx`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Aqui sugerimos que use o valor único da corda no Identificador. Contacte [a equipa de suporte&E Express Para](https://www.tyeexpress.com/contacto.aspx) obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configuração T&E Express,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-te-express-single-sign-on"></a>Configurar T&E Express Single Sign-On

1. Para configurar um único sinal de entrada em T&lado **E Express,** faça login na aplicação T&E express sem um único sinal DE SAML na utilização de credenciais de administração.

1. No separador **Admin,** clique no **domínio SAML** para abrir a página de definições SAML.

    ![A screenshot mostra o domínio SAML selecionado a partir do menu Dedmin.](./media/tyeexpress-tutorial/tye-SAML.png)

1. Selecione a opção **Ativar (Ativar)** de **Não** a **SI(Sim)**. Na caixa de texto **do Fornecedor de Identidade Metadata,** cole os metadados XML que descarregou a partir do portal Azure.

    ![A screenshot mostra a página Dominio SAML onde pode introduzir os metadados.](./media/tyeexpress-tutorial/tyeAdmin.png)

1. Clique no botão **Guardar (Guardar)** para guardar as definições.

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

Nesta secção, você permite que Britta Simon utilize o Azure single sign-on, permitindo o acesso a T&E Express.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **T&E Express**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **T&E Express**.

    ![O link T&E Express na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-te-express-test-user"></a>Criar T&utilizador de teste E Express

Para permitir que os utilizadores da Azure AD acedam ao T&E Express, devem ser adusidos em T&E Express. No caso de T&E Express, o provisionamento é uma tarefa manual.

**Para a disponibilização de uma conta de utilizador, execute os seguintes passos:**

1. Faça login no seu site da empresa T&E Express como administrador.

1. Na etiqueta Admin, clique nos Utilizadores para abrir a página principal dos Utilizadores.

    ![A screenshot mostra os utilizadores selecionados a partir do menu Dedmin.](./media/tyeexpress-tutorial/tye-adminusers.png)

1. Na página inicial, clique **+** para adicionar os utilizadores.

    ![A screenshot mostra o ícone plus para adicionar utilizadores.](./media/tyeexpress-tutorial/tye-usershome.png)

1. Introduza todos os detalhes obrigatórios como solicitado no formulário e clique no botão de guardar para guardar os detalhes.

    ![A screenshot mostra a secção de informações do Utilizador onde pode introduzir valores apropriados.](./media/tyeexpress-tutorial/tye-usersadd.png)

    ![A screenshot mostra as secções Approvers e Assistant onde pode introduzir valores apropriados.](./media/tyeexpress-tutorial/tye-userssave.png)

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo T&E Express no Painel de Acesso, deverá ser automaticamente inscrito no T&E Express para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)