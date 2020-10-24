---
title: 'Tutorial: Integração do Azure Ative Directory com Nomeadamente / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Namely.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: bafec2c1b857d3220089e2ca41190987de33edb6
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519522"
---
# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Tutorial: Integração do Diretório Ativo Azure com nomeadamente

Neste tutorial, aprende-se a integrar-se nomeadamente com o Azure Ative Directory (Azure AD).
Integrar a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Nomeadamente.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos para (Sign-on único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a Namely, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Nomeadamente uma assinatura ativada por cada sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Nomeadamente suporta **sSO** iniciado SP

## <a name="adding-namely-from-the-gallery"></a>Adicionar nomeadamente da galeria

Para configurar a integração de Azure AD, é necessário adicionar, nomeadamente, da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Nomeadamente da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva mente,** selecione **Nomeadamente** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![Nomeadamente na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com a identificação com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Namely.

Para configurar e testar o Azure AD com um único sinal de acesso com a namely, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure Nomeadamente o Sign-On Único](#configure-namely-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Namely test user](#create-namely-test-user)** - para ter uma contraparte de Britta Simon em Ad nomeadamente que está ligada à representação AD AZure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar a Azure AD um único sinal de inscrição com a namely, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de **aplicações,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Nomeadamente, informações de assinatura única de domínio e URLs](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<subdomain>.namely.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<subdomain>.namely.com/saml/metadata`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de apoio ao Cliente](https://www.namely.com/contact/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configuração,** copiar os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-namely-single-sign-on"></a>Configurar nomeadamente Sign-On individuais

1. Em outra janela do navegador, inscreva-se no seu site da empresa, como administrador.

2. Na barra de ferramentas em cima, clique em **Empresa**.
   
    ![A screenshot mostra o valor da Empresa selecionado.](./media/namely-tutorial/tutorial_namely_06.png) 

3. Clique no separador **Definições**.
   
    ![A screenshot mostra o separador Definições da Empresa selecionado.](./media/namely-tutorial/tutorial_namely_07.png) 

4. Clique **em SAML**.
   
    ![A screenshot mostra SAML selecionado.](./media/namely-tutorial/tutorial_namely_08.png) 

5. Na página **definições DE SAML,** execute os seguintes passos:
   
    ![A screenshot mostra configurações SAML onde pode introduzir os valores descritos.](./media/namely-tutorial/tutorial_namely_09.png)
 
    a. Clique **em Ativar o SAML**. 

    b. Na url textbox **do fornecedor de identidade SSO,** cole o valor do URL de **Login,** que copiou do portal Azure.
    
    c. Abra o seu certificado descarregado no Bloco de Notas, copie o conteúdo e, em seguida, cole-o na caixa de texto **do certificado de fornecedor de identidade.**
     
    d. Clique em **Guardar**.

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

Nesta secção, você permite que Britta Simon utilize a Azure single sign-on, concedendo acesso a Nome.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Nomeadamente**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Nomeadamente**.

    ![O link nomeadamente na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-namely-test-user"></a>Criar Utilizador de teste de identificação

O objetivo desta secção é criar um utilizador chamado Britta Simon em Nome.

**Para criar um utilizador chamado Britta Simon in Namely, execute os seguintes passos:**

1. Inscreva-se no seu site da empresa, nomeadamente como administrador.

2. Na barra de ferramentas em cima, clique em **People**.
   
    ![A screenshot mostra o valor do Povo selecionado.](./media/namely-tutorial/tutorial_namely_10.png) 

3. Clique no **separador Diretório.**
   
    ![A screenshot mostra o separador People Directory selecionado.](./media/namely-tutorial/tutorial_namely_11.png) 

4. Clique **em Adicionar Nova Pessoa.**

    ![A screenshot mostra a opção Add New Person.](./media/namely-tutorial/tutorial_namely_12.png)

5. No diálogo **Add New Person,** execute os seguintes passos:

    a. Na caixa de texto do **primeiro nome,** **escreva Britta.**

    b. Na caixa de texto do **último nome,** escreva **Simon.**

    c. Na caixa de texto **de email,** digite o endereço de **e-mail** da BrittaSimon.

    d. Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo 'Nomeadamente' no Painel de Acesso, deverá ser automaticamente inscrito no SSO para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)