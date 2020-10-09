---
title: 'Tutorial: Integração do Azure Ative Directory com o LockPath Keylight Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o LockPath Keylight.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 82af00c091aedb56e0cad954e554094e75df0013
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88535867"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Tutorial: Integração do Diretório Ativo Azure com o LockPath Keylight

Neste tutorial, aprende-se a integrar o LockPath Keylight com o Azure Ative Directory (Azure AD).
A integração do Keylight LockPath com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a LockPath Keylight.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Keylight LockPath (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o LockPath Keylight, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* LockPath Keylight única subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Chave do LockPath suporta **SSO** iniciado SP
* O Keylight LockPath suporta o fornecimento do utilizador **Just In Time**

## <a name="adding-lockpath-keylight-from-the-gallery"></a>Adicionar chave de lockpath da galeria

Para configurar a integração do LockPath Keylight em Azure AD, precisa adicionar o Keylight LockPath da galeria à sua lista de aplicações geridas para o SaaS.

**Para adicionar a chave de segurança LockPath da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite o Keylight LockPath**, selecione **LockPath Keylight** do painel de resultados e, em seguida, clique em Adicionar o botão **Adicionar** a aplicação.

    ![Chave lockPath na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sinal de sinalização com o LockPath Keylight baseado num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no LockPath Keylight.

Para configurar e testar o Azure AD com a chave de chaves LockPath, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure a chave de teclas LockPath Single Sign-On](#configure-lockpath-keylight-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create LockPath Keylight test user](#create-lockpath-keylight-test-user)** - para ter uma contraparte de Britta Simon em LockPath Keylight que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com a chave de chaves LockPath, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **LockPath Keylight,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Informações de keylight lockPath e URLs únicas informações de súbs](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<company name>.keylightgrc.com/`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<company name>.keylightgrc.com`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<company name>.keylightgrc.com/Login.aspx`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real em URL, Identifier e URL de resposta. Contacte [a equipa de suporte do cliente LockPath Keylight](https://www.lockpath.com/contact/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Raw)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificateraw.png)

6. Na secção **De Teclas LockPath,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-lockpath-keylight-single-sign-on"></a>Configure a chave do lockpath única Sign-On

1. Para ativar o SSO na chave de segurança LockPath, execute os seguintes passos:

    a. Iniciar s-in na sua conta LockPath Keylight como administrador.

    b. No menu em cima, clique em **Pessoa**e selecione **Keylight Setup**.

    ![Configurar Sign-On Individuais](./media/keylight-tutorial/401.png)

    c. Na visão da árvore à esquerda, clique em **SAML**.

    ![Configurar Sign-On Individuais](./media/keylight-tutorial/402.png)

    d. No diálogo **de Definições SAML,** clique em **Editar**.

    ![Configurar Sign-On Individuais](./media/keylight-tutorial/404.png)

1. Na página de diálogo **de configurações de Edição SAML,** execute os seguintes passos:

    ![Configurar Sign-On Individuais](./media/keylight-tutorial/405.png)

    a. Desementação **SAML** para **Ative**.

    b. Na caixa de texto **URL do Fornecedor de Identidade,** cole o valor URL de **login** que copiou a partir do portal Azure.

    c. Na caixa de texto **URL do Fornecedor de Identidade,** cole o valor URL **logout** que copiou do portal Azure.

    d. Clique **em Escolher O Ficheiro** para selecionar o certificado de chave do LockPath descarregado e, em seguida, clique em **Abrir** para carregar o certificado.

    e. Definir **a localização do ID do utilizador SAML** para **o elemento identificador do nome da declaração do assunto**.

    f. Forneça ao **Fornecedor de Serviços keylight** o seguinte padrão: `https://<CompanyName>.keylightgrc.com` .

    exemplo, Desa **cosar automaticamente os utilizadores** **ativos.**

    h. Desa **cosão automática do tipo** de conta para **o Utilizador Completo.**

    i. Desa cos para definir **a função de segurança de fornecimento automático**, selecione Utilizador Standard com **SAML**.

    j. Desconfig **de segurança de fornecimento automático,** selecione **Configuração padrão do utilizador**.

    k. Na caixa de texto **do atributo email,** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    l. Na caixa de texto do **primeiro nome,** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .

    m. Na **caixa de texto do último nome,** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` .

    n. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **do nome do utilizador** `brittasimon@yourcompanydomain.extension` . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao LockPath Keylight.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **LockPath Keylight**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **LockPath Keylight**.

    ![O link de chave lockPath na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-lockpath-keylight-test-user"></a>Criar utilizador de teste de chave do LockPath

Nesta secção, um utilizador chamado Britta Simon é criado em LockPath Keylight. O LockPath Keylight suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no LockPath Keylight, um novo é criado após a autenticação. Se precisar de criar um utilizador manualmente, tem de contactar a equipa de suporte do [Cliente LockPath Keylight](https://www.lockpath.com/contact/).

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no tecla de teclado LockPath no Painel de Acesso, deverá ser automaticamente inscrito na chave de teclas LockPath para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)