---
title: 'Tutorial: Integração do Azure Ative Directory com Pequenas Melhorias Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Small Improvements.
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
ms.openlocfilehash: 6eced120a05ddaca8d8cf426fd2a977891b3e36b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997142"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Tutorial: Integração do Diretório Ativo Azure com Pequenas Melhorias

Neste tutorial, aprende-se a integrar pequenas Melhorias com o Azure Ative Directory (Azure AD).
A integração de Pequenas Melhorias com AZure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Pequenas Melhorias.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos em Pequenas Melhorias (Sign-on Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com pequenas melhorias, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Pequenas Melhorias únicas de inscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Pequenas Melhorias suporta **SSO** iniciado SP

## <a name="adding-small-improvements-from-the-gallery"></a>Adicionar pequenas melhorias da galeria

Para configurar a integração de Pequenas Melhorias no AD Azure, é necessário adicionar Pequenas Melhorias da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Pequenas Melhorias da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva Pequenas Melhorias**, selecione **Pequenas Melhorias** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![Pequenas Melhorias na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com pequenas melhorias com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Pequenas Melhorias.

Para configurar e testar o Azure AD com pequenas melhorias, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure pequenas melhorias O único sinal -](#configure-small-improvements-single-sign-on)** para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Small Improvements test user](#create-small-improvements-test-user)** - para ter uma contrapartida de Britta Simon em Pequenas Melhorias que está ligada à representação AD AZure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com pequenas melhorias, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de **aplicações Small Improvements,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Pequenas melhorias Domínio e URLs informações únicas de súming](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<subdomain>.small-improvements.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<subdomain>.small-improvements.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente Small Improvements](mailto:support@small-improvements.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar Pequenas Melhorias,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-small-improvements-single-sign-on"></a>Configure pequenas melhorias Sign-On

1. Em outra janela do navegador, inscreva-se no site da empresa Small Improvements como administrador.

1. A partir da página principal do painel de **instrumentos,** clique no botão De administração à esquerda.

    ![A screenshot mostra o botão de Administração selecionado.](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. Clique no botão **SSO SAML** a partir da secção **Integrações.**

    ![A screenshot mostra o ícone SAML S S O selecionado em Integrações.](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. Na página SSO Configuração, execute os seguintes passos:

    ![A screenshot mostra a página S S O Configuração onde pode introduzir os valores descritos.](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. Na caixa de texto **HTTP Endpoint,** cole o valor do URL de **login,** que copiou do portal Azure.

    b. Abra o seu certificado descarregado no Bloco de Notas, copie o conteúdo e, em seguida, cole-o na caixa de texto **do Certificado X509.** 

    c. Se deseja ter a opção de autenticação do formulário SSO e Login disponível para os utilizadores, verifique também o **acesso ativar via login/password.**  

    d. Introduza o valor adequado para nomear o botão SSO Login na caixa de texto **DE PEDIDO SAML.**  

    e. Clique em **Guardar**.

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

Nesta secção, você permite que Britta Simon utilize o Azure single sign-on, concedendo acesso a Pequenas Melhorias.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **Pequenas Melhorias**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Pequenas Melhorias**.

    ![O link Small Improvements na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-small-improvements-test-user"></a>Criar pequeno utilizador de teste de melhorias

Para permitir que os utilizadores de Azure AD iniciem sessão em Pequenas Melhorias, devem ser aditados em Pequenas Melhorias. No caso de Pequenas Melhorias, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Iniciar sing-on no site da empresa Small Improvements como administrador.

1. A partir da página Inicial, vá ao menu à esquerda, clique em **Administração**.

1. Clique no botão **Diretório** de Utilizador a partir da secção Gestão do Utilizador.

    ![A screenshot mostra o Diretório de Utilizador selecionado a partir de Visão Geral da Administração.](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. Clique **em Adicionar utilizadores**.

    ![A screenshot mostra o botão adicionar utilizadores.](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. No diálogo **'Adicionar utilizadores',** execute os seguintes passos: 

    ![A screenshot mostra a caixa de diálogo dos utilizadores Add onde pode introduzir os valores descritos.](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)

    a. Insira o **primeiro nome** de utilizador como **Britta**.

    b. Insira o **último nome** de utilizador como **Simon.**

    c. Introduza o **e-mail** do utilizador como **brittasimon@contoso.com** .

    d. Também pode optar por inserir a mensagem pessoal na caixa **de e-mail de envio.** Se não pretender enviar a notificação, desmarque esta caixa de verificação.

    e. Clique **em Criar Utilizadores.**

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Small Improvements no Painel de Acesso, deverá ser automaticamente inscrito nas Pequenas Melhorias para as quais configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)