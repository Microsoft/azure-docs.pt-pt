---
title: 'Tutorial: Integração do Azure Ative Directory com Sprinklr Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Sprinklr.
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
ms.openlocfilehash: c6a7784d052a03971fad84c90cdd70cd16b4a872
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122573"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Tutorial: Integração do Diretório Ativo Azure com Sprinklr

Neste tutorial, aprende-se a integrar o Sprinklr com o Azure Ative Directory (Azure AD).
A integração da Sprinklr com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Sprinklr.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Sprinklr (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o Sprinklr, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada por sprinklr

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Sprinklr suporta **SP** iniciado SSO

## <a name="adding-sprinklr-from-the-gallery"></a>Adicionando Sprinklr da galeria

Para configurar a integração do Sprinklr no AZure AD, é necessário adicionar o Sprinklr da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Sprinklr da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Sprinklr**, selecione **Sprinklr** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![Sprinklr na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Sprinklr com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Sprinklr.

Para configurar e testar o Azure AD com sprinklr, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sprinklr Single Sign-On](#configure-sprinklr-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Sprinklr test user](#create-sprinklr-test-user)** - para ter uma contraparte de Britta Simon em Sprinklr que está ligada à representação AD AZure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com Sprinklr, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Sprinklr,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Sprinklr Domain e URLs informações únicas de sinal-on](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<subdomain>.sprinklr.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<subdomain>.sprinklr.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente Sprinklr](https://www.sprinklr.com/contact-us/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar Sprinklr,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-sprinklr-single-sign-on"></a>Configure Sprinklr Single Sign-On

1. Numa janela diferente do navegador web, inicie sessão no seu site da empresa Sprinklr como administrador.

1. Ir para ** \> definições de administração**.

    ![Administration](./media/sprinklr-tutorial/ic782907.png "Administração")

1. Vá para **Gerir o Parceiro Único \> Sinal** a partir do painel esquerdo.

    ![Gerir Parceiro](./media/sprinklr-tutorial/ic782908.png "Gerir Parceiro")

1. Clique **em +Adicionar 'Único Sinal' Ons**.

    ![A screenshot mostra o botão Adicionar Único Sinal Ons.](./media/sprinklr-tutorial/ic782909.png "Sign-Ons única")

1. No **único sinal na** página, execute os seguintes passos:

    ![A screenshot mostra o Sinal Único na página onde pode introduzir os valores descritos.](./media/sprinklr-tutorial/ic782910.png "Sign-Ons única")

    a. Na caixa de texto **Name,** digite um nome para a sua configuração (por exemplo: *WAADSSOTest).*

    b. Selecione **Ativado**.

    c. Selecione **Utilize o novo Certificado SSO**.

    d. Abra o certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **do Certificado de Fornecedor de Identidade.**

    e. Cole o valor do **identificador Azure AD** que copiou do Portal Azure para a caixa de texto **da Entity Id.**

    f. Cole o valor URL de **login** que copiou do Portal Azure para a caixa de texto **URL do Fornecedor de Identidade.**

    exemplo, Cole o valor **URL logout** que copiou do Portal Azure para a caixa de texto **URL do Fornecedor de Identidade.**

    h. Como **tipo de identificação do utilizador SAML,** **selecione Assertion contém o nome de utilizador sprinklr.com do utilizador**.

    i. Como **Localização ID do utilizador SAML,** selecione **User ID está no elemento identificador de nome da declaração do sujeito**.

    j. Clique em **Guardar**.

    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a Sprinklr.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Sprinklr**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Sprinklr**.

    ![O link Sprinklr na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-sprinklr-test-user"></a>Criar utilizador de teste Sprinklr

1. Faça login no seu site da empresa Sprinklr como administrador.

1. Ir para ** \> definições de administração**.

    ![Administration](./media/sprinklr-tutorial/ic782907.png "Administração")

1. Vá para **gerir \> os utilizadores** de clientes a partir do painel esquerdo.

    ![A screenshot mostra o botão Adicionar Utilizador em Definições/Utilizadores.](./media/sprinklr-tutorial/ic782914.png "Definições")

1. Clique em **Adicionar Utilizador**.

    ![A screenshot mostra a caixa de diálogo do utilizador Editar onde pode introduzir os valores descritos.](./media/sprinklr-tutorial/ic782915.png "Definições")

1. No diálogo do **utilizador Editar,** execute os seguintes passos:

    ![Editar utilizador](./media/sprinklr-tutorial/ic782916.png "Editar utilizador")

    a. Nas caixas de texto **de Email**, **Nome Próprio** e **Apelido,** digite a informação de uma conta de utilizador Azure AD que pretende obter.

    b. Selecione **palavra-passe desativada**.

    c. Selecione **Linguagem**.

    d. Selecione **o tipo de utilizador**.

    e. Clique em **Atualizar**.

    > [!IMPORTANT]
    > **O Desativado de Palavra-Passe** deve ser selecionado para permitir que um utilizador faça login através de um fornecedor de Identidade. 

1. Vá para o **Papel**, e, em seguida, execute os seguintes passos:

    ![Funções de Parceiro](./media/sprinklr-tutorial/ic782917.png "Funções de Parceiro")

    a. Na lista **Global,** selecione **ALL_Permissions**.  

    b. Clique em **Atualizar**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador Sprinklr ou APIs fornecidas pela Sprinklr para fornecer contas de utilizador Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Sprinklr no Painel de Acesso, deverá ser automaticamente inscrito no Sprinklr para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
