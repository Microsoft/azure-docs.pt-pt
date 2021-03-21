---
title: 'Tutorial: Integração do Azure Ative Directory com a Optimizely | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Optimizely.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: c10681a053631a338bd0b9a7d19c5625151a9a5a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515802"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Tutorial: Integração do Azure Ative Directory com a Optimizely

Neste tutorial, aprende-se a integrar a Optimizely com o Azure Ative Directory (Azure AD).
A integração da Optimizely com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Optimizely.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Optimizely (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com a Optimizely, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Otimizar uma subscrição ativada por cada um

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Otimize suporta **SSO** iniciado sp

## <a name="adding-optimizely-from-the-gallery"></a>Adicionar Optimizely da galeria

Para configurar a integração da Optimizely no AD AZure, precisa adicionar Optimizely da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Optimizely da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva Optimizely,** selecione **Optimizely** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![Otimizar na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com optimizemente baseado num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Optimizely.

Para configurar e testar o Azure AD com a Optimizely, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure optimizamente o sign-on único](#configure-optimizely-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Crie o utilizador de teste Optimizely](#create-optimizely-test-user)** - para ter uma contraparte de Britta Simon em Optimizely que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com otimização, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Optimizely,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Optimizely Domain e URLs informações únicas de súmis](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://app.optimizely.net/<instance name>`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `urn:auth0:optimizely:contoso`

    > [!NOTE]
    > Estes valores não são reais. Irá atualizar o valor com o URL e o Identificador de Inscrição real, que é explicado mais tarde no tutorial. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. A sua aplicação Optimizely espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique **em Editar** o ícone para abrir o diálogo **dos Atributos do Utilizador.**

    ![Screenshot que mostra o diálogo "Atributos do Utilizador" com o ícone "Editar" no top-right selecionado.](common/edit-attribute.png)

6. Além de acima, a aplicação Optimizely espera que alguns mais atributos sejam repercutidos na resposta SAML. Na secção **'Reclamações** de Utilizador' no diálogo **'Atributos do Utilizador',** execute os seguintes passos para adicionar o atributoken SAML, tal como mostrado na tabela abaixo:

    | Name | Atributo de origem |
    | ---------------| --------------- |
    | e-mail | user.mail |
    
    a. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![Screenshot que mostra o diálogo "User Claims" com as ações "Adicionar nova reivindicação" e "Salvar" em destaque.](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **'Nome',** digite o nome do atributo indicado para esta linha.

    c. Deixe o **Espaço Namespace** em branco.

    d. Selecione Fonte como **Atributo**.

    e. A partir da lista **de atributos Source,** digite o valor de atributo mostrado para esta linha.

    f. Clique **em Ok**

    exemplo, Clique em **Guardar**.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar Optimizely,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-optimizely-single-sign-on"></a>Configurar otimizar Sign-On única

1. Para configurar um único sign-on no lado **Optimizely,** contacte o seu Gestor de Conta Optimizely e forneça o Certificado descarregado **(Base64)** e URLs copiados apropriados.

2. Em resposta ao seu e-mail, a Optimizely fornece-lhe os valores sign on URL (SSO iniciado sp) e os valores de Identifier (Service Provider Entity ID).

    a. Copie o **URL SSO iniciado** pelo SP fornecido pela Optimizely e cole-o na caixa de texto **Sign on URL** na secção **de Configuração SAML Básica** no portal Azure.

    b. Copie o **ID da Entidade fornecedora de serviços** fornecido pela Optimizely e cole-o na caixa de texto **identifier** na secção **de Configuração SAML Básica** no portal Azure.

3. Numa janela de navegador diferente, inscreva-se na sua aplicação Optimizely.

4. Clique no nome da conta no canto superior direito e, em seguida, **definições de conta**.

    ![Screenshot que mostra o nome da conta selecionado no canto superior direito, com "Definições de Conta" selecionadas no menu.](./media/optimizely-tutorial/tutorial_optimizely_09.png)

5. No separador Conta, verifique a caixa **Ativar SSO** sob único sinal na secção **'Vista Geral'.**
  
    ![Azure AD Single Sign-On](./media/optimizely-tutorial/tutorial_optimizely_10.png)

6. Clique em **Guardar**

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso à Optimizely.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **Optimizely**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, **selecione Optimizely**.

    ![O link Optimizely na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-optimizely-test-user"></a>Criar o utilizador de teste Optimizely

Nesta secção, cria-se um utilizador chamado Britta Simon em Optimizely.

1. Na página inicial, **selecione Separador de Colaboradores.**

2. Para adicionar um novo colaborador ao projeto, clique em **Novo Colaborador.**
   
    ![Screenshot que mostra a página inicial optimizdamente com o separador "Colaboradores" e o botão "Novo Colaborador" selecionado.](./media/optimizely-tutorial/create_aaduser_10.png)

3. Preencha o endereço de e-mail e atribua-lhes uma função. Clique **em Convidar.**

    ![Criar um utilizador de teste AZure AD](./media/optimizely-tutorial/create_aaduser_11.png)

4. Recebem um convite por e-mail. Utilizando o endereço de e-mail, eles têm que fazer login na Optimizely.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Optimizely no Painel de Acesso, deverá ser automaticamente inscrito no Optimizely para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)