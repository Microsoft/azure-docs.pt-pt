---
title: 'Tutorial: Integração do Diretório Ativo Azure com peakon [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Peakon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a944c397-ed3f-4d45-b9b2-6d4bcb6b0a09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: b093a26848701254ad674081037c266f1fb012b2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67094725"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Tutorial: Integração do Diretório Ativo Azure com peakon

Neste tutorial, aprende-se a integrar peakon com o Azure Ative Directory (Azure AD).
Integrar peakon com Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso a Peakon.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Peakon (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com peakon, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por sinal único peakon

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Peakon suporta **SP** e **IDP** iniciadoS SSO

## <a name="adding-peakon-from-the-gallery"></a>Adicionando Peakon da galeria

Para configurar a integração de Peakon em Azure AD, você precisa adicionar Peakon da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Peakon da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Peakon**, selecione **Peakon** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Peakon na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sinal de Azure AD com Peakon com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Peakon.

Para configurar e testar o único sinal de Azure AD com peakon, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único peakon](#configure-peakon-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Peakon test user](#create-peakon-test-user)** - para ter uma contrapartida de Britta Simon em Peakon que está ligada à representação azure AD do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure com peakon, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Peakon,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado **idp,** execute os seguintes passos:

    ![Peakon Domain e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://app.peakon.com/saml/<companyid>/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://app.peakon.com/saml/<companyid>/assert`

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    ![Peakon Domain e URLs informações únicas de inscrição](common/metadata-upload-additional-signon.png)

    Na caixa de texto **de URL sign-on,** escreva um URL:`https://app.peakon.com/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Identificação e Resposta real, o que é explicado mais tarde no tutorial. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

6. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Raw)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificateraw.png)

7. Na secção **set up Peakon,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-peakon-single-sign-on"></a>Configure Peakon Single Sign-On

1. Numa janela de navegador web diferente, inscreva-se em Peakon como administrador.

2. Na barra de menu sintetizador do lado esquerdo da página, clique em **Configuração,** depois navegue para **integrações**.

    ![O Config](./media/peakon-tutorial/tutorial_peakon_config.png)

3. Na página **Integrações,** clique em **Single Sign-On**.

    ![O Single](./media/peakon-tutorial/tutorial_peakon_single.png)

4. Na secção **De Sessão Individual,** clique em **Ativar**.

    ![O habilitador](./media/peakon-tutorial/tutorial_peakon_enable.png)

5. No único sinal para os empregados que utilizem a secção **SAML,** execute os seguintes passos:

    ![O saml](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. Na caixa de texto **URL sSO Login,** colá o valor do URL de **Login,** que copiou do portal Azure.

    b. Na caixa de texto **SSO Logout URL,** colá o valor do URL de **Logout,** que copiou do portal Azure.

    c. Clique em **Escolher ficheiro** para fazer o upload do certificado que descarregou do portal Azure, para a caixa de Certificado.

    d. Clique no **ícone** para copiar o ID da **entidade** e colar na caixa de texto **identifier** na secção **de configuração Básica SAML** no portal Azure.

    e. Clique no **ícone** para copiar o URL de **resposta (ACS)** e cole na caixa de texto URL de **resposta** na secção **basic SAML Configuração** no portal Azure.

    f. Clique em **Guardar**

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso a Peakon.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Peakon**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Peakon**.

    ![A ligação Peakon na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-peakon-test-user"></a>Criar o utilizador de teste Peakon

Para permitir que os utilizadores de Anúncios Azure entrem em Peakon, devem ser aprovisionados em Peakon.  
No caso de Peakon, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da sua empresa Peakon como administrador.

2. Na barra de menu sintetizador do lado esquerdo da página, clique em **Configuração,** depois navegue para **os Empregados**.

    ![O empregado](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. No lado superior direito da página, clique em **Adicionar empregado**.

      ![O empregado adicionado](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. Na página de diálogo do **novo funcionário,** execute os seguintes passos:

     ![O novo empregado](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. Na caixa de texto **Name,** digite o primeiro nome como **Britta** e sobrenome como **simon**.

    b. Na caixa de texto **por e-mail,** digite o endereço de e-mail como **Brittasimon\@contoso.com**.

    c. Clique em **Criar empregado**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Peakon no Painel de Acesso, deve ser automaticamente inscrito no Peakon para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

