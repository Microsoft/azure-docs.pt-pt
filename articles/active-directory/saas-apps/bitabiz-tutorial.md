---
title: 'Tutorial: Integração do Diretório Ativo Azure com a BitaBIZ [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o BitaBIZ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a51e677-c62b-4aee-9c61-56926aaaa899
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f827945cbeccacfdf048865b6e89b6947fe7de9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159381"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Tutorial: Integração do Diretório Ativo Azure com a BitaBIZ

Neste tutorial, aprende-se a integrar o BitaBIZ com o Azure Ative Directory (Azure AD).
Integrar o BitaBIZ com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao BitaBIZ.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no BitaBIZ (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o BitaBIZ, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada por um único sinal bitaBIZ

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* BitaBIZ suporta **SP e IDP** iniciadoS SSO

## <a name="adding-bitabiz-from-the-gallery"></a>Adicionar BitaBIZ da galeria

Para configurar a integração do BitaBIZ em Azure AD, precisa adicionar bitaBIZ da galeria à sua lista de aplicações geridas do SaaS.

**Para adicionar bitaBIZ da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite BitaBIZ,** selecione **BitaBIZ** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![BitaBIZ na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com bitaBIZ com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no BitaBIZ.

Para configurar e testar o único sinal de Azure AD com o BitaBIZ, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Single BitaBIZ](#configure-bitabiz-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Criar o utilizador de **[teste BitaBIZ](#create-bitabiz-test-user)** - para ter uma contrapartida da Britta Simon no BitaBIZ que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com o BitaBIZ, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **BitaBIZ,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** Se desejar configurar a aplicação no modo iniciado **idp,** execute o seguinte passo:

    ![BitaBIZ Domain e URLs informações únicas de inscrição](common/idp-identifier.png)

    Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://www.bitabiz.com/<instanceId>`

    > [!NOTE]
    > O valor no URL acima é apenas para demonstração. Atualize o valor com o identificador real, o que é explicado mais tarde no tutorial.

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    ![image](common/both-preintegrated-signon.png)

    Na caixa de texto **de URL sign-on,** escreva o URL:`https://www.bitabiz.com/dashboard`

6. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

7. Na secção **'Configurar BitaBIZ',** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-bitabiz-single-sign-on"></a>Configure BitaBIZ Single Sign-On

1. Numa janela de navegador web diferente, inscreva-se no seu inquilino BitaBIZ como administrador.

2. Clique em **CONFIGURAUP ADMIN**.

    ![Configuração BitaBIZ](./media/bitabiz-tutorial/settings1.png)

3. Clique nas **integrações da Microsoft** no âmbito da secção **de valor adicionar.**

    ![Configuração BitaBIZ](./media/bitabiz-tutorial/settings2.png)

4. Desloque-se até à secção **Microsoft Azure AD (Ative um único sinal)** e execute os seguintes passos:

    ![Configuração BitaBIZ](./media/bitabiz-tutorial/settings3.png)

    a. Copie o valor da caixa de texto ID da **Entidade ("Identificador" em Azure AD)** e cole-o na caixa de texto **do Identificador** na secção **de Configuração Básica SAML** no portal Azure. 

    b. Na caixa de texto URL de serviço de **assinatura única Azure AD,** colhe url **de login,** que copiou do portal Azure.

    c. Na caixa de texto ID da **Entidade Azure AD SAML,** pasta **Azure Ad Identifier,** que copiou do portal Azure.

    d. Abra o ficheiro Certificado descarregado **(Base64)** no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **Azure AD (Base64 codificada).**

    e. Adicione o nome de domínio de e-mail de negócios, ou seja, mycompany.com na caixa de **texto de nome domínio** para atribuir SSO aos utilizadores da sua empresa com este domínio de e-mail (NÃO OBRIGATÓRIO).

    f. Mark **SSO ativou** a conta BitaBIZ.

    g. Clique na **configuração do AD Save Azure** para guardar e ativar a configuração SSO.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **tipo** de campo de nome utilizador **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao BitaBIZ.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **BitaBIZ**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **BitaBIZ**.

    ![O link BitaBIZ na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-bitabiz-test-user"></a>Criar o utilizador de teste BitaBIZ

Para permitir que os utilizadores de Anúncios Azure entrem no BitaBIZ, devem ser aprovisionados no BitaBIZ.  
No caso do BitaBIZ, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa BitaBIZ como administrador.

2. Clique em **CONFIGURAUP ADMIN**.

    ![BitaBIZ Adicionar Utilizador](./media/bitabiz-tutorial/settings1.png)

3. Clique em **Adicionar utilizadores** na secção **Organização.**

    ![BitaBIZ Adicionar Utilizador](./media/bitabiz-tutorial/user1.png)

4. Clique **Em adicionar novo empregado**.

    ![BitaBIZ Adicionar Utilizador](./media/bitabiz-tutorial/user2.png)

5. Na **página de** diálogo adicionar novos colaboradores, execute os seguintes passos:

    ![BitaBIZ Adicionar Utilizador](./media/bitabiz-tutorial/user3.png)

    a. Na caixa de texto **First Name,** digite o primeiro nome de utilizador como Britta.

    b. Na caixa de texto **Last Name,** digite o último nome do utilizador como Simon.

    c. Na caixa de texto **e-mail,** Brittasimon@contoso.comdigite o endereço de e-mail do utilizador como .

    d. Selecione uma data em **Data de Emprego**.

    e. Existem outros atributos de utilizador não obrigatórios que podem ser configurados para o utilizador. Por favor, consulte o Doc de [Configuração de Empregados](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee) para mais detalhes.

    f. Clique em **Guardar empregado**.

    > [!NOTE]
    > O titular da conta Azure Ative Directory recebe um e-mail e segue um link para confirmar a sua conta antes de se tornar ativo.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo BitaBIZ no Painel de Acesso, deverá ser automaticamente inscrito no BitaBIZ para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
