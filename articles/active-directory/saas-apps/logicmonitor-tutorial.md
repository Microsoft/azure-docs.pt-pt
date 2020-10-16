---
title: 'Tutorial: Integração do Azure Ative Directory com o LogicMonitor Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o LogicMonitor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 4c5906d9cca193129a4213f697815f70ec639d8b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88549760"
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Tutorial: Integração do Azure Ative Directory com o LogicMonitor

Neste tutorial, aprende-se a integrar o LogicMonitor com o Azure Ative Directory (Azure AD).
A integração do LogicMonitor com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao LogicMonitor.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no LogicMonitor (Sign-on único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD do Azure com o LogicMonitor, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada por LogicMonitor

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* LogicMonitor suporta SSO iniciado **pela SP**

## <a name="adding-logicmonitor-from-the-gallery"></a>Adicionar LogicMonitor da galeria

Para configurar a integração do LogicMonitor no AD AZure, é necessário adicionar o LogicMonitor da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar LogicMonitor da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **LogicMonitor**, selecione **LogicMonitor** do painel de resultados e clique no botão **Adicionar** para adicionar a aplicação.

     ![LogicMonitor na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o LogicMonitor com base num utilizador de teste chamado **Britta Simon**.
Para um único s-on para o trabalho, deve ser estabelecida uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no LogicMonitor.

Para configurar e testar o Azure AD com o LogicMonitor, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure logicMonitor Single Sign-On](#configure-logicmonitor-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create LogicMonitor test user](#create-logicmonitor-test-user)** - para ter uma contraparte de Britta Simon em LogicMonitor que está ligada à representação AD Ad Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o LogicMonitor, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **LogicMonitor,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Informações de assinatura única do Domínio LogicMonitor e URLs](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<companyname>.logicmonitor.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<companyname>.logicmonitor.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente LogicMonitor](https://www.logicmonitor.com/contact/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção Configurar o **LogicMonitor,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-logicmonitor-single-sign-on"></a>Configurar logicMonitor single Sign-On

1. Faça login no seu site da empresa **LogicMonitor** como administrador.

2. No menu em cima, clique em **Definições**.

    ![Definições](./media/logicmonitor-tutorial/ic790052.png "Definições")

3. No taco de navegação do lado esquerdo, clique em **Single Sign On**

    ![Único sign-on](./media/logicmonitor-tutorial/ic790053.png "Início de Sessão Único")

4. Na secção **de definições de sign-on único (SSO),** execute os seguintes passos:

    ![Definições de Sign-On única](./media/logicmonitor-tutorial/ic790054.png "Definições de Sign-On única")

    a. Selecione **Ativar o único sinal de sôm.**

    b. Como **Atribuição de Função Predefinida**, selecione **readonly**.

    c. Abra o ficheiro de metadados descarregado no bloco de notas e, em seguida, cole o conteúdo do ficheiro na caixa de texto **do Fornecedor de Identidade.**

    d. Clique em **Guardar Alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador** **brittasimon \@ yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao LogicMonitor.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **LogicMonitor**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **LogicMonitor**.

    ![O link LogicMonitor na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-logicmonitor-test-user"></a>Criar utilizador de teste LogicMonitor

Para que os utilizadores de Azure AD possam iniciar sação, devem ser adutorados à aplicação LogicMonitor utilizando os seus nomes de utilizador do Azure Ative Directory.

**Para configurar o provisionamento do utilizador, execute os seguintes passos:**

1. Faça login no seu site da empresa LogicMonitor como administrador.

2. No menu em cima, clique em **Definições**e, em seguida, clique em **Funções e Utilizadores**.

    ![Funções e Utilizadores](./media/logicmonitor-tutorial/ic790056.png "Funções e Utilizadores")

3. Clique em **Adicionar**.

4. Na secção **Adicionar uma conta,** execute os seguintes passos:

    ![Adicionar uma conta](./media/logicmonitor-tutorial/ic790057.png "Adicionar uma conta")

    a. Digite o **nome de utilizador**, **Email**, **Password**e **Retipe** os valores de senha do utilizador do Azure Ative Directory que pretende obter nas caixas de texto relacionadas.

    b. Selecione **Funções**, **Ver Permissões**e **o Estado**.

    c. Clique **em Submeter.**

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador LogicMonitor ou APIs fornecidas pelo LogicMonitor para fornecer contas de utilizadores do Azure Ative Directory.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo LogicMonitor no Painel de Acesso, deverá ser automaticamente inscrito no LogicMonitor para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

