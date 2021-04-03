---
title: 'Tutorial: Integração do Diretório Ativo Azure com | BitaBIZ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o BitaBIZ.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.openlocfilehash: f564032873be6e4c70426d48c4576371862ea35d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673624"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Tutorial: Integração do Diretório Ativo Azure com o BitaBIZ

Neste tutorial, aprende-se a integrar o BitaBIZ com o Azure Ative Directory (Azure AD).
A integração do BitaBIZ com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao BitaBIZ.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no BitaBIZ (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o BitaBIZ, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por bitaBIZ

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* BitaBIZ apoia **SP e IDP** iniciado SSO

## <a name="adding-bitabiz-from-the-gallery"></a>Adicionar BitaBIZ da galeria

Para configurar a integração do BitaBIZ no AD Azure, é necessário adicionar o BitaBIZ da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar BitaBIZ da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva BitaBIZ,** selecione **BitaBIZ** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

    ![BitaBIZ na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com bitaBIZ com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no BitaBIZ.

Para configurar e testar o Azure AD com bitaBIZ, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único bitaBIZ](#configure-bitabiz-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create BitaBIZ test user](#create-bitabiz-test-user)** - para ter uma contraparte de Britta Simon em BitaBIZ que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com bitaBIZ, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **BitaBIZ,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute o seguinte passo:

    ![BitaBIZ Domain e URLs informações únicas de súbdis](common/idp-identifier.png)

    Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:  `https://www.bitabiz.com/<instanceId>`

    > [!NOTE]
    > O valor na URL acima é apenas para demonstração. Atualize o valor com o identificador real, que é explicado mais tarde no tutorial.

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![image](common/both-preintegrated-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite o URL:  `https://www.bitabiz.com/dashboard`

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

7. Na secção **Configurar BitaBIZ,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-bitabiz-single-sign-on"></a>Configurar Sign-On Única BitaBIZ

1. Numa janela diferente do navegador web, inscreva-se no seu inquilino BitaBIZ como administrador.

2. Clique em **SETUP ADMIN**.

    ![A screenshot mostra parte de uma janela do navegador com o Setup Admin selecionado.](./media/bitabiz-tutorial/settings1.png)

3. Clique nas **integrações da Microsoft** na secção **Valor Acrescentado.**

    ![Screenshot mostra valor de adicionar com integrações microsoft selecionadas.](./media/bitabiz-tutorial/settings2.png)

4. Desloque-se até à secção **Microsoft Azure AD (Faça sinal único)** e execute os seguintes passos:

    ![A screenshot mostra a secção Microsoft Azure A D onde introduz as informações descritas neste passo.](./media/bitabiz-tutorial/settings3.png)

    a. Copie o valor da caixa de texto **Entity ID ("Identificador" em Azure AD)** e cole-o na caixa de texto **identifier** na secção **de Configuração SAML Básica** no portal Azure. 

    b. Na caixa de texto URL de **serviço de serviço Azure AD Single Sign-On,** pasta URL de **login,** que copiou do portal Azure.

    c. Na caixa de texto ID da **Entidade Azure AD SAML,** cole **o identificador Azure Ad,** que copiou do portal Azure.

    d. Abra o seu ficheiro Certificado descarregado **(Base64)** no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **Azure AD Signing Certificate (Base64 codificada).**

    e. Adicione o nome de domínio de e-mail do seu negócio que é, mycompany.com na caixa de texto **do nome de domínio** para atribuir SSO aos utilizadores da sua empresa com este domínio de e-mail (NÃO OBRIGATÓRIO).

    f. A Mark **SSO ativou** a conta BitaBIZ.

    exemplo, Clique **em Guardar a configuração AD do Azure** para guardar e ativar a configuração SSO.

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao BitaBIZ.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **BitaBIZ**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **BitaBIZ**.

    ![O link BitaBIZ na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-bitabiz-test-user"></a>Criar utilizador de teste BitaBIZ

Para permitir que os utilizadores da Azure AD iniciem sessão no BitaBIZ, devem ser a provisionados no BitaBIZ.  
No caso do BitaBIZ, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Faça login no seu site da empresa BitaBIZ como administrador.

2. Clique em **SETUP ADMIN**.

    ![A screenshot mostra parte da janela do seu navegador com o Setup Admin selecionado.](./media/bitabiz-tutorial/settings1.png)

3. Clique em **Adicionar utilizadores** na secção **Organização.**

    ![A screenshot mostra a secção Organização com utilizadores add selecionados.](./media/bitabiz-tutorial/user1.png)

4. Clique **em Adicionar novo funcionário.**

    ![Screenshot mostra Adicionar utilizadores com Adicionar novo funcionário selecionado.](./media/bitabiz-tutorial/user2.png)

5. Na página de diálogo **do novo funcionário,** execute os seguintes passos:

    ![A screenshot mostra a página onde introduz as informações descritas neste passo.](./media/bitabiz-tutorial/user3.png)

    a. Na caixa de texto **Name First,** digite o primeiro nome de utilizador como Britta.

    b. Na caixa de texto **Do Último Nome,** digite o último nome de utilizador como Simon.

    c. Na caixa de texto **por e-mail,** digite o endereço de e-mail do utilizador como Brittasimon@contoso.com .

    d. Selecione uma data em **Data de emprego**.

    e. Existem outros atributos não obrigatórios do utilizador que podem ser configurado para o utilizador. Por favor, consulte o [Documento de Configuração do Funcionário](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee) para mais detalhes.

    f. Clique **em Guardar o empregado.**

    > [!NOTE]
    > O titular da conta Azure Ative Directory recebe um e-mail e segue um link para confirmar a sua conta antes de ficar ativa.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo BitaBIZ no Painel de Acesso, deverá ser automaticamente inscrito no BitaBIZ para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)