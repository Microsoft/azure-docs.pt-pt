---
title: 'Tutorial: Integração do Azure Ative Directory com o SpaceIQ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SpaceIQ.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 74c3c4d1a9192256c36d41db7a4f9406b6c966f3
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92514729"
---
# <a name="tutorial-azure-active-directory-integration-with-spaceiq"></a>Tutorial: Integração do Diretório Ativo Azure com o SpaceIQ

Neste tutorial, aprende-se a integrar o SpaceIQ com o Azure Ative Directory (Azure AD).
A integração do SpaceIQ com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao SpaceIQ.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no SpaceIQ (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o SpaceIQ, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Subscrição ativada por um único sign-on spaceIQ

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* SpaceIQ suporta SSO iniciado pelo **IDP**

## <a name="adding-spaceiq-from-the-gallery"></a>Adicionar SpaceIQ da galeria

Para configurar a integração do SpaceIQ em Azure AD, precisa adicionar spaceIQ da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar SpaceIQ da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SpaceIQ,** selecione **SpaceIQ** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

    ![SpaceIQ na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o SpaceIQ com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de acesso ao trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no SpaceIQ.

Para configurar e testar o Azure AD com o SpaceIQ, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único SpaceIQ](#configure-spaceiq-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create SpaceIQ test user](#create-spaceiq-test-user)** - para ter uma contraparte de Britta Simon no SpaceIQ que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com spaceIQ, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **SpaceIQ,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na **configuração single Sign-On com página SAML,** execute os seguintes passos:

    ![SpaceIQ Domain e URLs informações únicas de acesso](common/idp-intiated.png)

    a. Na caixa de texto **identifier,** digite o URL: `https://api.spaceiq.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://api.spaceiq.com/saml/<instanceid>/callback`

    > [!NOTE]
    > Atualize estes valores com o URL de resposta real e o identificador que é explicado mais tarde no tutorial.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção Configurar o **SpaceIQ,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-spaceiq-single-sign-on"></a>Configurar Sign-On únicas spaceIQ

1. Abra uma nova janela do navegador e, em seguida, inscreva-se no seu ambiente SpaceIQ como administrador.

1. Uma vez iniciado o login, clique no sinal do puzzle no topo direito e, em seguida, clique em **Integrações**

    ![Definições da conta](./media/spaceiq-tutorial/setting1.png) 

1. Em **Todas as disposições & SSO,** clique no azulejo **Azure** para adicionar uma instância de Azure como IDP.

    ![Ícone DE SAML](./media/spaceiq-tutorial/setting2.png)

1. Na caixa de diálogo **SSO,** execute os seguintes passos:

    ![Definições de autenticação SAML](./media/spaceiq-tutorial/setting3.png)

    a. Na caixa URL do **emitente SAML,** cole o valor do **identificador Azure AD** copiado da janela de configuração da aplicação AZure AD.

    b. Copie o valor **DO URL de endpoint de chamada SAML (apenas para leitura)** e cole o valor na caixa **URL de resposta** na secção **Configuração Básica SAML** no portal Azure.

    c. Copie o valor **DO PÚBLICO SAML URI (apenas para leitura)** e cole o valor na caixa **do Identificador** na secção **Configuração Básica do SAML** no portal Azure.

    d. Abra o ficheiro de certificado descarregado no bloco de notas, copie o conteúdo e, em seguida, cole-o na caixa de **Certificado X.509.**

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
  
    b. No tipo de campo **nome de utilizador**`brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao SpaceIQ.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **SpaceIQ**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **SpaceIQ**.

    ![A ligação SpaceIQ na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-spaceiq-test-user"></a>Criar utilizador de teste SpaceIQ

Nesta secção, cria-se um utilizador chamado Britta Simon no SpaceIQ. Equipa [de suporte do Work SpaceIQ](mailto:eng@spaceiq.com) para adicionar os utilizadores na plataforma SpaceIQ. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SpaceIQ no Painel de Acesso, deverá ser automaticamente inscrito no SpaceIQ para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)