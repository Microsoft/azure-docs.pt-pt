---
title: 'Tutorial: Integração do Diretório Ativo Azure com Perception United States (Non-UltiPro) | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Perception United States (Non-UltiPro).
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
ms.openlocfilehash: 3fc16a869c176074467a0b21880937d7753602b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515530"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Tutorial: Integração do Diretório Ativo Azure com a Perception United States (Non-UltiPro)

Neste tutorial, aprende-se a integrar a Perception United States (Non-UltiPro) com o Azure Ative Directory (Azure AD).
Integrar a Perceção Estados Unidos (Não-UltiPro) com Azure AD fornece-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso à Perception United States (Non-UltiPro).
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Perception United States (Non-UltiPro) (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração Azure AD com a Perception United States (Non-UltiPro), precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Perception United States (Non-UltiPro) assinatura única ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Perception United States (Non-UltiPro) apoia **IDP** iniciado SSO

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Adicionar Perceção Estados Unidos (Não-UltiPro) da galeria

Para configurar a integração da Perception United States (Non-UltiPro) no AD Azure, é necessário adicionar Perception United States (Non-UltiPro) da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Perception United States (Non-UltiPro) da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Perception United States (Non-UltiPro)**, selecione **Perception United States (Non-UltiPro)** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![Perceção Estados Unidos (Não-UltiPro) na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on with Perception United States (Non-UltiPro) com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Perception United States (Non-UltiPro).

Para configurar e testar o Azure AD com a Perception United States (Non-UltiPro), é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure a Perceção Estados Unidos (Non-UltiPro) Single Sign-On](#configure-perception-united-states-non-ultipro-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Perception United States (Non-UltiPro) test user](#create-perception-united-states-non-ultipro-test-user)** - para ter uma contraparte de Britta Simon in Perception United States (Non-UltiPro) que está ligada à representação AD Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD um único sinal de insuflação com Perception United States (Non-UltiPro), execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Perception United States (Non-UltiPro),** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na **configuração single Sign-On com página SAML,** execute os seguintes passos:

    ![Perceção Estados Unidos (Não-UltiPro) Domínio e URLs informações únicas de súmis](common/idp-intiated.png)

    a. Na caixa de texto **identifier,** digite um URL: `https://perception.kanjoya.com/sp`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://perception.kanjoya.com/sso?idp=<entity_id>`

    c. A aplicação **Perception United States (Non-UltiPro)** requer que o valor do **identificador Azure AD** como <entity_id>, que obterá da secção **Configuração Perception United States (Non-UltiPro)** para ser codificado. Para obter o valor codificado uri, utilize o seguinte link: **http://www.url-encode-decode.com/** .

    d. Depois de obter o valor codificado uri combine-o com o **URL de resposta,** como mencionado abaixo...

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    e. Cole o valor acima na caixa de texto **URL answer.**

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configurar Perceção Estados Unidos (Não-UltiPro),** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL   

### <a name="configure-perception-united-states-non-ultipro-single-sign-on"></a>Configure Perceção Estados Unidos (Não-UltiPro) Single Sign-On

1. Em outra janela do navegador, inscreva-se no site da empresa Perception United States (Non-UltiPro) como administrador.

2. Na barra de ferramentas principal, clique em **Definições de Conta**.

    ![Screenshot que mostra "Definições de conta" selecionadas a partir da barra de ferramentas principal.](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

3. Na página **Definições de Conta,** execute os seguintes passos:

    ![Utilizador da Perceção Estados Unidos (Não-UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. Na caixa de texto **Nome da Empresa,** digite o nome da **Empresa.**
    
    b. Na caixa de texto **'Nome** da Conta', digite o nome da **Conta**.

    c. Na **caixa de texto Reply-To Por email,** digite o **e-mail** válido.

    d. Selecione **O Fornecedor de Identidade SSO** como **SAML 2.0**.

4. Na página **de Configuração SSO,** execute os seguintes passos:

    ![Perceção Estados Unidos (Não-UltiPro) SSOConfig](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Selecione **O Tipo SAML NameID** como **EMAIL**.

    b. Na caixa de texto **SSO Configuration Name,** digite o nome da sua **Configuração**.
    
    c. Na caixa de texto **Name Provider de Identidade,** cole o valor do **Identificador AD Azure,** que copiou do portal Azure. 

    d. Na **caixa de texto do domínio SAML,** insira o domínio como @contoso.com .

    e. Clique em **Upload Again** para carregar o ficheiro **XML dos metadados.**

    f. Clique em **Atualizar**.

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso à Perception United States (Non-UltiPro).

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** selecione **Perception United States (Non-UltiPro)**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Perception United States (Non-UltiPro)**.

    ![A ligação Perception United States (Non-UltiPro) na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-perception-united-states-non-ultipro-test-user"></a>Criar utilizador de teste Perception United States (Non-UltiPro)

Nesta secção, cria-se um utilizador chamado Britta Simon in Perception United States (Non-UltiPro). Trabalhe com a [equipa de apoio Perception United States (Non-UltiPro)](https://www.ultimatesoftware.com/Contact/ContactUs) para adicionar os utilizadores na plataforma Perception United States (Non-UltiPro).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Perception United States (Non-UltiPro) no Painel de Acesso, deverá ser automaticamente inscrito na Perception United States (Non-UltiPro) para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)