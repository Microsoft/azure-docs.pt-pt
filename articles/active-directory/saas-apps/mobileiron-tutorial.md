---
title: 'Tutorial: Integração do Azure Ative Directory com o MobileIron Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o MobileIron.
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
ms.openlocfilehash: 7965edd5dc54155f03b9211d5c2b21d451018bd1
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92514457"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Tutorial: Integração do Azure Ative Directory com o MobileIron

Neste tutorial, aprende-se a integrar o MobileIron com o Azure Ative Directory (Azure AD).
A integração do MobileIron com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao MobileIron.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no MobileIron (Sign-on único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o MobileIron, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada por MobileIron

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* MobileIron suporta **SP e IDP** iniciado SSO

## <a name="adding-mobileiron-from-the-gallery"></a>Adicionar MobileIron da galeria

Para configurar a integração do MobileIron no Azure AD, é necessário adicionar o MobileIron da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar MobileIron da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite MobileIron,** selecione **MobileIron** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![MobileIron na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o MobileIron com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de acesso ao trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no MobileIron.

Para configurar e testar o Azure AD com o MobileIron, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o MobileIron Single Sign-On](#configure-mobileiron-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create MobileIron test user](#create-mobileiron-test-user)** - para ter uma contraparte de Britta Simon em MobileIron que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o MobileIron, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **MobileIron,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** execute os seguintes passos se desejar configurar a aplicação no modo iniciado pelo **IDP:**

    ![A screenshot mostra a Configuração BÁSICA SAML, onde pode introduzir o Identificador, Responder U R L e selecionar Guardar.](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://www.mobileiron.com/<key>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<host>.mobileiron.com/saml/SSO/alias/<key>`

    c. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![Screenshot mostra Definir U R Ls adicionais onde pode introduzir um sinal em U R L.](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<host>.mobileiron.com/user/login.html`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL Sign-On. Você receberá os valores de chave e anfitrião do portal administrativo do MobileIron que é explicado mais tarde no tutorial.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

### <a name="configure-mobileiron-single-sign-on"></a>Configurar Sign-On single mobileIron

1. Numa janela diferente do navegador web, inicie sessão no site da empresa MobileIron como administrador.

2. Vá para a Identidade **de Administrador**  >  **Identity** e selecione a opção **AAD** no campo **Informação na Configuração do IDP da Nuvem.**

    ![A screenshot mostra o separador Admin do site MobileIron com identidade selecionada.](./media/mobileiron-tutorial/tutorial_mobileiron_admin.png)

3. Copie os valores de **Chave** e **Anfitrião** e cole-os para completar os URLs na secção **de Configuração Básica SAML** no portal Azure.

    ![A screenshot mostra a opção Configuração SAML com uma chave e valor de anfitrião.](./media/mobileiron-tutorial/key.png)

4. No **ficheiro de metadados de exportação a partir de AAD e importado para MobileIron Cloud Field** clique **em Escolher Ficheiro** para carregar os metadados descarregados do portal Azure. Clique em **Fazer** uma vez carregado.

    ![Configure botão de metadados únicos Sign-On de administração](./media/mobileiron-tutorial/tutorial_mobileiron_adminmetadata.png)

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao MobileIron.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **MobileIron**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, escreva e selecione **MobileIron**.

    ![O link MobileIron na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-mobileiron-test-user"></a>Criar utilizador de teste MobileIron

Para permitir que os utilizadores de Azure AD iniciem sessão no MobileIron, devem ser a provisionados no MobileIron.  
No caso do MobileIron, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Faça login no seu site da empresa MobileIron como administrador.

1. Vá aos **Utilizadores** e clique em **Adicionar**  >  **Utilizador Único**.

    ![Configure botão único de utilizador Sign-On](./media/mobileiron-tutorial/tutorial_mobileiron_user.png)

1. Na página de diálogo **"Único Utilizador",** execute os seguintes passos:

    ![Configure botão único Sign-On utilizador adicionar](./media/mobileiron-tutorial/tutorial_mobileiron_useradd.png)

    a. Na caixa de texto **do endereço de e-mail,** insira o e-mail do utilizador como brittasimon@contoso.com .

    b. Na caixa de texto **First Name,** insira o primeiro nome do utilizador como Britta.

    c. Na caixa de texto **Last Name,** insira o último nome do utilizador como Simon.

    d. Clique em **Done** (Concluído).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo MobileIron no Painel de Acesso, deverá ser automaticamente inscrito no MobileIron para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)