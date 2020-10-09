---
title: 'Tutorial: Integração do Azure Ative Directory com a Kintone Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Kintone.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 2e831fd561b7c8dcf78e9203ec6d38c60c22a5bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88535361"
---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>Tutorial: Integração do Diretório Ativo Azure com a Kintone

Neste tutorial, aprende-se a integrar a Kintone com o Azure Ative Directory (Azure AD).
A integração da Kintone com a Azure AD proporciona-lhe os seguintes benefícios:

* Podes controlar o Azure AD que tem acesso ao Kintone.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Kintone (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração ad da Azure com a Kintone, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura ativada kintone

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Kintone apoia **SP** iniciado SSO

## <a name="adding-kintone-from-the-gallery"></a>Adicionando Kintone da galeria

Para configurar a integração da Kintone no Azure AD, precisa adicionar Kintone da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Kintone da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Kintone,** selecione **Kintone** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![Kintone na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Kintone com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Kintone.

Para configurar e testar o Azure AD com a Kintone, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure kintone single sign-on](#configure-kintone-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Kintone test user](#create-kintone-test-user)** - ter uma contraparte de Britta Simon em Kintone que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com Kintone, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Kintone,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Kintone Domain e URLs informações únicas de súming](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<companyname>.kintone.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão:
    
    ```http
    https://<companyname>.cybozu.com
    https://<companyname>.kintone.com
    ```

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte a [equipa de suporte do Cliente Kintone](https://www.kintone.com/contact/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar Kintone,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-kintone-single-sign-on"></a>Configurar kintone Single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no site da empresa **Kintone** como administrador.

1. Clique no **ícone Definições**.

    ![Definições](./media/kintone-tutorial/ic785879.png "Definições")

1. Clique **& Administração do Sistema dos Utilizadores.**

    ![Utilizadores & Administração do Sistema](./media/kintone-tutorial/ic785880.png "Utilizadores & Administração do Sistema")

1. Em **Segurança da Administração do Sistema \> ** clique em **Iniciarções de Sessão**.

    ![Iniciar sessão](./media/kintone-tutorial/ic785881.png "Iniciar sessão")

1. Clique **em Ativar a autenticação SAML**.

    ![Autenticação SAML](./media/kintone-tutorial/ic785882.png "Autenticação SAML")

1. Na secção de Autenticação SAML, execute os seguintes passos:

    ![Autenticação SAML](./media/kintone-tutorial/ic785883.png "Autenticação SAML")

    a. Na caixa de texto **url de login,** cole o valor do URL de **login** que copiou do portal Azure.

    b. Na caixa de texto **url logout,** cole o valor do **URL logout** que copiou do portal Azure.

    c. Clique **em Procurar** para fazer o upload do seu ficheiro de certificado descarregado a partir do portal Azure.

    d. Clique em **Guardar**.

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a Kintone.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Kintone**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Kintone**.

    ![O link Kintone na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-kintone-test-user"></a>Criar utilizador de teste Kintone

Para permitir que os utilizadores da Azure AD inscrevam-se na Kintone, devem ser a provisionados na Kintone. No caso da Kintone, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para obter uma conta de utilizador, execute os seguintes passos:

1. Inscreva-se no site da sua empresa **Kintone** como administrador.

1. Clique no **ícone Definições**.

    ![Definições](./media/kintone-tutorial/ic785879.png "Definições")

1. Clique **& Administração do Sistema dos Utilizadores.**

    ![Administração do sistema & de utilizador](./media/kintone-tutorial/ic785880.png "Administração do sistema & de utilizador")

1. Sob **a Administração do Utilizador,** clique em **Departamentos & Utilizadores**.

    ![Utilizadores do Departamento &](./media/kintone-tutorial/ic785888.png "Utilizadores do Departamento &")

1. Clique **em Novo Utilizador**.

    ![Novos Utilizadores](./media/kintone-tutorial/ic785889.png "Novos Utilizadores")

1. Na secção **Novo Utilizador,** execute os seguintes passos:

    ![Novos Utilizadores](./media/kintone-tutorial/ic785890.png "Novos Utilizadores")

    a. Digite um **Nome de Exibição**, Nome de Início de **Sessão**, **Nova Palavra-passe**, **Confirme a Palavra-passe,** Endereço de **e-mail**e outros detalhes de uma conta AD válida Azure que pretende apresentar nas caixas de texto relacionadas.

    b. Clique em **Guardar**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador Kintone ou APIs fornecidas pela Kintone para fornecer contas de utilizador Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Kintone no Painel de Acesso, deverá ser automaticamente inscrito no Kintone para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
