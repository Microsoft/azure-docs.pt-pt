---
title: 'Tutorial: Integração de Diretório Sonérório Ativo Azure com Serviços de Gestão de Presenças [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e os Serviços de Gestão de Presenças.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1f56e612-728b-4203-a545-a81dc5efda00
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7143d0afce7a3644286703a9eba0da1ee45305f2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67106547"
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Tutorial: Integração de Diretório Ativo Azure com Serviços de Gestão de Presenças

Neste tutorial, aprende-se a integrar os Serviços de Gestão de Presenças com o Azure Ative Directory (Azure AD).
Integrar os Serviços de Gestão de Presenças com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Serviços de Gestão de Presenças.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos nos Serviços de Gestão de Presenças (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com os Serviços de Gestão de Presenças, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Serviços de Gestão de Atendimento única subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Serviços de Gestão de Atendimento apoia **SP** iniciado SSO

## <a name="adding-attendance-management-services-from-the-gallery"></a>Adicionar Serviços de Gestão de Presenças da galeria

Para configurar a integração dos Serviços de Gestão de Presenças em Azure AD, você precisa adicionar Serviços de Gestão de Presenças da galeria à sua lista de aplicações geridas saaS.

**Para adicionar Serviços de Gestão de Presenças na galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite os Serviços de **Gestão de Presenças,** selecione Serviços de Gestão de **Presenças** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

    ![Serviços de Gestão de Atendimento na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com os Serviços de Gestão de Presenças com base num utilizador de teste chamado **Britta Simon**.
Para que o registo único funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador coligado nos Serviços de Gestão de Presenças.

Para configurar e testar o único sign-on azure ad com os Serviços de Gestão de Presenças, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure serviços de gestão de presenças únicas -](#configure-attendance-management-services-single-sign-on)** para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Attendance Management Services user](#create-attendance-management-services-test-user)** - para ter uma contrapartida da Britta Simon nos Serviços de Gestão de Presenças que esteja ligada à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o Azure AD único sign-on com os Serviços de Gestão de Presenças, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações de Serviços de **Gestão de Presenças,** selecione **um único sinal.**

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Domínio de Serviços de Gestão de Atendimento e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://id.obc.jp/<tenant information >/`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de apoio ao Cliente dos Serviços de [Gestão](https://www.obcnet.jp/) de Atendimento para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção de Serviços de **Gestão de Presenças, copie** os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-attendance-management-services-single-sign-on"></a>Configure Serviços de Gestão de Presenças Única Sessão

1. Numa janela de navegador diferente, inscreva-se no site da sua empresa de Serviços de Gestão de Presenças como administrador.

1. Clique na **autenticação SAML** sob a secção de **gestão**de segurança .

    ![Configuração de Serviços de Gestão de Presenças](./media/attendancemanagementservices-tutorial/user1.png)

1. Execute os seguintes passos:

    ![Configuração de Serviços de Gestão de Presenças](./media/attendancemanagementservices-tutorial/user2.png)

    a. Selecione **Utilizar a autenticação SAML**.

    b. Na caixa de texto **Identifier,** colhe o valor do valor do **Identificador AD Azure,** que copiou do portal Azure.

    c. Na caixa de texto URL de ponto final de **Autenticação,** colá o valor do valor do URL de **Login,** que copiou do portal Azure.

    d. Clique **em Selecionar um ficheiro** para fazer o upload do certificado que descarregou a partir do Azure AD.

    e. Selecione **Desativar a autenticação da palavra-passe**.

    f. Clique no **registo**

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

Nesta secção, permite à Britta Simon utilizar o único sign-on azure, concedendo acesso aos Serviços de Gestão de Presenças.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione Serviços de **Gestão de Presenças**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione Serviços de **Gestão de Presenças.**

    ![O link serviços de gestão de presenças na lista de Candidaturas](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-attendance-management-services-test-user"></a>Criar o utilizador de teste de Serviços de Gestão de Presenças

Para permitir que os utilizadores da AD Azure assinem os Serviços de Gestão de Presenças, devem ser prestados aos Serviços de Gestão de Presenças. No caso dos Serviços de Gestão de Presenças, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da sua empresa de Serviços de Gestão de Presenças como administrador.

1. Clique na **gestão do utilizador** sob a secção de **gestão**de segurança .

    ![Adicionar Empregado](./media/attendancemanagementservices-tutorial/user5.png)

1. Clique em **novas regras de login**.

    ![Adicionar Empregado](./media/attendancemanagementservices-tutorial/user3.png)

1. Na secção de **informação oBCiD,** execute os seguintes passos:

    ![Adicionar Empregado](./media/attendancemanagementservices-tutorial/user4.png)

    a. Na caixa de texto **OBCiD,** digite o e-mail do utilizador como `BrittaSimon\@contoso.com`.

    b. Na caixa de texto **Password,** digite a palavra-passe do utilizador.

    c. Clique no **registo**

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo dos Serviços de Gestão de Presenças no Painel de Acesso, deverá ser automaticamente inscrito nos Serviços de Gestão de Presenças para os quais configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)