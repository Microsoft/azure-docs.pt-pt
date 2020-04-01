---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Picturepark Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Picturepark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 08c5bd8da0dda74156b2d44c8106ed345ef749dd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73177014"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Tutorial: Integração do Diretório Ativo Azure com o Picturepark

Neste tutorial, aprende-se a integrar o Picturepark com o Azure Ative Directory (Azure AD).
Integrar o Picturepark com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Picturepark.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Picturepark (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Picturepark, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura de inscrição única do Picturepark

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Picturepark suporta **SP** iniciado SSO

## <a name="adding-picturepark-from-the-gallery"></a>Adicionando Picturepark da galeria

Para configurar a integração do Picturepark em Azure AD, precisa de adicionar o Picturepark da galeria à sua lista de aplicações geridas do SaaS.

**Para adicionar o Picturepark da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **O Picturepark**, selecione **Picturepark** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Picturepark na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o single sign-on azure com o Picturepark com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Picturepark.

Para configurar e testar o único sign-on azure ad com o Picturepark, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Picturepark Single Sign-On](#configure-picturepark-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Picturepark test user](#create-picturepark-test-user)** - para ter uma contrapartida de Britta Simon no Picturepark que está ligada à representação do utilizador azure AD.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com o Picturepark, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **do Picturepark,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Picturepark Domain e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<companyname>.picturepark.com`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:

    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de apoio ao [Cliente do Picturepark](https://picturepark.com/company/picturepark-customer-support) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na secção Certificado de **Assinatura SAML,** clique no botão **Editar** para abrir o diálogo do Certificado de **Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

6. Na secção certificado de **assinatura SAML,** copie a **impressão digital** e guarde-a no seu computador.

    ![Copiar valor de impressão digital](common/copy-thumbprint.png)

7. Na secção Configurar o **Picturepark,** copie os URL(s) adequados de acordo com o seu requisito. Para **o URL de login,** utilize o valor com o seguinte padrão:`https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ é a identidade de inquilino da assinatura azure AD.

    ![URLs de configuração de cópia](./media/picturepark-tutorial/configurls.png)

    a. Identificador Azure AD

    b. Logout URL

### <a name="configure-picturepark-single-sign-on"></a>Configure Picturepark Single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa do Picturepark como administrador.

2. Na barra de ferramentas na parte superior, clique em **ferramentas administrativas,** e depois clique na Consola de **Gestão**.
   
    ![Consola de gestão](./media/picturepark-tutorial/ic795062.png "Consola de gestão")

3. Clique em **Autenticação**e, em seguida, clique em **fornecedores de identidade**.
   
    ![Autenticação](./media/picturepark-tutorial/ic795063.png "Autenticação")

4. Na secção de configuração do **fornecedor de identidade,** execute os seguintes passos:
   
    ![Configuração do fornecedor de identidade](./media/picturepark-tutorial/ic795064.png "Configuração do fornecedor de identidade")
   
    a. Clique em **Adicionar**.
  
    b. Digite um nome para a sua configuração.
   
    c. Selecione **Definir como padrão**.
   
    d. Na caixa de texto **Emitr URI,** colhe o valor do URL de **Login** que copiou do portal Azure.
   
    e. Na caixa de texto **de impressão digital do emitente fidedigno,** colá o valor da impressão **digital** que copiou da secção de certificado de **assinatura SAML.** 

5. Clique em **UnirDefaultUsersGroup**.

6. Para definir o atributo do endereço de `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` **e-mail** na caixa de texto **claim,** escreva e clique em **Guardar**.

      ![Configuração](./media/picturepark-tutorial/ic795065.png "Configuração")

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **User name** tipo `brittasimon@yourcompanydomain.extension`de campo do nome do utilizador . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao Picturepark.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Picturepark**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Picturepark**.

    ![O link Picturepark na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-picturepark-test-user"></a>Criar o utilizador de teste picturepark

Para permitir que os utilizadores da AD Azure assinem no Picturepark, devem ser aprovisionados no Picturepark. No caso do Picturepark, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu inquilino **do Picturepark.**

1. Na barra de ferramentas na parte superior, clique em **ferramentas administrativas**e, em seguida, clique em **Utilizadores**.
   
    ![Utilizadores](./media/picturepark-tutorial/ic795067.png "Utilizadores")

1. No separador visão geral dos **Utilizadores,** clique em **New**.
   
    ![Gestão de utilizadores](./media/picturepark-tutorial/ic795068.png "Gestão de utilizadores")

1. No diálogo **Create User,** execute os seguintes passos de um utilizador de diretório ativo Azure válido que pretende fornecer:
   
    ![Criar o Utilizador](./media/picturepark-tutorial/ic795069.png "Criar Utilizador")
   
    a. Na caixa de texto endereço de **e-mail,** escreva o endereço de **e-mail** do utilizador `BrittaSimon@contoso.com`.  
   
    b. Nas **caixas** de texto Password e **Confirme,** escreva a **palavra-passe** da BrittaSimon. 
   
    c. Na caixa de texto **First Name,** digite o **Primeiro Nome** do utilizador **Britta**. 
   
    d. Na caixa de texto **Sobre Nome,** digite o **Último Nome** do utilizador **Simon**.
   
    e. Na caixa de texto **da Empresa,** digite o nome da **Empresa** do utilizador. 
   
    f. Na caixa de texto **Country,** selecione o **País/Região** do utilizador.
  
    g. Na caixa de texto **ZIP,** digite o **código POSTAL** da cidade.
   
    h. Na caixa de texto **da Cidade,** digite o **nome city** do utilizador.

    i. Selecione um **Idioma**.
   
    j. Clique em **Criar**.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador do Picturepark ou APIs fornecidas pelo Picturepark para fornecer contas de utilizadores da Azure AD.
>

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Picturepark no Painel de Acesso, deve ser automaticamente inscrito no Picturepark para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

