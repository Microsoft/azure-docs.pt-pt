---
title: 'Tutorial: Integração do Azure Ative Directory com o HackerOne | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o HackerOne.
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
ms.openlocfilehash: ac8bdc37bc2572b580deebc35931e324ed107d87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92446618"
---
# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>Tutorial: Integração do Azure Ative Directory com a HackerOne

Neste tutorial, aprende-se a integrar o HackerOne com o Azure Ative Directory (Azure AD).
A integração do HackerOne com a AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso ao HackerOne.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no HackerOne (Sign-on único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a HackerOne, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por hackerOne

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* HackerOne suporta SSO iniciado **pela SP**
* HackerOne suporta **provisão** de utilizadores just in time

## <a name="adding-hackerone-from-the-gallery"></a>Adicionando HackerOne da galeria

Para configurar a integração do HackerOne no AD Azure, é necessário adicionar o HackerOne da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar HackerOne da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite HackerOne,** selecione **HackerOne** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![HackerOne na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o HackerOne com base num utilizador de teste chamado **Britta Simon**.
Para um único s-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no HackerOne.

Para configurar e testar um único sinal de Ad AD com hackerOne, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure hackerOne single sign-on](#configure-hackerone-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create HackerOne test user](#create-hackerone-test-user)** - para ter uma contraparte de Britta Simon em HackerOne que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o HackerOne, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **HackerOne,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![HackerOne Domain e URLs informações únicas de súmis](common/sp-identifier.png)

    a. Na caixa de texto **URL, insira** o seguinte: `https://hackerone.com/users/saml/sign_in?email=<configured domain>`

    b. Na caixa de texto **identifier (Entity ID),** insira o seguinte: `hackerone.com`

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar HackerOne,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-hackerone-single-sign-on"></a>Configurar hacker Um único Sign-On

1. Inscreva-se no seu inquilino HackerOne como administrador.

2. No menu em cima, clique nas **Definições**.

    ![Screenshot que mostra "Definições" selecionadas no menu.](./media/hackerone-tutorial/tutorial_hackerone_001.png)

3. Navegue para **autenticação** e clique **em Adicionar as definições DE SAML**.

    ![Screenshot que mostra a página "Definições de autenticação" com o botão "Adicionar S A M L" selecionado.](./media/hackerone-tutorial/tutorial_hackerone_003.png)

4. No diálogo **de Definições SAML,** execute os seguintes passos:

    ![Configurar Sign-On Individuais](./media/hackerone-tutorial/tutorial_hackerone_004.png)

    a. Na caixa de texto **do Domínio de E-mail,** digite um domínio registado.

    b. Em  **Signo Único Nas** caixas de texto URL, cole o valor do URL de **login** que copiou do portal Azure.

    c. Abra o **seu ficheiro Certificado** descarregado do portal Azure para o Bloco de Notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **do Certificado X509.**

    d. Clique em **Guardar**.

5. No diálogo Definições de Autenticação, execute os seguintes passos:

    ![Screenshot que mostra o diálogo "Definições de autenticação" com o botão "Executar teste" selecionado.](./media/hackerone-tutorial/tutorial_hackerone_005.png)

    a. Clique **no teste de execução**.

6. Quando o teste terminar com sucesso e o campo **'Status'** mostrar o **último estado do teste: sucesso**, selecione o botão De Verificação de **Pedidos** para submeter ao HackerOne para aprovação.

    ![Submeta-se ao HackerOne para aprovação](./media/hackerone-tutorial/tutorial-hackerone-006.png)

7. Depois de o HackerOne aprovar as definições, pode selecionar o botão **Utilizadores Migradores** para exigir a autenticação SSO para todos os utilizadores.

    ![Ativar o SAML](./media/hackerone-tutorial/tutorial-hackerone-007.png)

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao HackerOne.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **HackerOne**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **HackerOne**.

    ![O link HackerOne na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-hackerone-test-user"></a>Criar utilizador de teste HackerOne

Nesta secção, um utilizador chamado Britta Simon é criado na HackerOne. O HackerOne suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no HackerOne, um novo é criado após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo HackerOne no Painel de Acesso, deverá ser automaticamente inscrito no HackerOne para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)