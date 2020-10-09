---
title: 'Tutorial: Integração do Azure Ative Directory com o TeamSeer Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o TeamSeer.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 815c738f1680c12f94aa97e91cd123237bf1b4f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88542503"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Tutorial: Integração do Azure Ative Directory com o TeamSeer

Neste tutorial, aprende-se a integrar o TeamSeer com o Azure Ative Directory (Azure AD).
Integrar o TeamSeer com a Azure AD proporciona-lhe os seguintes benefícios:

* Podes controlar no Azure AD que tem acesso ao TeamSeer.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no TeamSeer (Sign-on único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o TeamSeer, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura ativada por teamSeer

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* TeamSeer apoia SSO iniciado **pela SP**

## <a name="adding-teamseer-from-the-gallery"></a>Adicionar TeamSeer da galeria

Para configurar a integração do TeamSeer no AD Azure, precisa de adicionar o TeamSeer da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar TeamSeer na galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite TeamSeer**, selecione **TeamSeer** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![TeamSeer na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o TeamSeer com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no TeamSeer.

Para configurar e testar o Azure AD com o TeamSeer, tem de completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único teamSeer](#configure-teamseer-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create TeamSeer test user](#create-teamseer-test-user)** - para ter uma contraparte de Britta Simon em TeamSeer que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o TeamSeer, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **TeamSeer,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![TeamSeer Domain e URLs informações únicas de súmis](common/sp-signonurl.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://www.teamseer.com/<companyid>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte [a equipa de suporte do Cliente TeamSeer](https://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar TeamSeer,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-teamseer-single-sign-on"></a>Configurar teamSeer Single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no site da empresa TeamSeer como administrador.

1. Vá ao **Rh Admin.**

    ![Administração HR](./media/teamseer-tutorial/ic789634.png "Administração HR")

1. Clique **em Configuração**.

    ![Configuração](./media/teamseer-tutorial/ic789635.png "Configuração")

1. Clique **em Configurar detalhes do fornecedor SAML**.

    ![Definições SAML](./media/teamseer-tutorial/ic789636.png "Definições SAML")

1. Na secção de detalhes do fornecedor SAML, execute os seguintes passos:

    ![Definições SAML](./media/teamseer-tutorial/ic789637.png "Definições SAML")

    a. Na caixa de texto **URL,** cole o valor URL do **Login,** que copiou a partir do portal Azure.

    b. Abra o certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **do Certificado Público IdP.**

1. Para completar a configuração do fornecedor SAML, execute os seguintes passos:

    ![Definições SAML](./media/teamseer-tutorial/ic789638.png "Definições SAML")

    a. Nos **endereços de e-mail de teste,** digite o endereço de e-mail do utilizador do teste.
  
    b. Na caixa de texto **emitente,** digite o URL emitente do prestador de serviços.
  
    c. Clique em **Guardar**.

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao TeamSeer.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **TeamSeer**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **TeamSeer**.

    ![O link TeamSeer na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-teamseer-test-user"></a>Criar utilizador de teste TeamSeer

Para permitir que os utilizadores de Azure AD inscrevam-se no TeamSeer, devem ser adusitados ao ShiftPlanning. No caso do TeamSeer, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da sua empresa **TeamSeer** como administrador.

1. Vá aos **Utilizadores de \> Administração HR** e, em seguida, clique em **Executar o novo assistente do utilizador**.

    ![Administração HR](./media/teamseer-tutorial/ic789640.png "Administração HR")

1. Na secção **Detalhes do Utilizador,** execute os seguintes passos:

    ![Detalhes do utilizador](./media/teamseer-tutorial/ic789641.png "Detalhes do utilizador")

    a. Digite o **Nome Próprio,** **Apelido,** Nome de Utilizador (endereço de **e-mail)** de uma conta AD válida que pretenda apresentar nas caixas de texto relacionadas.
  
    b. Clique em **Seguinte**.

1. Siga as instruções no ecrã para adicionar um novo utilizador e clique em **Terminar**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador do TeamSeer ou APIs fornecidas pela TeamSeer para fornecer contas de utilizador Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo TeamSeer no Painel de Acesso, deverá ser automaticamente inscrito no TeamSeer para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
