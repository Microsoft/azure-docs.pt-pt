---
title: 'Tutorial: Integração do Azure Ative Directory com a Vidyard Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Vidyard.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 2c6b88e9c59fa7195e77275ad193975924df137f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88532165"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Tutorial: Integração do Diretório Ativo Azure com a Vidyard

Neste tutorial, aprende-se a integrar a Vidyard com o Azure Ative Directory (Azure AD).
A integração da Vidyard com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Vidyard.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Vidyard (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com a Vidyard, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura ativada única vidyard

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Vidyard apoia **SP** e **IDP** iniciado SSO

* Vidyard suporta **provisão de** utilizadores just in time

## <a name="adding-vidyard-from-the-gallery"></a>Adicionando Vidyard da galeria

Para configurar a integração da Vidyard no Azure AD, é necessário adicionar o Vidyard da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Vidyard da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Vidyard,** selecione **Vidyard** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![Vidyard na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Vidyard com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Vidyard.

Para configurar e testar o Azure AD com a Vidyard, tem de completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único do Vidyard](#configure-vidyard-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Vidyard test user](#create-vidyard-test-user)** - para ter uma contraparte de Britta Simon em Vidyard que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com a Vidyard, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Vidyard,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![Informações de assinatura única do domínio vidyard e URLs](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![Informações de assinatura única do domínio vidyard e URLs](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Estes valores não são reais. Irá atualizar estes valores com o identificador real, URL de resposta e URL Sign-On, que é explicado mais tarde no tutorial. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

7. Na secção **Configurar Vidyard,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-vidyard-single-sign-on"></a>Configurar vidyard single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Vidyard Software como administrador.

2. A partir do painel Vidyard, selecione **Security do Grupo**  >  **Security**

    ![Configuração Vidyard](./media/vidyard-tutorial/configure1.png)

3. Clique no separador **Novo Perfil.**

    ![Configuração Vidyard](./media/vidyard-tutorial/configure2.png)

4. Na secção **de Configuração SAML,** execute os seguintes passos:

    ![Configuração Vidyard](./media/vidyard-tutorial/configure3.png)

    a. Introduza o nome do perfil geral na caixa de texto **'Nome do Perfil'.**

    b. Copie o valor **da página de início de sso do utilizador** e cole-o em Assinar na caixa de texto **URL** na secção **de configuração SAML básica** no portal Azure.

    c. Copie o valor **do URL ACS** e cole-o na caixa de texto **URL de resposta** na secção de **configuração SAML básica** no portal Azure.

    d. Copiar o valor **URL do emitente/metadados** e colá-lo na caixa de texto **identifier** na secção **de configuração SAML básica** no portal Azure.

    e. Abra o seu ficheiro de certificado descarregado a partir do portal Azure no Bloco de Notas e, em seguida, cole-o na caixa de texto **do Certificado X.509.**

    f. Na caixa de texto **URL DO PONTO FINAL SAML,** cole o valor do URL de **Login** copiado do portal Azure.

    exemplo, Clique em **Confirmar**.

5. A partir do sinal único no separador, selecione **Atribuir** ao lado de um perfil existente

    ![Configuração Vidyard](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Depois de ter criado um perfil SSO, atribua-o a qualquer grupo para o qual os utilizadores necessitem de acesso através do Azure. Se o utilizador não existir dentro do grupo ao qual foram designados, a Vidyard criará automaticamente uma conta de utilizador e atribuirá o seu papel em tempo real.

6. Selecione o seu grupo de organização, que está visível nos **Grupos Disponíveis para Atribuir.**

    ![Configuração Vidyard](./media/vidyard-tutorial/configure5.png)

7. Pode ver os grupos designados nos **grupos atualmente designados.** Selecione uma função para o grupo de acordo com a sua organização e clique em **Confirmar**.

    ![Configuração Vidyard](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Para mais informações, consulte [este doc](https://knowledge.vidyard.com/hc/articles/360009990033-SAML-based-Single-Sign-On-SSO-in-Vidyard).

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso à Vidyard.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Vidyard**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Vidyard**.

    ![O link Vidyard na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-vidyard-test-user"></a>Criar utilizador de teste Vidyard

Nesta secção, um utilizador chamado Britta Simon é criado em Vidyard. A Vidyard suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na Vidyard, um novo é criado após a autenticação.

>[!Note]
>Se precisar de criar um utilizador manualmente, contacte a [equipa de suporte da Vidyard](mailto:support@vidyard.com).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Vidyard no Painel de Acesso, deverá ser automaticamente inscrito no Vidyard para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

