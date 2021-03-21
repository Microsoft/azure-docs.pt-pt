---
title: 'Tutorial: Integração do Diretório Ativo Azure com Serviços de Gestão de Atendimento | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e os Serviços de Gestão de Atendimento.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: 1f404d3613f9de8daadc4bb2ceb39282cf3b619e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101688999"
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Tutorial: Integração do Diretório Ativo Azure com Serviços de Gestão de Atendimento

Neste tutorial, aprende-se a integrar os Serviços de Gestão de Assiduidade com o Azure Ative Directory (Azure AD).
A integração dos Serviços de Gestão de Assiduidade com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Serviços de Gestão de Atendimento.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos nos Serviços de Gestão de Atendimento (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com os Serviços de Gestão de Atendimento, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Serviços de Gestão de Atendimento uma única subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Serviços de Gestão de Atendimento suporta SSO iniciados **pela SP**

## <a name="adding-attendance-management-services-from-the-gallery"></a>Adicionar Serviços de Gestão de Atendimento da galeria

Para configurar a integração dos Serviços de Gestão de Atendimento no AZure AD, é necessário adicionar Serviços de Gestão de Assiduidade da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Serviços de Gestão de Assiduidade da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Serviços de Gestão de Assistência**, selecione Serviços de **Gestão** de Assistência a partir do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

    ![Serviços de Gestão de Atendimento na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com Serviços de Gestão de Assiduidade com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado nos Serviços de Gestão de Atendimento.

Para configurar e testar o Azure AD com os Serviços de Gestão de Atendimento, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure serviços de gestão de assistência único sinal-on](#configure-attendance-management-services-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Attendance Management Services test user](#create-attendance-management-services-test-user)** - para ter uma contrapartida de Britta Simon em Serviços de Gestão de Atendimento que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com os Serviços de Gestão de Atendimento, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **dos Serviços** de Gestão de Atendimento, selecione **'SÚB's's's'on' Único**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Serviços de Gestão de Atendimento Domínio e URLs informações únicas de súmis](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://id.obc.jp/<tenant information >/`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte ao cliente dos Serviços de Gestão](https://www.obcnet.jp/) de Atendimento para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **De Serviços de Gestão de Assistência,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-attendance-management-services-single-sign-on"></a>Configure serviços de gestão de assistência Sign-On

1. Numa janela de navegador diferente, inscreva-se no site da empresa de Serviços de Gestão de Atendimento como administrador.

1. Clique na **autenticação SAML** na **secção de gestão de segurança.**

    ![A screenshot mostra a autenticação SAML selecionada numa página que utiliza caracteres não latinos.](./media/attendancemanagementservices-tutorial/user1.png)

1. Efetue os seguintes passos:

    ![A screenshot mostra a janela onde pode executar as tarefas descritas neste passo.](./media/attendancemanagementservices-tutorial/user2.png)

    a. Selecione **A autenticação SAML**.

    b. Na caixa de texto **identifier,** cole o valor do valor do **identificador Azure AD,** que copiou do portal Azure.

    c. Na caixa de texto URL do ponto final de **autenticação,** cole o valor do valor URL do **Login,** que copiou do portal Azure.

    d. Clique **em Selecionar um ficheiro** para fazer o upload do certificado que descarregou a partir do Azure AD.

    e. Selecione **A autenticação de senha de desativação**.

    f. Clique **no Registo**

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

Nesta secção, permite à Britta Simon utilizar o Azure um único sinal de acesso, permitindo o acesso aos Serviços de Gestão de Atendimento.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Serviços de Gestão de Atendimento**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Serviços de Gestão de Atendimento.**

    ![O link dos Serviços de Gestão de Atendimento na lista de Candidaturas](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-attendance-management-services-test-user"></a>Criar utilizador de teste de Serviços de Gestão de Atendimento

Para permitir que os utilizadores da Azure AD inscrevam-se nos Serviços de Gestão de Atendimento, devem ser ademedidos aos Serviços de Gestão de Atendimento. No caso dos Serviços de Gestão de Atendimento, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa de Gestão de Atendimento como administrador.

1. Clique na **gestão do utilizador** na **secção de gestão de Segurança.**

    ![A screenshot mostra a gestão do utilizador selecionada numa página que utiliza caracteres não latinos.](./media/attendancemanagementservices-tutorial/user5.png)

1. Clique em **início de sessão de novas regras**.

    ![A screenshot mostra a seleção da opção mais.](./media/attendancemanagementservices-tutorial/user3.png)

1. Na secção **de informação OBCiD,** execute os seguintes passos:

    ![A screenshot mostra a janela onde pode executar as tarefas descritas.](./media/attendancemanagementservices-tutorial/user4.png)

    a. Na caixa de texto **OBCiD,** escreva o e-mail do utilizador como `BrittaSimon@contoso.com` .

    b. Na caixa de texto **da palavra-passe,** digite a palavra-passe do utilizador.

    c. Clique **no Registo**

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo dos Serviços de Gestão de Atendimento no Painel de Acesso, deverá ser automaticamente inscrito nos Serviços de Gestão de Atendimento para os quais configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)