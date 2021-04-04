---
title: 'Tutorial: Integração do Azure Ative Directory com a Plataforma de Produtividade ClickUp | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Plataforma de Produtividade ClickUp.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: 753256adddf8b28518bac45011d582bc7a079629
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97670463"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Tutorial: Integração do Azure Ative Directory com a Plataforma de Produtividade ClickUp

Neste tutorial, aprende-se a integrar a Plataforma de Produtividade ClickUp com o Azure Ative Directory (Azure AD).
A integração da Plataforma de Produtividade ClickUp com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso à Plataforma de Produtividade ClickUp.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Plataforma de Produtividade ClickUp (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a Plataforma de Produtividade ClickUp, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada pela Plataforma de Produtividade ClickUp

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Plataforma de Produtividade ClickUp suporta **SSO** iniciado sp

## <a name="adding-clickup-productivity-platform-from-the-gallery"></a>Adicionar plataforma de produtividade clickUp da galeria

Para configurar a integração da Plataforma de Produtividade ClickUp em AD Azure, precisa adicionar a Plataforma de Produtividade ClickUp da galeria à sua lista de aplicações geridas para o SaaS.

**Para adicionar a Plataforma de Produtividade ClickUp da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva a Plataforma de Produtividade ClickUp**, selecione **ClickUp Productivity Platform** a partir do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Plataforma de Produtividade ClickUp na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com a Plataforma de Produtividade ClickUp com base num utilizador de teste chamado **Britta Simon**.
Para um único s-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Plataforma de Produtividade ClickUp.

Para configurar e testar o único sinal de Adure com a Plataforma de Produtividade ClickUp, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure a plataforma de produtividade ClickUp Single Sign-On](#configure-clickup-productivity-platform-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. Crie o utilizador de **[teste da Plataforma de Produtividade ClickUp](#create-clickup-productivity-platform-test-user)** - para ter uma contrapartida de Britta Simon na Plataforma de Produtividade ClickUp que está ligada à representação AD AZure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com a Plataforma de Produtividade ClickUp, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações da **Plataforma de Produtividade ClickUp,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![ClickUp Productivity Platform Domain e URLs informações únicas de súpr on](common/sp-identifier.png)

    a. Na caixa de texto **URL,** digite um URL: `https://app.clickup.com/login/sso`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > O valor do Identificador não é real. Atualize este valor com o identificador real, que é explicado mais tarde neste tutorial.

5. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

### <a name="configure-clickup-productivity-platform-single-sign-on"></a>Configurar plataforma de produtividade clickUp única Sign-On

1. Numa janela diferente do navegador web, inscreva-se no seu inquilino da Plataforma de Produtividade ClickUp como administrador.

2. Clique no **perfil do Utilizador** e, em seguida, selecione **Definições**.

    ![O screenshot mostra o inquilino de produtividade clickUp com o ícone Definições selecionado.](./media/clickup-productivity-platform-tutorial/configure0.png)

    ![A imagem mostra definições.](./media/clickup-productivity-platform-tutorial/configure1.png)

3. Selecione **Microsoft**, em Fornecedor de Sign-On (SSO).

    ![A screenshot mostra o painel de autenticação com a Microsoft selecionada.](./media/clickup-productivity-platform-tutorial/configure2.png)

4. Na **página Configure Microsoft Single Sign On,** execute os seguintes passos:

    ![O screenshot mostra o sinal único do Microsoft configurar na página onde pode copiar a Entidade I D e salvar os metadados U R L da Federação Azure.](./media/clickup-productivity-platform-tutorial/configure3.png)

    a. Clique em **Copiar** para copiar o valor do ID da Entidade e cole-o na caixa de texto **identifier (Entity ID)** na secção **configuração BÁSICA SAML** no portal Azure.

    b. Na caixa de texto **URL da Federação Azure,** cole o valor do url de metadados da Federação de Aplicações, que copiou a partir do portal Azure e, em seguida, clique em **Guardar**.

5. Para completar a configuração, clique em **Autenticar com a Microsoft para completar a configuração** e autenticar com a conta microsoft.

    ![A screenshot mostra o Authenticate com a Microsoft para completar o botão de configuração.](./media/clickup-productivity-platform-tutorial/configure4.png)

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

Nesta secção, permite que Britta Simon utilize o Azure single sign-on, concedendo acesso à Plataforma de Produtividade ClickUp.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **a Plataforma de Produtividade ClickUp**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **ClickUp Productivity Platform**.

    ![O link da Plataforma de Produtividade ClickUp na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-clickup-productivity-platform-test-user"></a>Criar utilizador de teste de plataforma de produtividade ClickUp

1. Numa janela diferente do navegador web, inscreva-se no seu inquilino da Plataforma de Produtividade ClickUp como administrador.

2. Clique no **perfil de Utilizador** e, em seguida, selecione **Pessoas**.

    ![A screenshot mostra o inquilino da Produtividade ClickUp.](./media/clickup-productivity-platform-tutorial/configure0.png)

    ![A screenshot mostra a ligação Pessoas selecionada.](./media/clickup-productivity-platform-tutorial/user1.png)

3. Introduza o endereço de e-mail do utilizador na caixa de texto e clique em **Convidar**.

    ![O Screenshot mostra as Definições dos Utilizadores da Equipa onde pode convidar as pessoas por e-mail.](./media/clickup-productivity-platform-tutorial/user2.png)

    > [!NOTE]
    > O utilizador receberá a notificação e deve aceitar o convite para ativar a conta.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo da Plataforma de Produtividade ClickUp no Painel de Acesso, deverá ser automaticamente inscrito na Plataforma de Produtividade ClickUp para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)