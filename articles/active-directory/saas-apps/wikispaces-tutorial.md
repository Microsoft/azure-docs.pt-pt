---
title: 'Tutorial: Integração do Azure Ative Directory com wikispaces Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Wikispaces.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: f62e746a189a14838227a40be3bca9d05553a642
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92635801"
---
# <a name="tutorial-azure-active-directory-integration-with-wikispaces"></a>Tutorial: Integração do Azure Ative Directory com wikispaces

Neste tutorial, você aprende a integrar wikispaces com Azure Ative Directory (Azure AD).
A integração dos Wikispaces com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Wikispaces.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Wikispaces (Sign-on único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com wikispaces, você precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Wikispaces subscrição ativada única

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Wikispaces suporta **SSO** iniciado SP

## <a name="adding-wikispaces-from-the-gallery"></a>Adicionar Wikispaces da galeria

Para configurar a integração dos Wikispaces no Azure AD, é necessário adicionar Wikispaces da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Wikispaces da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Wikispaces,** selecione **Wikispaces** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![Wikispaces na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Wikispaces com base em um utilizador de teste chamado **Britta Simon** .
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Wikispaces.

Para configurar e testar o Azure AD com wikispaces, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure wikispaces Single Sign-On](#configure-wikispaces-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Wikispaces test user](#create-wikispaces-test-user)** - para ter uma contraparte de Britta Simon em Wikispaces que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com wikispaces, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **wikispaces,** selecione **Single sign-on** .

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Wikispaces Domain e URLs informações únicas de súmis](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<companyname>.wikispaces.net`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://session.wikispaces.net/<instancename>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte a [equipa de suporte do Cliente wikispaces](https://www.wikispaces.com/site/help) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configuração wikispaces,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-wikispaces-single-sign-on"></a>Configurar wikispaces single Sign-On

Para configurar um único sign-on no lado **wikispaces,** você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipa de [suporte wikispaces](https://www.wikispaces.com/site/help). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory** , selecione **Utilizadores** , e, em seguida, selecione **Todos os utilizadores** .

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon** .
  
    b. No tipo de campo **do nome do utilizador** brittasimon@yourcompanydomain.extension . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a Wikispaces.

1. No portal Azure, selecione **Aplicações empresariais** , selecione **Todas as aplicações** e, em seguida, selecione **Wikispaces** .

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Wikispaces** .

    ![O link Wikispaces na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos** .

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-wikispaces-test-user"></a>Criar utilizador de teste wikispaces

Para permitir que os utilizadores de Azure AD inscrevam-se nos Wikispaces, devem ser a provisionados em Wikispaces. No caso dos Wikispaces, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para a disponibilização de uma conta de utilizador, execute os seguintes passos:

1. Inscreva-se no site da sua empresa **Wikispaces** como administrador.

2. Vá aos **deputados.**
   
    ![Membros](./media/wikispaces-tutorial/ic787193.png "Membros")

3. Clique no **Pessoas convidadas.**
   
    ![A screenshot mostra a página dos membros onde pode selecionar Pessoas convidadas.](./media/wikispaces-tutorial/ic787194.png "Convidar pessoas")

4. Na secção **Pessoas Convidadas,** execute os seguintes passos:
   
    ![A screenshot mostra a secção Pessoas convidadas onde pode introduzir dados do utilizador.](./media/wikispaces-tutorial/ic787208.png "Convidar pessoas")
   
    a. Digite os nomes de utilizador ou endereço de **e-mail** de uma conta AD Azure válida que pretenda obter nas caixas de texto relacionadas.
   
    b. Clique em **Enviar** .  
      
    > [!NOTE]
    > O titular da conta Azure Ative Directory recebe um e-mail incluindo um link para confirmar a conta antes de ficar ativa.
    
> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador wikispaces ou APIs fornecidas pela Wikispaces para fornecer contas de utilizadores Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Wikispaces no Painel de Acesso, deverá ser automaticamente inscrito nos Wikispaces para os quais configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)