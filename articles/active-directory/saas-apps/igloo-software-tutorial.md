---
title: 'Tutorial: Integração de Diretório Sonérório Ativo Azure com igloo Software [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Igloo Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/06/2019
ms.author: jeedes
ms.openlocfilehash: df1d70f895e2e0a81344cf2a4e8e2d9963c951fa
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67100588"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Tutorial: Integração de Diretório Ativo Azure com igloo Software

Neste tutorial, aprende-se a integrar o Igloo Software com o Azure Ative Directory (Azure AD).
Integrar o Software Igloo com a AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao Software Igloo.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Igloo Software (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Software Igloo, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Igloo Software única subscrição ativada por sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Igloo Software suporta **SP** iniciado SSO
* Igloo Software suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-igloo-software-from-the-gallery"></a>Adicionando Software Igloo da galeria

Para configurar a integração do Software Igloo no Azure AD, é necessário adicionar software Igloo da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar software Igloo a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite software Igloo,** selecione **Igloo Software** a partir do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Software Igloo na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on do Azure AD com o Igloo Software com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Igloo Software.

Para configurar e testar o único sign-on azure ad com o Igloo Software, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Igloo Software Single Sign-On](#configure-igloo-software-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador de teste igloo Software](#create-igloo-software-test-user)** - para ter uma contrapartida da Britta Simon no Software Igloo que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on da Azure AD com o Igloo Software, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **igloo Software,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Igloo Software Domain e URLs informações únicas de inscrição](common/sp-identifier-reply.png)

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<company name>.igloocommmunities.com`

    b. Na caixa **de identificador,** digite um URL utilizando o seguinte padrão:`https://<company name>.igloocommmunities.com/saml.digest`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL, Identificador e Resposta real. Contacte a equipa de suporte do [Cliente igloo](https://www.igloosoftware.com/services/support) software para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **Deconfiguração do Software Igloo,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-igloo-software-single-sign-on"></a>Configure Igloo Software Single Sign-On

1. Numa janela diferente do navegador web, inicie sessão no site da sua empresa Igloo Software como administrador.

2. Vá ao Painel de **Controlo.**

     ![Painel de Controlo](./media/igloo-software-tutorial/ic799949.png "Painel de Controlo")

3. No separador **Demembro,** clique **em Definições de 'Iniciar sessão '.**

    ![Iniciar sessão em Definições](./media/igloo-software-tutorial/ic783968.png "Iniciar sessão em Definições")

4. Na secção de Configuração SAML, clique em **Configurar a Autenticação SAML**.

    ![Configuração SAML](./media/igloo-software-tutorial/ic783969.png "Configuração SAML")

5. Na secção **Configuração Geral,** execute os seguintes passos:

    ![Configuração Geral](./media/igloo-software-tutorial/ic783970.png "Configuração Geral")

    a. Na caixa de texto **'Nome de Ligação',** escreva um nome personalizado para a sua configuração.

    b. Na caixa de texto **IDP Login URL,** colá o valor do URL de **Login** que copiou do portal Azure.

    c. Na caixa de texto **IDP Logout URL,** colá o valor do URL de **Logout** que copiou do portal Azure.

    d. Selecione Resposta de **logout e solicitar http type** como **POST**.

    e. Abra o seu certificado codificado **base-64** no bloco de notas descarregado do portal Azure, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto do **Certificado Público.**

6. Na **Configuração de Resposta e Autenticação,** execute os seguintes passos:

    ![Configuração de resposta e autenticação](./media/igloo-software-tutorial/IC783971.png "Configuração de resposta e autenticação")
  
    a. Como **Fornecedor de Identidade,** selecione **Microsoft ADFS**.

    b. Como **Tipo de Identificador,** selecione **Endereço de e-mail**. 

    c. Na caixa de texto **e-mail Atributo,** digite **endereço de e-mail**.

    d. Na caixa de texto **de primeiro nome Atributo,** digite **o nome dado**.

    e. Na caixa de texto **de último nome Atributo,** escreva **sobrenome**.

7. Execute os seguintes passos para completar a configuração:

    ![Criação do utilizador no Sign in](./media/igloo-software-tutorial/IC783972.png "Criação do utilizador no Sign in") 

    a. Como **criação do Utilizador no Signin ,** selecione **Criar um novo utilizador no seu site quando iniciar a sua inscrição**.

    b. As **Sign in Settings**, selecione Use o **botão SAML no ecrã "Iniciar sessão".**

    c. Clique em **Guardar**.

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

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao Igloo Software.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Igloo Software**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, **selecione Igloo Software**.

    ![O link Igloo Software na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-igloo-software-test-user"></a>Criar o utilizador de teste igloo software

Não existe nenhum item de ação para configurar o fornecimento de utilizadores ao Igloo Software.  

Quando um utilizador designado tenta iniciar sessão no Igloo Software utilizando o painel de acesso, o Igloo Software verifica se o utilizador existe.  Caso ainda não exista uma conta de utilizador disponível, é automaticamente criada pela Igloo Software.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Igloo Software no Painel de Acesso, deve ser automaticamente inscrito no Software Igloo para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)