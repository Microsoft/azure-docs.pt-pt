---
title: 'Tutorial: Integração do Azure Ative Directory com a Asana Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Asana.
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
ms.openlocfilehash: 29baaac7cf9139368a191153fdbd0179595bef66
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672887"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Tutorial: Integração do Diretório Ativo Azure com a Asana

Neste tutorial, aprende-se a integrar a Asana com o Azure Ative Directory (Azure AD).
A integração da Asana com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso a Asana.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Asana (Sign-on Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Asana, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada única de Asana

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Asana apoia **SP** iniciado SSO

* Asana suporta fornecimento [ **automatizado** de utilizadores](asana-provisioning-tutorial.md)

## <a name="adding-asana-from-the-gallery"></a>Adicionando Asana da galeria

Para configurar a integração da Asana no Azure AD, precisa adicionar Asana da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Asana da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Asana,** selecione **Asana** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Asana na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Asana com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Asana.

Para configurar e testar o Azure AD com asana, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único de Asana](#configure-asana-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Asana test user](#create-asana-test-user)** - ter uma contraparte de Britta Simon em Asana que está ligada à representação AZure AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com Asana, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Asana,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Asana Domain e URLs informações únicas de súmis](common/sp-identifier.png)

    a. Na caixa de texto URL do **Signo,** escreva URL: `https://app.asana.com/`

    b. Na caixa de texto **identifier (Entity ID),** tipo URL: `https://app.asana.com/`

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configuração Asana,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-asana-single-sign-on"></a>Configurar asana single Sign-On

1. Numa janela de navegador diferente, inscreva-se na sua aplicação Asana. Para configurar o SSO em Asana, aceda às definições do espaço de trabalho clicando no nome do espaço de trabalho no canto superior direito do ecrã. Em seguida, clique em **\<your workspace name\> Definições**.

    ![Asana sso configurações](./media/asana-tutorial/tutorial_asana_09.png)

2. Na janela de definições da **Organização,** clique em **Administração**. Em seguida, clique **em Membros devem iniciar sessão via SAML** para ativar a configuração SSO. Executar os seguintes passos:

    ![Configurar configurar configurações da Organização de Sign-On Única](./media/asana-tutorial/tutorial_asana_10.png)  

    a. Na caixa de texto **URL da página de início de sessão,** cole o URL de início de **sessão**.

    b. Clique no certificado descarregado do portal Azure e, em seguida, abra o ficheiro de certificado usando o Notepad ou o seu editor de texto preferido. Copie o conteúdo entre o título do certificado de início e de fim e cole-o na caixa de texto **do Certificado X.509.**

3. Clique em **Guardar**. Vá ao [guia da Asana para configurar o SSO](https://asana.com/guide/help/premium/authentication#gl-saml) se precisar de mais assistência.

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a Asana.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **Asana**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Asana.**

    ![O link Asana na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-asana-test-user"></a>Criar utilizador de teste Asana

O objetivo desta secção é criar um utilizador chamado Britta Simon em Asana. A Asana suporta o fornecimento automático do utilizador, que está por defeito. Pode encontrar mais detalhes [aqui](asana-provisioning-tutorial.md) sobre como configurar o fornecimento automático do utilizador.

**Se precisar de criar o utilizador manualmente, execute os seguintes passos:**

Nesta secção, cria-se um utilizador chamado Britta Simon em Asana.

1. Em **Asana,** vá à secção **de Equipas** no painel esquerdo. Clique no botão de sinal de mais.

    ![Criar um utilizador de teste AZure AD](./media/asana-tutorial/tutorial_asana_12.png)

2. Digite o e-mail do utilizador como **britta.simon \@ contoso.com** na caixa de texto e, em seguida, selecione **Convidar**.

3. Clique **em Enviar Convite.** O novo utilizador receberá um e-mail na sua conta de e-mail. o utilizador terá de criar e validar a conta.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Asana no Painel de Acesso, deverá ser automaticamente inscrito no Asana para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)

- [Configurar o Provisionamento do Utilizador](asana-provisioning-tutorial.md)