---
title: 'Tutorial: Integração do Azure Ative Directory com o Picturepark Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Picturepark.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: bb12b666315f6d30df64b33b6a87d6ce831fd225
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88553860"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Tutorial: Integração do Diretório Ativo Azure com o Picturepark

Neste tutorial, aprende-se a integrar o Picturepark com o Azure Ative Directory (Azure AD).
A integração do Picturepark com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Picturepark.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Picturepark (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o Picturepark, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura ativada única do Picturepark

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Picturepark suporta **SSO** iniciado SP

## <a name="adding-picturepark-from-the-gallery"></a>Adicionar Picturepark da galeria

Para configurar a integração do Picturepark no Azure AD, é necessário adicionar o Picturepark da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar o Picturepark da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Picturepark**, selecione **Picturepark** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![Picturepark na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Picturepark com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Picturepark.

Para configurar e testar o Azure AD com um único sinal de acesso com o Picturepark, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On único do Picturepark](#configure-picturepark-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Picturepark test user](#create-picturepark-test-user)** - ter uma contraparte de Britta Simon no Picturepark que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o Picturepark, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Picturepark,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Informações de súmis de picturepark e URLs](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<companyname>.picturepark.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão:

    ```http
        https://<companyname>.current-picturepark.com
        https://<companyname>.picturepark.com
        https://<companyname>.next-picturepark.com
    ```

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente Picturepark](https://picturepark.com/company/picturepark-customer-support) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na secção **Certificado de Assinatura SAML,** clique em Editar o botão **Editar** para abrir o diálogo **do Certificado de Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

6. Na secção **certificado de assinatura SAML,** copie a **impressão digital e** guarde-a no seu computador.

    ![Valor da impressão digital do polegar da cópia](common/copy-thumbprint.png)

7. Na secção Configurar o **Picturepark,** copie os URL(s) apropriados de acordo com o seu requisito. Para **URL de início de sessão,** utilize o valor com o seguinte padrão: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ é o id de inquilino da assinatura AD Azure.

    ![URLs de configuração de cópia](./media/picturepark-tutorial/configurls.png)

    a. Identificador de Azure Ad

    b. Logout URL

### <a name="configure-picturepark-single-sign-on"></a>Configurar picturepark single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Picturepark como administrador.

2. Na barra de ferramentas no topo, clique em **ferramentas administrativas**e, em seguida, clique em **Consola de Gestão**.
   
    ![Consola de Gestão](./media/picturepark-tutorial/ic795062.png "Consola de Gestão")

3. Clique **em Autenticação**e, em seguida, clique em **Fornecedores de Identidade**.
   
    ![Autenticação](./media/picturepark-tutorial/ic795063.png "Autenticação")

4. Na secção **de configuração do fornecedor de identidade,** execute os seguintes passos:
   
    ![Configuração do fornecedor de identidade](./media/picturepark-tutorial/ic795064.png "Configuração do fornecedor de identidade")
   
    a. Clique em **Adicionar**.
  
    b. Digite um nome para a sua configuração.
   
    c. Selecione **Definir como padrão**.
   
    d. Na caixa de texto **do Emitente URI,** cole o valor do URL de **Login** que copiou do portal Azure.
   
    e. Na caixa de texto **de impressão de polegar fidedigno,** cole o valor da impressão **digital** thumbprint que copiou da secção certificado de **assinatura SAML.** 

5. Clique **em JoinDefaultUsersGroup**.

6. Para definir o atributo **emailaddress** na caixa de texto **'Reclamar',** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` e clique em **Guardar**.

      ![Configuração](./media/picturepark-tutorial/ic795065.png "Configuração")

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao Picturepark.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Picturepark**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Picturepark**.

    ![O link Picturepark na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-picturepark-test-user"></a>Criar utilizador de teste picturepark

Para permitir que os utilizadores de Azure AD assinem no Picturepark, devem ser a provisionados no Picturepark. No caso do Picturepark, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu inquilino **do Picturepark.**

1. Na barra de ferramentas no topo, clique em **ferramentas administrativas**e, em seguida, clique em **Utilizadores**.
   
    ![Utilizadores](./media/picturepark-tutorial/ic795067.png "Utilizadores")

1. No separador visão geral dos **Utilizadores,** clique em **Novo**.
   
    ![Gestão de utilizadores](./media/picturepark-tutorial/ic795068.png "Gestão de utilizadores")

1. No diálogo **Criar** Utilizador, execute os seguintes passos de um Utilizador de Diretório Ativo Azure válido que pretende prever:
   
    ![Criar Utilizador](./media/picturepark-tutorial/ic795069.png "Criar Utilizador")
   
    a. Na caixa de texto **do Endereço de E-mail,** digite o endereço de **e-mail** do utilizador `BrittaSimon@contoso.com` .  
   
    b. Nas **caixas** de texto password e **confirme palavra-passe,** digite a **palavra-passe** de BrittaSimon. 
   
    c. Na caixa de texto **Name Name,** digite o **Primeiro Nome** do utilizador **Britta**. 
   
    d. Na caixa de texto **do último nome,** digite o **Apelido** do utilizador **Simon**.
   
    e. Na caixa de texto **da Empresa,** **digite** o nome da Empresa do utilizador. 
   
    f. Na caixa de texto **Country,** selecione o **País/Região** do utilizador.
  
    exemplo, Na caixa de texto **ZIP,** digite o **código POSTAL** da cidade.
   
    h. Na caixa de texto da **Cidade,** digite o **nome City** do utilizador.

    i. Selecione um **idioma**.
   
    j. Clique em **Criar**.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador do Picturepark ou APIs fornecidas pelo Picturepark para fornecer contas de utilizador Azure AD.
>

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Picturepark no Painel de Acesso, deverá ser automaticamente inscrito no Picturepark para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

