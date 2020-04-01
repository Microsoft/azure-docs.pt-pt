---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Silverback Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Silverback.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 32cfc96f-2137-49ff-818b-67feadcd73b7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 3c4eab02ed0c7c09fe9b5893bbaaf7cbe1c8028f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090917"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>Tutorial: Integração do Diretório Ativo Azure com a Silverback

Neste tutorial, aprende-se a integrar o Silverback com o Azure Ative Directory (Azure AD).
Integrar silverback com Azure AD proporciona-lhe os seguintes benefícios:

* Podes controlar em Azure AD quem tem acesso a Silverback.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Silverback (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Silverback, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de sinal único silverback

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Silverback apoia **SP** iniciado SSO

## <a name="adding-silverback-from-the-gallery"></a>Adicionando Silverback da galeria

Para configurar a integração da Silverback em Azure AD, você precisa adicionar Silverback da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Silverback da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Silverback**, selecione **Silverback** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Silverback na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com silverback com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Silverback.

Para configurar e testar o único sinal de Azure AD com silverback, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Single Silverback](#configure-silverback-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Silverback test user](#create-silverback-test-user)** - para ter uma contrapartida de Britta Simon em Silverback que está ligada à representação azure AD do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure com silverback, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Silverback,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Silverback Domain e URLs informações únicas de inscrição](common/sp-identifier-reply.png)

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<YOURSILVERBACKURL>.com/ssp`

    b. Na caixa **de identificador,** digite um URL utilizando o seguinte padrão:`<YOURSILVERBACKURL>.com`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<YOURSILVERBACKURL>.com/sts/authorize/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL, Identificador e Resposta real. Contacte a equipa de apoio ao [Cliente Silverback](mailto:helpdesk@matrix42.com) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração do Single Sign-On com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

### <a name="configure-silverback-single-sign-on"></a>Configure Silverback Single Sign-On

1. Num navegador web diferente, inicie sessão no seu Silverback Server como Administrador.

2. Navegue para o Fornecedor de**Autenticação** **de Administrador.** > 

3. Na página Definições do **Fornecedor de Autenticação,** execute os seguintes passos:

    ![O administrador](./media/silverback-tutorial/tutorial_silverback_admin.png)

    a.  Clique em **Importar a partir de URL**.

    b.  Colhe o URL de Metadados copiado e clique **OK**.

    c.  Confirme com **OK** então os valores serão povoados automaticamente.

    d.  Ativar **a exibição na página de login**.

    e.  Ativar a **Criação dinâmica do utilizador** se pretender adicionar automaticamente os utilizadores autorizados do Azure AD (opcional).

    f.  Crie um **Título** para o botão no Portal do Self Service.

    g.  Faça upload de um **Ícone** clicando no **Ficheiro Escolha**.

    h.  Selecione a **cor** de fundo para o botão.

    i.  Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No tipo de campo de **nome do utilizador****brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso à Silverback.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Silverback**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Silverback**.

    ![O link Silverback na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-silverback-test-user"></a>Criar o utilizador de teste Silverback

Para permitir que os utilizadores de Anúncios Azure entrem na Silverback, devem ser aprovisionados em Silverback. Em Silverback, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu Silverback Server como administrador.

2. Navegue aos **Utilizadores** e adicione um novo utilizador de **dispositivos.**

3. Na página **Basic,** execute os seguintes passos:

    ![O utilizador](./media/silverback-tutorial/tutorial_silverback_user.png)

    a. Na caixa de texto **Username,** introduza o nome de utilizador como **Britta**.

    b. Na caixa de texto **First Name,** introduza o primeiro nome de utilizador como **Britta**.

    c. Na caixa de texto **De Apelido,** introduza o último nome de utilizador como **Simon**.

    d. Na caixa de texto Endereço de endereço **Brittasimon@contoso.com**de **e-mail,** introduza o e-mail do utilizador como .

    e. Na caixa de texto **Password,** introduza a sua palavra-passe.

    f. Na caixa de texto **Confirmar Password,** reintroduza a sua palavra-passe e confirme.

    g. Clique em **Guardar**.

> [!NOTE]
> Se não pretender criar cada utilizador manualmente, ative a caixa de verificação de **criação dinâmica** do utilizador no âmbito do Fornecedor de**Autenticação** **de Administrador** > .

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Silverback no Painel de Acesso, deve ser automaticamente inscrito no Silverback para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

