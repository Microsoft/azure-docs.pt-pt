---
title: 'Tutorial: Integração do Diretório Ativo Azure com Tela [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Canvas.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: bfed291c-a33e-410d-b919-5b965a631d45
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3ce5cff0de2939c25400d1d63138b23bc6c9822
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74232039"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas"></a>Tutorial: Integração do Diretório Ativo Azure com Tela

Neste tutorial, aprende-se a integrar a Canvas com o Azure Ative Directory (Azure AD).
Integrar tela com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Canvas.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Canvas (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Canvas, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por inscrição única de tela

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Tela apoia **SP** iniciado SSO

## <a name="adding-canvas-from-the-gallery"></a>Adicionando Tela da galeria

Para configurar a integração da Canvas no Azure AD, precisa de adicionar tela da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Tela da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **tela,** selecione **Canvas** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Tela na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com tela com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Canvas.

Para configurar e testar o único sinal de Azure AD com tela, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o sinal único](#configure-canvas-single-sign-on)** da tela - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Criar o utilizador](#create-canvas-test-user)** de teste de Tela - para ter uma contrapartida de Britta Simon em Tela que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com tela, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **da Canvas,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Domínio de lona e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<tenant-name>.instructure.com`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<tenant-name>.instructure.com/saml2`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de apoio ao [Cliente da Canvas](https://community.canvaslms.com/community/help) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na secção Certificado de **Assinatura SAML,** clique no botão **Editar** para abrir o diálogo do Certificado de **Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

6. Na secção **Certificado de Assinatura SAML,** **copie** a IMPRESSÃO DIGITAL e guarde-a no seu computador.

    ![Copiar valor de impressão digital](common/copy-thumbprint.png)

7. Na secção **De Configurar** tela, copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-canvas-single-sign-on"></a>Configure inscrição única de tela

1. Numa janela diferente do navegador web, inicie sessão no site da sua empresa canvas como administrador.

2. Ir a **Cursos Contas \> Geridas \> Microsoft**.

    ![Telas](./media/canvas-lms-tutorial/ic775990.png "Telas")

3. No painel de navegação à esquerda, **selecione Autenticação**, e, em seguida, clique em **Adicionar Novo Config SAML**.

    ![Autenticação](./media/canvas-lms-tutorial/ic775991.png "Autenticação")

4. Na página de Integração Atual, execute os seguintes passos:

    ![Integração Atual](./media/canvas-lms-tutorial/ic775992.png "Integração Atual")

    a. Na caixa de texto **ID Entity ID,** colhe o valor do **Identificador Azure Ad** que copiou do portal Azure.

    b. No **Log On URL** textbox, colhe o valor do URL de **Login** que copiou do portal Azure .

    c. Na caixa de texto **URL log out,** colá o valor do URL de **Logout** que copiou do portal Azure.

    d. Na caixa de texto **Change Password Link,** colá o valor do URL de **Palavra-passe** de alteração que copiou do portal Azure.

    e. Na caixa de texto **Impressões Digitais de Certificado,** cola o valor de **impressão digital** do certificado que copiou do portal Azure.

    f. A partir da lista de **Atributos de Login,** selecione **NameID**.

    g. A partir da lista de **Formato identificador,** **selecione emailAddress**.

    h. Clique em **guardar definições de autenticação**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **tipo** de campo de nome utilizador **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso à Tela.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Canvas**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Canvas**.

    ![O link de Tela na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-canvas-test-user"></a>Criar o utilizador do teste de Tela

Para permitir que os utilizadores de Anúncios Azure entrem em Canvas, devem ser aprovisionados em Tela. No caso da Tela, o fornecimento do utilizador é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Faça login no seu inquilino de **Lona.**

2. Ir a **Cursos Contas \> Geridas \> Microsoft**.

   ![Telas](./media/canvas-lms-tutorial/ic775990.png "Telas")

3. Clique em **Utilizadores**.

   ![Utilizadores](./media/canvas-lms-tutorial/ic775995.png "Utilizadores")

4. Clique em **adicionar novo utilizador**.

   ![Utilizadores](./media/canvas-lms-tutorial/ic775996.png "Utilizadores")

5. Na página adicionar uma nova página de diálogo do Utilizador, execute os seguintes passos:

   ![Adicionar Utilizador](./media/canvas-lms-tutorial/ic775997.png "Adicionar Utilizador")

   a. Na caixa de texto **Nome Completo,** introduza o nome de utilizador como **BrittaSimon**.

   b. Na caixa de texto **por e-mail,** introduza o e-mail de utilizador como **o\@brittasimon contoso.com**.

   c. Na caixa de texto **Login,** introduza o endereço de e-mail Azure AD do utilizador como **\@o brittasimon contoso.com**.

   d. Selecione **Enviar por email o utilizador sobre a criação desta conta.**

   e. Clique em **Adicionar Utilizador**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador de Lona ou APIs fornecidas pela Canvas para fornecer contas de utilizador da Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo de tela no Painel de Acesso, deve ser automaticamente inscrito na Tela para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

