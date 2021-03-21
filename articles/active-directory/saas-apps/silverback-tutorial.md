---
title: 'Tutorial: Integração do Azure Ative Directory com silverback | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Silverback.
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
ms.openlocfilehash: a70b6bb50b397429af1af41869bbe9ecf7e8bad9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004182"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>Tutorial: Integração do Azure Ative Directory com Silverback

Neste tutorial, você aprende a integrar Silverback com Azure Ative Directory (Azure AD).
A integração da Silverback com a Azure AD proporciona-lhe os seguintes benefícios:

* Podes controlar o Azure AD que tem acesso ao Silverback.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Silverback (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com silverback, você precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada única silverback

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Silverback suporta **SP** iniciado SSO

## <a name="adding-silverback-from-the-gallery"></a>Adicionando Silverback da galeria

Para configurar a integração de Silverback no Azure AD, você precisa adicionar Silverback da galeria à sua lista de aplicações geridas saaS.

**Para adicionar Silverback da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Silverback**, selecione **Silverback** do painel de resultados e, em seguida, clique em Adicionar o botão **Adicionar** a aplicação.

     ![Silverback na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Silverback com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Silverback.

Para configurar e testar o Azure AD com silverback, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único silverback](#configure-silverback-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Silverback test user](#create-silverback-test-user)** - para ter uma contraparte de Britta Simon em Silverback que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com Silverback, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Silverback,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Silverback Domain e URLs informações únicas de súmis](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<YOURSILVERBACKURL>.com/ssp`

    b. Na caixa **identifier,** digite um URL utilizando o seguinte padrão: `<YOURSILVERBACKURL>.com`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<YOURSILVERBACKURL>.com/sts/authorize/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Sign-On real, identifier e responder URL. Contacte [a equipa de suporte do Cliente Silverback](mailto:helpdesk@matrix42.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

### <a name="configure-silverback-single-sign-on"></a>Configurar silverback single Sign-On

1. Num navegador web diferente, faça login no seu Silverback Server como administrador.

2. Navegue para **o**  >  **Fornecedor de Autenticação** Admin.

3. Na página **Definições do Fornecedor de Autenticação,** execute os seguintes passos:

    ![O administrador](./media/silverback-tutorial/tutorial_silverback_admin.png)

    a.  Clique em **Import from URL**.

    b.  Cole o URL de metadados copiado e clique **em OK**.

    c.  Confirme com **OK,** em seguida, os valores serão povoados automaticamente.

    d.  Ativar **o Show na Página de Login**.

    e.  Ativar **a Criação dinâmica do utilizador** se pretender adicionar automaticamente os utilizadores autorizados pela Azure AD (opcional).

    f.  Crie um **título** para o botão no Portal do Autosserviço.

    exemplo,  Faça upload de um **ícone** clicando no **Choose File**.

    h.  Selecione a **cor** de fundo para o botão.

    i.  Clique em **Guardar**.

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a Silverback.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **Silverback**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Silverback**.

    ![O link Silverback na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-silverback-test-user"></a>Criar utilizador de teste Silverback

Para permitir que os utilizadores da Azure AD iniciem sessão em Silverback, devem ser abastados em Silverback. Em Silverback, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Faça login no seu Servidor Silverback como administrador.

2. Navegue para **os Utilizadores** e **adicione um novo utilizador do dispositivo**.

3. Na página **Basic,** execute os seguintes passos:

    ![O utilizador](./media/silverback-tutorial/tutorial_silverback_user.png)

    a. Na caixa de texto **username,** insira o nome de utilizador como **Britta**.

    b. Na caixa de texto **First Name,** insira o primeiro nome do utilizador como **Britta**.

    c. Na caixa de texto **Last Name,** insira o último nome do utilizador como **Simon**.

    d. Na caixa de texto **do endereço de e-mail,** insira o e-mail de um utilizador como **o Brittasimon \@ contoso.com**.

    e. Na caixa de texto da **palavra-passe,** insira a sua palavra-passe.

    f. Na caixa de texto **'Confirmar palavra-passe',** introduza novamente a sua palavra-passe e confirme.

    exemplo, Clique em **Guardar**.

> [!NOTE]
> Se não pretender criar cada utilizador manualmente, Ative a Caixa **de Verificação dinâmica** da criação do utilizador sob o Provedor de Autenticação **Admin**  >  .

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Silverback no Painel de Acesso, deverá ser automaticamente inscrito no Silverback para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)