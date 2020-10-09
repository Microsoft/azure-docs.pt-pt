---
title: 'Tutorial: Integração do Azure Ative Directory com o Zoho Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Zoho.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jeedes
ms.openlocfilehash: 00b9df3ad5e4d2104051e5489138785b4ecde8e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88546156"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Tutorial: Integração do Diretório Ativo Azure com o Zoho

Neste tutorial, aprende-se a integrar o Zoho com o Azure Ative Directory (Azure AD).
A integração do Zoho com a AD Azure proporciona-lhe os seguintes benefícios:

* Podes controlar o Azure AD que tem acesso ao Zoho.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Zoho (Sign-on único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração azure AD com zoho, você precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada por zoho

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Zoho apoia **SP** iniciado SSO

## <a name="adding-zoho-from-the-gallery"></a>Adicionando Zoho da galeria

Para configurar a integração do Zoho no AD Azure, precisa adicionar zoho da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Zoho da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Zoho,** selecione **Zoho** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![Zoho na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Zoho com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Zoho.

Para configurar e testar o Azure AD com o Zoho, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único zoho](#configure-zoho-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Zoho test user](#create-zoho-test-user)** - ter uma contraparte de Britta Simon em Zoho que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com zoho, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Zoho,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Zoho Domain e URLs informações únicas de súmis](common/sp-signonurl.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<company name>.zohomail.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte [a equipa de apoio ao Cliente Zoho](https://www.zoho.com/mail/contact.html) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar Zoho,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-zoho-single-sign-on"></a>Configure zoho single Sign-On

1. Numa janela diferente do navegador, inicie sessão no site da empresa Zoho Mail como administrador.

2. Vá ao **painel de controlo.**
   
    ![Painel de Controlo](./media/zoho-mail-tutorial/ic789607.png "Painel de Controlo")

3. Clique no separador **autenticação SAML.**
   
    ![Autenticação SAML](./media/zoho-mail-tutorial/ic789608.png "Autenticação SAML")

4. Na secção Detalhes de **Autenticação SAML,** execute os seguintes passos:
   
    ![Detalhes da autenticação SAML](./media/zoho-mail-tutorial/ic789609.png "Detalhes da autenticação SAML")
   
    a. Na caixa de texto **do URL de login,** cole URL de **login** que copiou do portal Azure.
   
    b. Na caixa **de textos logout URL,** cole **URL logout** que copiou do portal Azure.
   
    c. Na caixa de texto **URL de alteração da palavra-passe,** cole URL de **palavra-passe** de alteração que copiou do portal Azure.
       
    d. Abra o certificado codificado base-64 descarregado do portal Azure no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o para a caixa de texto **PublicKey.**
   
    e. Como **algoritmo,** selecione **RSA**.
   
    f. Clique em **OK**.

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao Zoho.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Zoho**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, escreva e selecione **Zoho.**

    ![O link Zoho na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-zoho-test-user"></a>Criar utilizador de teste Zoho

Para permitir que os utilizadores da Azure AD acedam ao Zoho Mail, devem ser ateados no Zoho Mail. No caso do Zoho Mail, o provisionamento é uma tarefa manual.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador do Zoho Mail ou APIs fornecidas pelo Zoho Mail para fornecer contas de utilizadores Azure AD.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para obter uma conta de utilizador, execute os seguintes passos:

1. Faça login no seu site da empresa **Zoho Mail** como administrador.

1. Vá ao **Control Panel Mail & \> Docs**.

1. Vá aos **detalhes do utilizador Adicionar \> Utilizador**.
   
    ![Adicionar utilizador](./media/zoho-mail-tutorial/ic789611.png "Adicionar Utilizador")

1. No diálogo **de utilizadores Adicionar,** execute os seguintes passos:
   
    ![Adicionar utilizador](./media/zoho-mail-tutorial/ic789612.png "Adicionar Utilizador")
   
    a. Na caixa de texto **Name Name,** digite o primeiro nome de utilizador como **Britta**.

    b. Na caixa de texto **Do Último Nome,** digite o último nome de utilizador como **Simon**.

    c. Na caixa de texto **do Email ID,** digite o id de e-mail do utilizador como ** \@ brittasimon contoso.com**.

    d. Na caixa de texto **da palavra-passe,** introduza a palavra-passe do utilizador.
   
    e. Clique em **OK**.  
      
    > [!NOTE]
    > O titular da conta Azure Ative Directory receberá um e-mail com um link para confirmar a conta antes de ficar ativa.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo zoho no Painel de Acesso, deverá ser automaticamente inscrito no Zoho para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

