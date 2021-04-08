---
title: 'Tutorial: Integração do Diretório Ativo Azure com pingboard | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Pingboard.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: d7b090eb5228a449212ceebd6cd299c5f8860089
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92520800"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Tutorial: Integração do Diretório Ativo Azure com Pingboard

Neste tutorial, aprende-se a integrar o Pingboard com o Azure Ative Directory (Azure AD).
A integração do Pingboard com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Pingboard.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Pingboard (Sign-on único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o Pingboard, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada única do pingboard

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Pingboard suporta **SP** e **IDP** iniciado SSO

* Pingboard suporta [fornecimento automatizado de utilizadores](./pingboard-provisioning-tutorial.md) 

## <a name="adding-pingboard-from-the-gallery"></a>Adicionando pingboard da galeria

Para configurar a integração do Pingboard no Azure AD, é necessário adicionar o Pingboard da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Pingboard da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Pingboard,** selecione **Pingboard** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![Pingboard na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sinal de sinalização com pingboard baseado num utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Pingboard.

Para configurar e testar o Azure AD com um único sinal de acesso com pingboard, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o pingboard single sign-on](#configure-pingboard-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Pingboard test user](#create-pingboard-test-user)** - para ter uma contraparte de Britta Simon em Pingboard que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o Pingboard, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **pingboard,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![Screenshot que mostra a configuração "Basic S A M L" com as caixas de texto "Identifier" e "Answer U R L" realçadas e o botão "Save" selecionado.](common/idp-intiated.png)

    a. Na caixa de texto **identifier,** digite um URL: `http://app.pingboard.com/sp`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<entity-id>.pingboard.com/auth/saml/consume`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![Pingboard Domain e URLs informações únicas de súmis](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de resposta real e URL de inscrição. Contacte a [equipa de suporte do Cliente pingboard](https://support.pingboard.com/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

7. Na secção **Configurar pingboard,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-pingboard-single-sign-on"></a>Configurar pingboard single Sign-On

1. Para configurar o SSO no lado do Pingboard, abra uma nova janela do navegador e inscreva-se na sua Conta Pingboard. Deve ser um administrador de pingboard para configurar um único sinal.

2. A partir do menu superior, selecione **Apps > Integrações**

    ![Configurar Sign-On Individuais](./media/pingboard-tutorial/Pingboard_integration.png)

3. Na página **Integrações,** encontre o azulejo **"Azure Ative Directory"** e clique nele.

    ![Integração de Sign-On individuais de pingboard](./media/pingboard-tutorial/Pingboard_aad.png)

4. Na modal que se segue clique em **"Configurar"**

    ![Botão de configuração do pingboard](./media/pingboard-tutorial/Pingboard_configure.png)

5. Na página seguinte, nota-se que "A Azure SSO Integration está ativada". Abra o ficheiro XML de metadados descarregado num bloco de notas e cole o conteúdo em **Metadados IDP**.

    ![Ecrã de configuração SSO de pingboard](./media/pingboard-tutorial/Pingboard_sso_configure.png)

6. O ficheiro é validado e, se estiver tudo bem, o sôm-on será agora ativado.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **do nome do utilizador** brittasimon@yourcompanydomain.extension . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao Pingboard.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **Pingboard**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Pingboard**.

    ![O link pingboard na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-pingboard-test-user"></a>Criar utilizador de teste de pingboard

O objetivo desta secção é criar um utilizador chamado Britta Simon em Pingboard. O pingboard suporta o fornecimento automático do utilizador, que é por defeito ativado. Pode encontrar mais detalhes [aqui](pingboard-provisioning-tutorial.md) sobre como configurar o fornecimento automático do utilizador.

**Se precisar de criar o utilizador manualmente, execute os seguintes passos:**

1. Inscreva-se no site da empresa Pingboard como administrador.

2. Clique no botão **"Adicionar Empregado"** na página **do Diretório.**

    ![Adicionar Empregado](./media/pingboard-tutorial/create_testuser_add.png)

3. Na página de diálogo **"Adicionar Empregado",** execute os seguintes passos:

    ![Convidar pessoas](./media/pingboard-tutorial/create_testuser_name.png)

    a. Na caixa de texto **'Nome Completo',** digite o nome completo do utilizador como **Britta Simon**.

    b. Na caixa de texto **por e-mail,** digite o endereço de e-mail do utilizador como **brittasimon@contoso.com** .

    c. Na caixa de texto **do Título de Emprego,** escreva o título de emprego de Britta Simon.

    d. No **dropdown de localização,** selecione a localização de Britta Simon.

    e. Clique em **Adicionar**.

4. Um ecrã de confirmação surge para confirmar a adição do utilizador.

    ![confirmar](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > O titular da conta Azure Ative Directory recebe um e-mail e segue um link para confirmar a sua conta antes de ficar ativa.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Pingboard no Painel de Acesso, deverá ser automaticamente inscrito no Pingboard para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)

- [Configurar o Provisionamento do Utilizador](./pingboard-provisioning-tutorial.md)