---
title: 'Tutorial: Integração do Diretório Ativo Azure com | springcm Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o SpringCM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: jeedes
ms.openlocfilehash: 9cfc48e3fdb96ba5b63b28288a801095f7b36f43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97589910"
---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Tutorial: Integração do Diretório Ativo Azure com a SpringCM

Neste tutorial, aprende-se a integrar o SpringCM com o Azure Ative Directory (Azure AD).
A integração da SpringCM com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a SpringCM.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no SpringCM (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a SpringCM, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura ativada única springCM

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* SpringCM suporta **SSO** iniciado sp

## <a name="adding-springcm-from-the-gallery"></a>Adicionando SpringCM da galeria

Para configurar a integração da SpringCM em Azure AD, é necessário adicionar springcm da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar SpringCM da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, clique no botão **de aplicação Novo** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite SpringCM,** selecione **SpringCM** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![SpringCM na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com springcm baseado num utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na SpringCM.

Para configurar e testar o Azure AD com springcm, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único springCM](#configure-springcm-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create SpringCM test user](#create-springcm-test-user)** - para ter uma contraparte de Britta Simon em SpringCM que está ligada à representação AZure AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com springcm, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **SpringCM,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![SpringCM Domain e URLs informações únicas de súmis](common/sp-signonurl.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte [a equipa de suporte do cliente SpringCM](https://knowledge.springcm.com/support) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Raw)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificateraw.png)

6. Na secção **Configurar o SpringCM,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-springcm-single-sign-on"></a>Configurar springcm single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no site da empresa **SpringCM** como administrador.

1. No menu em cima, clique em **GO TO,** clique em **Preferências**, e, em seguida, na secção **Preferências de Conta,** clique em **SSO SAML**.

    ![SAML SSO](./media/spring-cm-tutorial/ic797051.png "SAML SSO")

1. Na secção configuração do fornecedor de identidade, execute os seguintes passos:

    ![Configuração do fornecedor de identidade](./media/spring-cm-tutorial/ic797052.png "Configuração do fornecedor de identidade")

    a. Para fazer o upload do certificado de Diretório Ativo Azure descarregado, clique em **Select Emiter Certificate** ou **Change Emiter Certificate**.

    b. Na caixa de texto **emitente,** cole o valor **do identificador Azure AD,** que copiou do portal Azure.

    c. Na caixa de texto iniciada do **Fornecedor de Serviços (SP) Endpoint,** cole o valor URL do **Login,** que copiou a partir do portal Azure.

    d. Selecione **SAML Ativado** como **Ativação**.

    e. Clique em **Guardar**.

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao SpringCM.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **SpringCM**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **SpringCM**.

    ![O link SpringCM na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-springcm-test-user"></a>Criar utilizador de teste SpringCM

Para permitir que os utilizadores do Azure Ative Directory inscrevam-se na SpringCM, devem ser a provisionados na SpringCM. No caso da SpringCM, o provisionamento é uma tarefa manual.

> [!NOTE]
> Para obter mais informações, consulte [Criar e Editar um Utilizador SpringCM.](http://community.springcm.com/s/article/Create-and-Edit-a-SpringCM-User-1619481053) 

**Para prestar uma conta de utilizador à SpringCM, execute os seguintes passos:**

1. Inscreva-se no site da empresa **SpringCM** como administrador.

1. Clique **em GOTO** e, em seguida, clique em **ADDRESS BOOK**.

    ![Criar Utilizador](./media/spring-cm-tutorial/ic797054.png "Criar Utilizador")

1. Clique **em Criar Utilizador.**

1. Selecione uma **função de utilizador**.

1. Selecione **Enviar e-mail de ativação**.

1. Digite o primeiro nome, apelido e endereço de e-mail de uma conta de utilizador válida do Azure Ative Directory que pretende apresentar nas caixas de texto relacionadas.

1. Adicione o utilizador a um **grupo de Segurança**.

1. Clique em **Guardar**.

   > [!NOTE]
   > Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador springcm ou APIs fornecidas pela SpringCM para fornecer contas de utilizador Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SpringCM no Painel de Acesso, deverá ser automaticamente inscrito no SpringCM para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)