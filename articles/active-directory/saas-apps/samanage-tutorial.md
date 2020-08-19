---
title: 'Tutorial: Integração do Azure Ative Directory com a Samanage Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Samanage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: 56018ff0be07a48cf9448b9b92de5694ebac18bc
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543538"
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Tutorial: Integração do Diretório Ativo Azure com Samanage

Neste tutorial, aprende-se a integrar a Samanage com o Azure Ative Directory (Azure AD).
A integração da Samanage com a AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso a Samanage.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Samanage (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a Samanage, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada única de Samanage

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Samanage apoia **SP** iniciado SSO

## <a name="adding-samanage-from-the-gallery"></a>Adicionando Samanage da galeria

Para configurar a integração da Samanage no Azure AD, é necessário adicionar Samanage da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Samanage da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Samanage**, selecione **Samanage** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![Samanage na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Samanage com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Samanage.

Para configurar e testar o Azure AD com samanage, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure samanage single sign-on](#configure-samanage-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Samanage test user](#create-samanage-test-user)** - para ter uma contraparte de Britta Simon em Samanage que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com samanage, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Samanage,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração de 'Sessão única' com** a página SAML, clique em **Editar** o ícone para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Samanage Domain e URLs informações únicas de súmis](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL e o Identificador de Inscrição real, que é explicado mais tarde no tutorial. Para mais detalhes contacte [a equipa de suporte do Cliente Samanage.](https://www.samanage.com/support) Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração de 'Sessão Única' com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configuração samanage,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-samanage-single-sign-on"></a>Configurar samanage single sign-on

1. Numa janela diferente do navegador web, inicie sessão no site da empresa Samanage como administrador.

2. Clique **no Painel de Instrumentos** e selecione **Configurar** no painel de navegação esquerdo.
   
    ![Dashboard](./media/samanage-tutorial/tutorial_samanage_001.png "Dashboard")

3. Clique **em 'S sign-on' único**.
   
    ![Único sign-on](./media/samanage-tutorial/tutorial_samanage_002.png "Início de Sessão Único")

4. Navegue para iniciar sessão utilizando a secção **SAML,** execute os seguintes passos:
   
    ![Faça login usando SAML](./media/samanage-tutorial/tutorial_samanage_003.png "Faça login usando SAML")
 
    a. Clique **em Ativar o Sign-On único com SAML**.  
 
    b. Na caixa de texto **URL do Fornecedor de Identidade,** cole o valor do **Identificador Ad Azure** que copiou do portal Azure.    
 
    c. Confirme que o URL de início de **sessão** corresponde ao **URL** da secção **de configuração BÁSICA SAML** no portal Azure.
 
    d. Na caixa de texto **URL logout,** insira o valor do **URL logout** que copiou do portal Azure.
 
    e. Na caixa de texto do **emitente SAML,** digite o id URI da aplicação definido no seu fornecedor de identidade.
 
    f. Abra o certificado codificado base-64 descarregado do portal Azure no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o para a Pasta do **Seu Fornecedor de Identidade x.509 Certificado abaixo** da caixa de texto.
 
    exemplo, Clique **em Criar utilizadores se não existirem em Samanage**.
 
    h. Clique em **Atualizar**.

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a Samanage.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Samanage**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Samanage**.

    ![O link Samanage na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-samanage-test-user"></a>Criar utilizador de teste Samanage

Para permitir que os utilizadores da Azure AD iniciem sessão em Samanage, devem ser a provisionados em Samanage.  
No caso de Samanage, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu site da empresa Samanage como administrador.

2. Clique **no Painel de Instrumentos** e selecione **Configurar** na panela de navegação esquerda.
   
    ![Configuração](./media/samanage-tutorial/tutorial_samanage_001.png "Configuração")

3. Clique no **separador Utilizadores**
   
    ![Utilizadores](./media/samanage-tutorial/tutorial_samanage_006.png "Utilizadores")

4. Clique **em Novo Utilizador**.
   
    ![Novo Utilizador](./media/samanage-tutorial/tutorial_samanage_007.png "Novo Utilizador")

5. Digite o **Nome** e o Endereço de **E-mail** de uma conta Azure Ative Directory que pretende provisionar e clique em **Criar utilizador.**
   
    ![Criar Utilizador](./media/samanage-tutorial/tutorial_samanage_008.png "Criar Utilizador")
   
   >[!NOTE]
   >O titular da conta Azure Ative Directory receberá um e-mail e seguirá um link para confirmar a sua conta antes de ficar ativa. Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador samanage ou APIs fornecidas pela Samanage para fornecer contas de utilizador do Azure Ative Directory.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Samanage no Painel de Acesso, deverá ser automaticamente inscrito na Samanage para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

