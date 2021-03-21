---
title: 'Tutorial: Integração do Diretório Ativo Azure com | da SmarterU Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SmarterU.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: ad9e1fe10da3d78d652801f885f503d13aafa7c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515935"
---
# <a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Tutorial: Integração do Diretório Ativo Azure com a SmarterU

> [!NOTE]
> O processo de integração da SmarterU com o Azure Ative Directory também está documentado e mantido no [sistema de ajuda SmarterU.](https://help.smarteru.com/ID2053086)

Neste tutorial, aprende-se a integrar o SmarterU com o Azure Ative Directory (Azure AD).
A integração da SmarterU com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao SmarterU.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no SmarterU (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com a SmarterU, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por um único sinal de smarterU

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* SmarterU apoia **IDP** iniciado SSO

## <a name="adding-smarteru-from-the-gallery"></a>Adicionando SmarterU da galeria

Para configurar a integração do SmarterU no AZure AD, precisa de adicionar o SmarterU da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar smarteru da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva SmarterU,** selecione **SmarterU** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![SmarterU na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com SmarterU com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na SmarterU.

Para configurar e testar o Azure AD com a SmarterU, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único SmarterU](#configure-smarteru-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create SmarterU test user](#create-smarteru-test-user)** - para ter uma contraparte de Britta Simon em SmarterU que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com a SmarterU, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **SmarterU,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![SmarterU Domain e URLs informações únicas de entrada](common/idp-identifier.png)

    Na caixa de texto **identifier,** digite o URL:  `https://www.smarteru.com/`

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configurar SmarterU,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-smarteru-single-sign-on"></a>Configurar smarteru single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no site da empresa SmarterU como administrador.

1. Na barra de ferramentas na parte superior, clique em **Definições de Conta**.

    ![Definições de conta](./media/smarteru-tutorial/accountsettings.png)

1. Na página de configuração da conta, execute os seguintes passos:

    ![Autorização Externa](./media/smarteru-tutorial/externalauthorizationconfiguration.png) 

    a. Selecione **Habilitar a autorização externa**.
  
    b. Na secção **Master Login Control,** selecione o **separador SmarterU.**
  
    c. Na secção **de Início de Sessão padrão do utilizador,** selecione o **separador SmarterU.**
  
    d. Selecione **Ativar SAML**.
  
    e. Copie o conteúdo do ficheiro de metadados descarregado e, em seguida, cole-o na caixa de texto **de metadados IdP.**

    f. Selecione um **atributo/reclamação do identificador**.
  
    exemplo, Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador****brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao SmarterU.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **SmarterU**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **SmarterU**.

    ![O link SmarterU na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-smarteru-test-user"></a>Criar utilizador de teste SmarterU

Para permitir que os utilizadores da Azure AD inscrevam-se no SmarterU, devem ser a provisionados na SmarterU. No caso da SmarterU, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. inscreva-se no seu inquilino **SmarterU.**

1. Ir aos **Utilizadores**.

1. Na secção de utilizador, execute os seguintes passos:

    ![Novo Utilizador](./media/smarteru-tutorial/adduser.png)  

    a. Clique **em +Utilizador**.

    b. Digite os valores de atributos relacionados da conta de utilizador Azure AD nas seguintes caixas de texto: **Email Primário**, **ID do funcionário,** **Senha,** **Verificar palavra-passe,** **Nome Dado,** **Apelido**.

    c. Clique **em Ative**.

    d. Clique em **Guardar**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador smarterU ou APIs fornecidas pela SmarterU para fornecer contas de utilizadores AZure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SmarterU no Painel de Acesso, deverá ser automaticamente inscrito no SmarterU para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)