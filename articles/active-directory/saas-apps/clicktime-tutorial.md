---
title: 'Tutorial: Integração do Azure Ative Directory com o ClickTime Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o ClickTime.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2019
ms.author: jeedes
ms.openlocfilehash: c184d4758d6435659d737c11904357711f4f0a36
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455878"
---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Tutorial: Integração do Diretório Ativo Azure com o ClickTime

Neste tutorial, aprende a integrar o ClickTime com o Azure Ative Directory (Azure AD).
A integração do ClickTime com a AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso ao ClickTime.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no ClickTime (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD do Azure com o ClickTime, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por clickTime

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* ClickTime suporta SSO iniciado pelo **IDP**

## <a name="adding-clicktime-from-the-gallery"></a>Adicionar ClickTime da galeria

Para configurar a integração do ClickTime no AD Azure, é necessário adicionar o ClickTime da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar ClickTime na galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **ClickTime**, selecione **ClickTime** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![ClickTime na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o ClickTime com base num utilizador de teste chamado **Britta Simon**.
Para um único s-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no ClickTime.

Para configurar e testar o Azure AD com o ClickTime, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-on único do ClickTime](#configure-clicktime-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create ClickTime test user](#create-clicktime-test-user)** - para ter uma contraparte de Britta Simon no ClickTime que está ligada à representação AD AZure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o ClickTime, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **ClickTime,** selecione **'Único s-on'.**

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na **configuração single Sign-On com página SAML,** execute os seguintes passos:

    ![Informações de súmis de clickTime e URLs](common/idp-intiated.png)

    a. Na caixa de texto **identifier,** digite um URL: `https://app.clicktime.com/sp/`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:
    
    ```http
    https://app.clicktime.com/Login/
    https://app.clicktime.com/App/Login/Consume.aspx
    ```

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar clickTime,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-clicktime-single-sign-on"></a>Configurar clicktime single Sign-On

1. Numa janela diferente do navegador web, inicie sessão no site da empresa ClickTime como administrador.

1. Na barra de ferramentas na parte superior, clique em **Preferências**e, em seguida, clique em **Definições de Segurança**.

1. Na secção de configuração **De Preferências de Sign-On Única,** execute os seguintes passos:
   
    ![Definições de segurança](./media/clicktime-tutorial/tic777280.png "Definições de Segurança")
   
    a.  Selecione **Permitir o** acesso ao Sign-On único (SSO) com **Azure AD**.
   
    b. Na caixa de texto do **Fornecedor de Identidade Endpoint,** cole **URL de login** que copiou do portal Azure.
   
    c.  Abra o **certificado codificado base-64** descarregado do portal Azure no Bloco de **Notas,** copie o conteúdo e, em seguida, cole-o na caixa de texto **do Certificado X.509.**
   
    d.  Clique em **Guardar**.

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

Nesta secção, você permite que Britta Simon utilize o Azure single sign-on, concedendo acesso ao ClickTime.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **ClickTime**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **ClickTime**.

    ![O link ClickTime na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-clicktime-test-user"></a>Criar utilizador de teste ClickTime

Para permitir que os utilizadores de Azure AD acedam ao ClickTime, devem ser a provisionados no ClickTime.  
No caso do ClickTime, o provisionamento é uma tarefa manual.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador clickTime ou APIs fornecidas pelo ClickTime para fornecer contas de utilizador Azure AD.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Faça login no seu inquilino **ClickTime.**

1. Na barra de ferramentas no topo, clique em **'Empresa'** e, em seguida, clique em **People**.
   
    ![A screenshot mostra o inquilino ClickTime com empresa e pessoas selecionadas.](./media/clicktime-tutorial/tic777282.png "People")

1. Clique **em Adicionar Pessoa.**
   
    ![Adicionar Pessoa](./media/clicktime-tutorial/tic777283.png "Adicionar Pessoa")

1. Na secção Pessoa Nova, execute os seguintes passos:
   
    ![A screenshot mostra a secção Add Person onde pode adicionar as informações neste passo.](./media/clicktime-tutorial/tic777284.png "People")
   
    a.  Na caixa de texto **com nome completo,** escreva o nome completo de um utilizador como **Britta Simon**. 
  
    b.  Na caixa de texto do **endereço de e-mail,** escreva o e-mail do utilizador como ** \@ brittasimon contoso.com**.
       
    > [!NOTE]
    > Se quiser, pode definir propriedades adicionais do objeto da nova pessoa.
   
    c.  Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo ClickTime no Painel de Acesso, deverá ser automaticamente inscrito no ClickTime para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)