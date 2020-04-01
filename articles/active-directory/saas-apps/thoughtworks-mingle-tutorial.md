---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Thoughtworks Mingle Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Thoughtworks Mingle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: a12d4dca61734275ef0e56dfe2a800c64dc52540
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233297"
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Tutorial: Integração de Diretório Ativo Azure com a Thoughtworks Mingle

Neste tutorial, aprende-se a integrar a Thoughtworks Mingle with Azure Ative Directory (Azure AD).
Integrar a Thoughtworks Mingle com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Thoughtworks Mingle.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Thoughtworks Mingle (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Thoughtworks Mingle, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Thoughtworks Mingle única subscrição ativada por sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Thoughtworks Mingle apoia **SP** iniciado SSO

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Adicionar Thoughtworks Mingle da galeria

Para configurar a integração da Thoughtworks Mingle em Azure AD, você precisa adicionar Thoughtworks Mingle da galeria à sua lista de aplicações geridas saaS.

**Para adicionar Thoughtworks Mingle a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva Thoughtworks Mingle**, selecione **Thoughtworks Mingle** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Thoughtworks Mingle na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com a Thoughtworks Mingle com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Thoughtworks Mingle.

Para configurar e testar o único sign-on azure ad com a Thoughtworks Mingle, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure Thoughtworks Mingle Single Sign-On](#configure-thoughtworks-mingle-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Thoughtworks Mingle test user](#create-thoughtworks-mingle-test-user)** - para ter uma contrapartida de Britta Simon em Thoughtworks Mingle que está ligada à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com a Thoughtworks Mingle, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Thoughtworks Mingle,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Thoughtworks Mingle Domain e URLs informações únicas de inscrição](common/sp-signonurl.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte a equipa de apoio ao [Cliente Da Mingle Para](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support) obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na secção "Mistura de **Pensamentos",** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-thoughtworks-mingle-single-sign-on"></a>Configure Thoughtworks Mingle Single Sign-On

1. Inscreva-se no site da empresa **Thoughtworks Mingle** como administrador.

2. Clique no separador **'Administrador'** e, em seguida, clique em **SSO Config**.
   
    ![Separador de administrador](./media/thoughtworks-mingle-tutorial/ic785157.png "SSO Config")

3. Na secção **SSO Config,** execute os seguintes passos:
   
    ![SSO Config](./media/thoughtworks-mingle-tutorial/ic785158.png "SSO Config")
    
    a. Para fazer o upload do ficheiro de metadados, clique em **Escolher ficheiro**. 

    b. Clique em **Guardar Alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **User name** tipo brittasimon@yourcompanydomain.extensionde campo do nome do utilizador . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso à Thoughtworks Mingle.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Thoughtworks Mingle**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Thoughtworks Mingle**.

    ![O link Thoughtworks Mingle na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-thoughtworks-mingle-test-user"></a>Criar o utilizador do teste De Mistura thoughtworks

Para que os utilizadores da AD Azure possam iniciar o seu início, devem ser aprovisionados na aplicação Thoughtworks Mingle utilizando os seus nomes de utilizadores do Diretório Ativo Azure. No caso da Thoughtworks Mingle, o provisionamento é uma tarefa manual.

**Para configurar o fornecimento do utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa Thoughtworks Mingle como administrador.

2. Clique no **Perfil**.
   
    ![O seu primeiro projeto](./media/thoughtworks-mingle-tutorial/ic785160.png "O seu primeiro projeto")

3. Clique no separador **'Administrador'** e, em seguida, clique em **Utilizadores**.
   
    ![Utilizadores](./media/thoughtworks-mingle-tutorial/ic785161.png "Utilizadores")

4. Clique em **Novo Utilizador**.
   
    ![Novo Utilizador](./media/thoughtworks-mingle-tutorial/ic785162.png "Novo Utilizador")

5. Na página de diálogo **new user,** execute os seguintes passos:
   
    ![Novo diálogo do utilizador](./media/thoughtworks-mingle-tutorial/ic785163.png "Novo Utilizador")  
 
    a. Digite o **nome De acesso,** **nome de visualização,** **escolha palavra-passe,** **confirme a palavra-passe** de uma conta Azure AD válida que pretende fornecer nas caixas de texto relacionadas. 

    b. Como **tipo utilizador,** selecione **Utilizador completo**.

    c. Clique **em Criar este perfil**.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador da Thoughtworks Mingle ou APIs fornecidas pela Thoughtworks Mingle para fornecer contas de utilizadores da AD Azure.
> 

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Thoughtworks Mingle no Painel de Acesso, deve ser automaticamente inscrito no Thoughtworks Mingle para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

