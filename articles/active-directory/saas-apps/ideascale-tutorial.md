---
title: 'Tutorial: Integração do Azure Ative Directory com ideaScale | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o IdeaScale.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.openlocfilehash: ef04bf9f6d9ca6ff5089d2826c05a5acfb759b04
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001665"
---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Tutorial: Integração do Azure Ative Directory com IdeaScale

Neste tutorial, aprende-se a integrar o IdeaScale com o Azure Ative Directory (Azure AD).
A integração do IdeaScale com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso ao IdeaScale.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no IdeaScale (Sign-on único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD do Azure com o IdeaScale, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição única ativada ideaScale

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* IdeaScale suporta **SSO** iniciado sp

## <a name="adding-ideascale-from-the-gallery"></a>Adicionar IdeaScale da galeria

Para configurar a integração do IdeaScale no AD Azure, é necessário adicionar ideaScale da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar IdeaScale na galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **IdeaScale,** selecione **IdeaScale** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![IdeaScale na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com IdeaScale baseado num utilizador de teste chamado **Britta Simon**.
Para um único s-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no IdeaScale.

Para configurar e testar o Azure AD com o IdeaScale, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configurar o Sinal Único de Escala de Idea -](#configure-ideascale-single-sign-on)** para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Crie o utilizador de teste IdeaScale](#create-ideascale-test-user)** - para ter uma contraparte de Britta Simon em IdeaScale que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com ideaScale, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **IdeaScale,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![IdeaScale Domain e URLs informações únicas de súmis](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<companyname>.ideascale.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão:
    
    ```http
    http://<companyname>.ideascale.com
    https://<companyname>.ideascale.com
    ```

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente IdeaScale](https://support.ideascale.com/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configurar IdeaScale,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-ideascale-single-sign-on"></a>Configurar Sign-On única de escala de idea

1. Numa janela diferente do navegador web, inicie sessão no site da empresa IdeaScale como administrador.

2. Vá para **as Configurações Comunitárias**.

    ![Configurações comunitárias](./media/ideascale-tutorial/ic790847.png "Configurações comunitárias")

3. Aceda às **\> definições de sinalização única de segurança**.

    ![A screenshot mostra definições de signon única selecionadas a partir do menu De segurança.](./media/ideascale-tutorial/ic790848.png "Definições de signon único")

4. Como **Tipo Single-Signon**, selecione **SAML 2.0**.

    ![Tipo signon único](./media/ideascale-tutorial/ic790849.png "Tipo signon único")

5. No **diálogo de definições de sinalização única,** execute os seguintes passos:

    ![A screenshot mostra a caixa de diálogo de definições de signon único.](./media/ideascale-tutorial/ic790850.png "Definições de signon único")

    a. Na caixa de texto **IdP ID da ENTIDADE SAML,** cole o valor do **Identificador Ad Azure** que copiou do portal Azure.

    b. Abra o ficheiro de metadados descarregado do portal Azure para o Notepad, copie o conteúdo do mesmo e cole-o na caixa de texto de **metadados IdP SAML.**

    c. Na caixa de texto **URL de sucesso logout,** cole o valor do **URL logout** que copiou do portal Azure.

    d. Clique em **Guardar Alterações**.

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao IdeaScale.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **IdeaScale**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **IdeaScale**.

    ![O link IdeaScale na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-ideascale-test-user"></a>Criar utilizador de teste IdeaScale

Para permitir que os utilizadores de Azure AD acedam ao IdeaScale, eles devem ser adustados no IdeaScale. No caso do IdeaScale, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento do utilizador, execute os seguintes passos:**

1. Faça login no seu site da empresa **IdeaScale** como administrador.

2. Vá para **as Configurações Comunitárias**.

    ![Configurações comunitárias](./media/ideascale-tutorial/ic790847.png "Configurações comunitárias")

3. Ir para **a Gestão de Membros de Definições \> Básicas**.

4. Clique **em Adicionar Membro**.

    ![Gestão de Membros](./media/ideascale-tutorial/ic790852.png "Gestão de Membros")

5. Na secção Adicionar Novo Membro, execute os seguintes passos:

    ![Adicionar novo membro](./media/ideascale-tutorial/ic790853.png "Adicionar novo membro")

    a. Na caixa de texto dos endereços de **e-mail,** digite o endereço de e-mail de uma conta Azure AD válida que pretenda.

    b. Clique em **Guardar Alterações**.

    > [!NOTE]
    > O titular da conta Azure Ative Directory recebe um e-mail com um link para confirmar a conta antes de ficar ativa.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador ideaScale ou APIs fornecidas pelo IdeaScale para fornecer contas de utilizadores Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo IdeaScale no Painel de Acesso, deverá ser automaticamente inscrito no IdeaScale para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)