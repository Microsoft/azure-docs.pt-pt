---
title: 'Tutorial: Integração do Azure Ative Directory com a | de música de assentamento Microsoft Docs'
description: Aprenda a configurar um único sign-on entre o Azure Ative Directory e a música Settling.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: eafbc1543db1681b58ed499f4f29659e14a06e9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518482"
---
# <a name="tutorial-azure-active-directory-integration-with-settling-music"></a>Tutorial: Integração do Azure Ative Directory com a música de assentamento

Neste tutorial, aprende-se a integrar a música de assentamento com o Azure Ative Directory (Azure AD).
Integrar a música de assentamento com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a música de assentamento.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na fixação de música (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD do Azure com a música de assentamento, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Liquidação de música única sign-on ativada subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Assentamento de música suporta **SP** iniciado SSO

## <a name="adding-settling-music-from-the-gallery"></a>Adicionando música de assentamento da galeria

Para configurar a integração de Settleing music em Azure AD, você precisa adicionar música de assentamento da galeria à sua lista de aplicações geridas saaS.

**Para adicionar música de assentamento da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva música de assentamento**, selecione **Settleing music** from result panel then click **Add** button to add the application.

    ![Acertar a música na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com settleing music com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em "Settleing Music".

Para configurar e testar o único sinal de Azure AD com música de assentamento, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure a música de fixação Single Sign-On](#configure-settling-music-single-sign-on)** - para configurar as definições single Sign-On no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Settling music test user](#create-settling-music-test-user)** - para ter uma contrapartida de Britta Simon em Settling music que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o único sinal de Ad AD com música de assentamento, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **de música Settling,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Assentamento de música Domínio e URLs informações únicas de súmis](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do cliente](https://rakurakuseisan.jp/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **De Configuração de Definição** de Música, copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-settling-music-single-sign-on"></a>Configure a música de assentamento single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no Settleing music como Administrador de Segurança.

1. No topo da página, clique no **separador de gestão.**

    ![Assentamento da música passo1](./media/settlingmusic-tutorial/tutorial_settlingmusic_step1.png)

1. Clique no **separador definição do Sistema.**

    ![Assentamento da música passo2](./media/settlingmusic-tutorial/tutorial_settlingmusic_step2.png)

1. Mude para o **separador segurança.**

    ![Assentamento da música passo 3](./media/settlingmusic-tutorial/tutorial_settlingmusic_step3.png)

1. Na secção **de definição de inscrição única,** execute os seguintes passos:

    ![Ajuste da música passo5](./media/settlingmusic-tutorial/tutorial_settlingmusic_step4.png)

    a. Clique **para ativar**.

    b. No URL de login da caixa de texto **do fornecedor de ID,** cole o valor do URL de **Login** que copiou do portal Azure.

    c. Na caixa de texto **URL do provedor de ID,** cole o valor do URL **logout** que copiou do portal Azure.

    d. Clique **em Escolher Ficheiro** para fazer o upload do Certificado **(Base64)** que descarregou no portal Azure.

    e. Clique no botão **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **do nome do utilizador** `brittasimon@yourcompanydomain.extension` . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, permitindo o acesso a música de assentamento.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **Settle music**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, **selecione Settle music**.

    ![O link de música de assentamento na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-settling-music-test-user"></a>Criar utilizadores de teste de música de assentamento

Nesta secção, cria-se um utilizador chamado Britta Simon em "Settling music". Trabalhar com [a equipa de suporte do cliente settling music](https://rakurakuseisan.jp/) para adicionar os utilizadores na plataforma de música Settling. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo de música Settling no Painel de Acesso, deverá ser automaticamente inscrito na música De assentamento para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)