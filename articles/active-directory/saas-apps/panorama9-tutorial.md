---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Panorama9 [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Panorama9.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5e28d7fa-03be-49f3-96c8-b567f1257d44
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: c88088b804f90769512d33a5e96fdedc5ce6fba8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67094917"
---
# <a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Tutorial: Integração do Diretório Ativo Azure com o Panorama9

Neste tutorial, aprende-se a integrar o Panorama9 com o Azure Ative Directory (Azure AD).
Integrar o Panorama9 com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Panorama9.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Panorama9 (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Panorama9, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de assinatura de inscrição única panorama9

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Panorama9 apoia **SP** iniciado SSO

## <a name="adding-panorama9-from-the-gallery"></a>Adicionando Panorama9 da galeria

Para configurar a integração do Panorama9 em Azure AD, precisa de adicionar panorama9 da galeria à sua lista de aplicações geridas do SaaS.

**Para adicionar Panorama9 da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Panorama9**, selecione **Panorama9** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Panorama9 na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com panorama9 com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Panorama9.

Para configurar e testar o único sinal de Azure AD com o Panorama9, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Panorama9 Single Sign-On](#configure-panorama9-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador](#create-panorama9-test-user)** de teste Panorama9 - para ter uma contrapartida de Britta Simon no Panorama9 que está ligada à representação do utilizador azure AD.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure com o Panorama9, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Panorama9,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Panorama9 Domain e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL:`https://dashboard.panorama9.com/saml/access/3262`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://www.panorama9.com/saml20/<tenant-name>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de apoio ao [cliente panorama9](https://support.panorama9.com/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na secção Certificado de **Assinatura SAML,** clique no botão **Editar** para abrir o diálogo do Certificado de **Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

6. Na secção certificado de **assinatura SAML,** copie a **impressão digital** e guarde-a no seu computador.

    ![Copiar valor de impressão digital](common/copy-thumbprint.png)

7. Na secção **Configurar panorama9,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-panorama9-single-sign-on"></a>Configure Panorama9 Single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa Panorama9 como administrador.

2. Na barra de ferramentas por cima, clique em **Gerir**, e depois clique em **Extensões**.
   
    ![Extensões](./media/panorama9-tutorial/ic790023.png "Extensões")

3. No diálogo de **extensões,** clique em **Iniciar sessão individual**.
   
    ![Inscrição única](./media/panorama9-tutorial/ic790024.png "Início de Sessão Único")

4. Na secção **Definições,** execute os seguintes passos:
   
    ![Definições](./media/panorama9-tutorial/ic790025.png "Definições")
   
    a. Na caixa de texto URL do fornecedor de **identidade,** colá o valor do URL de **Login,** que copiou do portal Azure.
   
    b. Na caixa de texto **impressões digitais de certificado,** cola o valor de **impressão digital** do certificado, que copiou do portal Azure.    
         
5. Clique em **Guardar**.

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao Panorama9.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Panorama9**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Panorama9**.

    ![O link Panorama9 na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-panorama9-test-user"></a>Criar o utilizador de teste Panorama9

Para permitir que os utilizadores da AD Azure assinem o Panorama9, devem ser aprovisionados no Panorama9.  

No caso do Panorama9, o provisionamento é uma tarefa manual.

**Para configurar o fornecimento do utilizador, execute os seguintes passos:**

1. Inscreva-se no site da sua empresa **Panorama9** como administrador.

2. No menu em cima, clique em **Gerir**, e depois clique em **Utilizadores**.
   
    ![Utilizadores](./media/panorama9-tutorial/ic790027.png "Utilizadores")

3. Na secção Utilizadores, **+** Clique para adicionar um novo utilizador.

    ![Utilizadores](./media/panorama9-tutorial/ic790028.png "Utilizadores")

4. Vá à secção de dados do Utilizador, escreva o endereço de e-mail de um utilizador de Diretório Ativo Azure válido que pretende fornecer na caixa de texto de **email.**

5. Venha para a secção Utilizadores, clique em **Guardar**.
   
    > [!NOTE]
    > O titular da conta Azure Ative Directory recebe um e-mail e segue um link para confirmar a sua conta antes de se tornar ativo.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Panorama9 no Painel de Acesso, deve ser automaticamente inscrito no Panorama9 para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

