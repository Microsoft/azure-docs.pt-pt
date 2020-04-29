---
title: 'Tutorial: Integração do Diretório Ativo Azure com a SilkRoad Life Suite [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a SilkRoad Life Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 63165da69815c77afb8692e1e68c1710beb8df8c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67090827"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Tutorial: Integração de Diretório Ativo Azure com A Suite SilkRoad Life

Neste tutorial, aprende-se a integrar a SilkRoad Life Suite com o Azure Ative Directory (Azure AD).
Integrar a SilkRoad Life Suite com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso à SilkRoad Life Suite.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na SilkRoad Life Suite (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da Azure AD com a SilkRoad Life Suite, você precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura de inscrição única da SilkRoad Life Suite

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* SilkRoad Life Suite suporta **SP** iniciado SSO

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Adicionando a Suíte Vida SilkRoad da galeria

Para configurar a integração da SilkRoad Life Suite em Azure AD, você precisa adicionar SilkRoad Life Suite da galeria à sua lista de aplicações geridas saaS.

**Para adicionar a SilkRoad Life Suite da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **silkRoad Life Suite,** selecione **SilkRoad Life Suite** do painel de resultados e, em seguida, clique em adicionar o botão **Adicionar** a aplicação.

    ![SilkRoad Life Suite na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure ad com a SilkRoad Life Suite com base num utilizador de teste chamado **Britta Simon**.
Para um único início de inscrição para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na SilkRoad Life Suite.

Para configurar e testar o único sign-on azure ad com a SilkRoad Life Suite, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure seda Road Life Suite Single Sign-On](#configure-silkroad-life-suite-single-sign-on)** - para configurar as definições de entrada única no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador](#create-silkroad-life-suite-test-user)** de teste SilkRoad Life Suite - para ter uma contrapartida de Britta Simon na SilkRoad Life Suite que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o single sign-on azure ad com silkRoad life suite, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **silkRoad Life Suite,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** se tiver ficheiro de **metadados do Prestador**de Serviços, execute os seguintes passos:

    > [!NOTE]
    > Você terá o ficheiro de metadados do Fornecedor de **Serviços** explicado mais tarde neste tutorial.

    a. Clique no **ficheiro de metadados de upload**.

    ![image](common/upload-metadata.png)

    b. Clique no logotipo da **pasta** para selecionar o ficheiro de metadados e clicar em **Carregar**.

    ![image](common/browse-upload-metadata.png)

    c. Uma vez que o ficheiro de metadados é carregado com sucesso, os valores URL do **Identificador** e **da Resposta** ficam povoados automaticamente na secção de Configuração Básica sAML:

    ![image](common/sp-identifier-reply.png)

    > [!Note]
    > Se os valores de URL do **Identificador** e **da Resposta** não estiverem a ser auto-polerados, preencha os valores manualmente de acordo com o seu requisito.

    d. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<subdomain>.silkroad-eng.com/Authentication/`

5. Na secção **Basic SAML Configuration,** se não tiver ficheiro de **metadados do Prestador**de Serviços, execute os seguintes passos:

    ![SilkRoad Life Suite Domain e URLs informações únicas de inscrição](common/sp-identifier-reply.png)

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<subdomain>.silkroad-eng.com/Authentication/`

    b. Na caixa **de identificador,** digite um URL utilizando o seguinte padrão:

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP`|
    | `https://<subdomain>.silkroad.com/Authentication/SP`|

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/`|
    | `https://<subdomain>.silkroad.com/Authentication/`|

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL, Identificador e Resposta real. Contacte a equipa de suporte do [Cliente SilkRoad Life Suite](https://www.silkroad.com/locations/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

6. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

7. Na secção **set up SilkRoad Life Suite,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-silkroad-life-suite-single-sign-on"></a>Configure SilkRoad Life Suite Single Sign-On

1. Inscreva-se no site da sua empresa SilkRoad como administrador.

    > [!NOTE]
    > Para obter acesso à aplicação de autenticação Da SedaRoad Life Suite para configurar a federação com o Microsoft Azure AD, contacte o SilkRoad Support ou o seu representante dos Serviços SilkRoad.

1. Vá ao Prestador de **Serviços**e clique em **Detalhes da Federação**.

    ![Sinal único azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png)

1. Clique em **Descarregamento de Metadados da Federação**e, em seguida, guarde o ficheiro de metadados no seu computador. Utilize metadados da Federação Descarregado como ficheiro de metadados do Prestador de **Serviços** na secção **de Configuração SAML Básica** no portal Azure.

    ![Sinal único azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png)

1. Na sua aplicação **SilkRoad,** clique em **Fontes de Autenticação**.

    ![Sinal único azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png) 

1. Clique em **Adicionar Fonte de Autenticação**.

    ![Sinal único azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png)

1. Na secção Adicionar Fonte de **Autenticação,** execute os seguintes passos:

    ![Sinal único azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png)
  
    a. Em **Opção 2 - Ficheiro de Metadados,** clique em **Navegar** para carregar o ficheiro de metadados descarregado do portal Azure.
  
    b. Clique em Criar fornecedor de identidade usando dados de **ficheiros**.

1. Na secção Fontes de **Autenticação,** clique em **Editar**.

    ![Sinal único azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png)

1. No diálogo Fonte de **Autenticação de Edição,** execute os seguintes passos:

    ![Sinal único azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png)

    a. Como **ativado,** selecione **Sim**.

    b. Na caixa de texto **EntityId,** colhe o valor do **Identificador Azure AD** que copiou do portal Azure.

    c. Na caixa de texto **idp Descrição,** digite uma descrição para a sua configuração (por exemplo: *Azure AD SSO*).

    d. Na caixa de texto de **Ficheiros metadados,** faça upload do ficheiro **de metadados** que descarregou do portal Azure.
  
    e. Na caixa de texto **idp Name,** digite um nome específico da sua configuração (por exemplo: *Azure SP*).
  
    f. Na caixa de texto URL do **Serviço logout,** colá o valor do URL de **Logout** que copiou do portal Azure.

    g. Na caixa de texto URL do **serviço Sign-on,** colhe o valor do URL de **Login** que copiou do portal Azure.

    h. Clique em **Guardar**.

1. Desative todas as outras fontes de autenticação.

    ![Sinal único azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No tipo de campo de **nome do utilizador**`brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso à SilkRoad Life Suite.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **SilkRoad Life Suite**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **SilkRoad Life Suite**.

    ![O link SilkRoad Life Suite na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-silkroad-life-suite-test-user"></a>Crie o utilizador de teste SilkRoad Life Suite

Nesta secção, você cria uma utilizadora chamada Britta Simon na SilkRoad Life Suite. Trabalhe com a equipa de suporte do [SilkRoad Life Suite Cliente](https://www.silkroad.com/locations/) para adicionar os utilizadores na plataforma SilkRoad Life Suite. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SilkRoad Life Suite no Painel de Acesso, deverá ser automaticamente inscrito na SedaRoad Life Suite para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
