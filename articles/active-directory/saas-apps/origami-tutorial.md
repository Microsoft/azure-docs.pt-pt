---
title: 'Tutorial: Integração do Azure Ative Directory com o Origami Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Origami.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 39f2f592601f60f060cce8ded2b5303dc6b10e8a
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91995826"
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Tutorial: Integração do Diretório Ativo Azure com Origami

Neste tutorial, aprende-se a integrar o Origami com o Azure Ative Directory (Azure AD).
A integração do Origami com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Origami.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Origami (Sign-on Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração azure AD com o Origami, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por origami

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Origami apoia SSO iniciado **pela SP**

## <a name="adding-origami-from-the-gallery"></a>Adicionar Origami da galeria

Para configurar a integração do Origami no AD Azure, precisa adicionar Origami da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Origami da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Origami,** selecione **Origami** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![Origami na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Origami com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Origami.

Para configurar e testar o Azure AD com origami, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único origami](#configure-origami-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Origami test user](#create-origami-test-user)** - para ter uma contraparte de Britta Simon em Origami que está ligada à representação AZure AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com origami, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Origami,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Origami Domain e URLs informações únicas de súmiciclo](common/sp-signonurl.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://live.origamirisk.com/origami/account/login?account=<companyname>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte [a equipa de apoio ao Cliente origami](https://wordpress.org/support/theme/origami) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar Origami,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-origami-single-sign-on"></a>Configurar origami single Sign-On

1. Faça login na conta origami com direitos de administração.

2. No menu em cima, clique em **Administração.**
   
    ![Screenshot que mostra a página inicial de Origami com "Administrador" selecionado.](./media/origami-tutorial/tutorial_origami_51.png)

3. Na página de diálogo de sinal único na configuração, execute os seguintes passos:
   
    ![Screenshot que mostra a página "Sign On Setup" com "Enable Single Sign-on" selecionado e as caixas de texto destacadas.](./media/origami-tutorial/tutorial_origami_531.png)

    a. Selecione **Ativar um único sinal .**

    b. Na caixa de texto **URL do Fornecedor de Identidade,** cole o valor do URL de **login,** que copiou do portal Azure.

    c. Na caixa de texto **URL do Fornecedor de Identidade,** cole o valor do URL **logout,** que copiou do portal Azure.

    d. Clique **em Procurar** para fazer o upload do certificado que descarregou a partir do portal Azure.

    e. Clique em **Guardar Alterações**.

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a Origami.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Origami**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Origami**.

    ![O link Origami na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-origami-test-user"></a>Criar utilizador de teste origami

Nesta secção, cria-se um utilizador chamado Britta Simon em Origami. 

1. Faça login na conta origami com direitos de administração.

2. No menu em cima, clique em **Administração.**
   
    ![Screenshot que mostra a página inicial da conta origami com "Administrador" selecionado.](./media/origami-tutorial/tutorial_origami_51.png)

3. No diálogo **de Utilizadores e Segurança,** clique nos **Utilizadores**.
   
    ![Screenshot que mostra o diálogo "Utilizadores e Segurança" com "Utilizadores" selecionados.](./media/origami-tutorial/tutorial_origami_54.png)

4. Clique **em Adicionar Novo Utilizador**.
   
    ![Screenshot que mostra o botão "Adicionar Novo Utilizador" selecionado.](./media/origami-tutorial/tutorial_origami_55.png)

5. No diálogo Adicionar Novo Utilizador, execute os seguintes passos:
   
    ![Screenshot que mostra o diálogo "Adicionar Novo Utilizador" com as caixas de texto "Nome de Utilizador", "Primeiro Nome" e "Apelido" em destaque.](./media/origami-tutorial/tutorial_origami_56.png)

    a. Na caixa de texto **do Nome do Utilizador,** introduza o e-mail do utilizador como ** \@ brittasimon contoso.com**.

    b. Na caixa de texto da **palavra-passe,** digite uma palavra-passe.

    c. Na caixa de texto **'Confirmar palavra-passe',** escreva novamente a palavra-passe.

    d. Na caixa de texto **Name Name,** insira o primeiro nome de utilizador como **Britta**.

    e. Na caixa de texto **Do Último Nome,** insira o último nome de utilizador como **Simon**.

    f. Clique em **Guardar**.
   
    ![Screenshot que mostra o botão "Guardar" selecionado.](./media/origami-tutorial/tutorial_origami_57.png)

6. Atribuir **funções de utilizador** e acesso ao **cliente** ao utilizador. 
   
    ![Configurar Sign-On Individuais](./media/origami-tutorial/tutorial_origami_58.png)

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo origami no Painel de Acesso, deverá ser automaticamente inscrito no Origami para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

