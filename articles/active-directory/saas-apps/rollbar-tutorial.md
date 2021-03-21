---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Rollbar | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Rollbar.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/15/2019
ms.author: jeedes
ms.openlocfilehash: 2d5aedf24034c9ba5ee865dd0d2289169ea5f859
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92520664"
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Tutorial: Integração do Diretório Ativo Azure com o Rollbar

Neste tutorial, aprende-se a integrar o Rollbar com o Azure Ative Directory (Azure AD).
A integração do Rollbar com a Ad Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso ao Rollbar.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Rollbar (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o Rollbar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura única ativada do rollbar

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Rollbar suporta **SP e IDP** iniciado SSO

## <a name="adding-rollbar-from-the-gallery"></a>Adicionar Rollbar da galeria

Para configurar a integração do Rollbar no Azure AD, é necessário adicionar o Rollbar da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Rollbar da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Rollbar**, selecione **Rollbar** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![Rollbar na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com rollbar baseado num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Rollbar.

Para configurar e testar o Azure AD com rollbar, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o rollbar single sign-on](#configure-rollbar-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Rollbar test user](#create-rollbar-test-user)** - para ter uma contraparte de Britta Simon no Rollbar que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com rollbar, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Rollbar,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![A screenshot mostra a Configuração BÁSICA SAML, onde pode introduzir o Identificador, Responder U R L e selecionar Guardar.](common/idp-intiated.png)

    a. Na caixa de texto **identifier,** digite o URL: `https://saml.rollbar.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://rollbar.com/<accountname>/saml/sso/azure/`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![Screenshot mostra Definir U R Ls adicionais onde pode introduzir um sinal em U R L.](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://rollbar.com/<accountname>/saml/login/azure/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de resposta real e Sign-On URL. Contacte [a equipa de suporte do Cliente Rollbar](mailto:support@rollbar.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

7. Na secção **Configurar rollbar,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-rollbar-single-sign-on"></a>Configurar Sign-On single rollbar

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Rollbar como administrador.

1. Clique nas **Definições** de Perfil no canto superior direito e, em seguida, clique nas **definições de Nome de Conta**.

    ![A screenshot mostra as definições de nome de conta selecionadas a partir de Definições de Perfil.](./media/rollbar-tutorial/general.png)

1. Clique em **Fornecedor de Identidade** em SEGURANÇA.

    ![A screenshot mostra o Fornecedor de Identidade selecionado em SEGURANÇA.](./media/rollbar-tutorial/configure1.png)

1. Na secção **SAML Identity Provider,** execute os seguintes passos:

    ![A screenshot mostra o Fornecedor de Identidade SAML onde pode introduzir os valores descritos.](./media/rollbar-tutorial/configure2.png)

    a. Selecione **AZURE** a partir do **dropdown do Fornecedor de Identidade SAML.**

    b. Abra o seu ficheiro de metadados no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **dos metadados SAML.**

    c. Clique em **Guardar**.

1. Depois de clicar no botão de salvamento, o ecrã será assim:

    ![A screenshot mostra os resultados na página do Fornecedor de Identidade SAML.](./media/rollbar-tutorial/configure3.png)

    > [!NOTE]
    > Para completar o passo seguinte, deve primeiro adicionar-se como utilizador à aplicação Rollbar em Azure.
    >

    a. Se pretender exigir que todos os utilizadores autentem a autenticação via Azure, clique **em iniciar sessão através do seu fornecedor** de identidade para reautensar através do Azure.  

    b.  Assim que voltar ao ecrã, selecione o **login 'Requerer' através da** caixa de verificação DO Fornecedor de Identidade SAML.

    b. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador**`brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon utilize o Azure single sign-on, concedendo acesso ao Rollbar.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **Rollbar**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Rollbar**.

    ![O link Rollbar na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-rollbar-test-user"></a>Criar utilizador de teste rollbar

Para permitir que os utilizadores Azure AD inscrevam-se no Rollbar, devem ser aprovisionados no Rollbar. No caso do Rollbar, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da sua empresa Rollbar como administrador.

1. Clique nas **Definições** de Perfil no canto superior direito e, em seguida, clique nas **definições de Nome de Conta**.

    ![User](./media/rollbar-tutorial/general.png)

1. Clique em **Utilizadores**.

    ![Adicionar Empregado](./media/rollbar-tutorial/user1.png)

1. Clique **em Convidar Membros da Equipa**.

    ![A screenshot mostra a opção de membros da equipa de convite selecionados.](./media/rollbar-tutorial/user2.png)

1. Na caixa de texto, insira o nome do utilizador como **\@ brittasimon contoso.com** e o clique **Add/Invite**.

    ![A screenshot mostra Add/Invite Members com um endereço fornecido.](./media/rollbar-tutorial/user3.png)

1. O utilizador recebe um convite e depois de o aceitar são criados no sistema.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo rollbar no Painel de Acesso, deverá ser automaticamente inscrito na Barra de Rolo para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)