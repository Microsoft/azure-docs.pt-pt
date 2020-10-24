---
title: 'Tutorial: Integração do Azure Ative Directory com Riskware Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o Riskware.
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
ms.openlocfilehash: e33664501483280978c67b100cd56a714ef63a30
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520766"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Tutorial: Integração do Azure Ative Directory com Riskware

Neste tutorial, aprende-se a integrar o Riskware com o Azure Ative Directory (Azure AD).
A integração do Riskware com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Riskware.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Riskware (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o Riskware, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Subscrição ativada por um único signware de risco

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Riskware suporta **SSO** iniciado SP

## <a name="adding-riskware-from-the-gallery"></a>Adicionar Riskware da galeria

Para configurar a integração do Riskware no Azure AD, é necessário adicionar o Riskware da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Riskware da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Riskware**, selecione **Riskware** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Risco na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com o Riskware baseado num utilizador de teste chamado **Britta Simon**.
Para um único início de saúde a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Riskware.

Para configurar e testar o Azure AD com um único sinal de acesso com o Riskware, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o signo único](#configure-riskware-single-sign-on)** do risco - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Riskware test user](#create-riskware-test-user)** - para ter uma contraparte de Britta Simon em Riskware que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o Riskware, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Riskware,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Domínio de risco e informações únicas de início de saúde](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão:
    
    | Ambiente| Padrão url|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PROD| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. Na caixa de texto **identifier (Entity ID),** digite o URL:
    
    | Ambiente| Padrão url|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | PROD| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > O valor do sinal de URL não é real. Atualize o valor com o URL de Sign-On real. Contacte [a equipa de suporte do Cliente Riskware](mailto:support@pansoftware.com.au) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configurar o Riskware,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-riskware-single-sign-on"></a>Configurar o Sign-On único de risco

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Riskware como administrador.

1. No topo à direita, clique em **Manutenção** para abrir a página de manutenção.

    ![As configurações do risco mantêm](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na página de manutenção, clique em **Autenticação.**

    ![Configuração de risco authen](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. Na página **de Configuração de Autenticação,** execute os seguintes passos:

    ![Configuração de risco authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Selecione **Digite** como **SAML** para autenticação.

    b. Na caixa de texto **code,** digite o seu código como AZURE_UAT.

    c. Na caixa de texto **Descrição,** digite a sua descrição como Configuração AZURE para SSO.

    d. Na caixa de texto **'Sign On Page'** único, cole o valor URL de **login,** que copiou do portal Azure.

    e. Na caixa de texto **'Iniciar página',** cole o valor **URL logout,** que copiou do portal Azure.

    f. Na caixa de texto **do Campo pós-formulário,** digite o nome de campo presente na Resposta pós-publicação que contenha SAML como SAMLResponse

    exemplo, Na caixa de texto **XML Identity Tag Name,** o atributo do tipo, que contém o identificador único na resposta SAML como o NameID.

    h. Abra o **Metdata Xml** descarregado do portal Azure no bloco de notas, copie o certificado do ficheiro Metadados e cole-o na caixa de texto **do Certificado**

    i. Na caixa de texto **URL do consumidor,** cole o valor do URL de **resposta,** que obtém da equipa de suporte.

    j. Na caixa de texto **emitente,** cole o valor do **Identifier,** que obtém da equipa de suporte.

    > [!Note]
    > Contacte [a equipa de suporte do Cliente Riskware](mailto:support@pansoftware.com.au) para obter estes valores

    k. Selecione **Use POST** checkbox.

    l. Selecione **Use SAML Request checkbox.**

    m. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador**`brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao Riskware.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Riskware**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Riskware**.

    ![O link Riskware na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-riskware-test-user"></a>Criar utilizador de teste de risco

Para permitir que os utilizadores de Azure AD inscrevam-se no Riskware, devem ser a provisionados no Riskware. No Riskware, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no Riskware como Administrador de Segurança.

1. No topo à direita, clique em **Manutenção** para abrir a página de manutenção. 

    ![A configuração do risco mantém](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na página de manutenção, clique em **Pessoas.**

    ![Pessoas de configuração de risco](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Selecione **Details** e execute os seguintes passos:

    ![Detalhes da configuração do risco](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. **Selecione Pessoa Tipo** como Empregado.

    b. Na caixa de texto **Name First,** insira o primeiro nome de utilizador como **Britta**.

    c. Na caixa de texto **sobrenome,** insira o último nome de utilizador como **Simon**.

1. No separador **Segurança,** execute os seguintes passos:

    ![Segurança de configuração do risco](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. Na secção **de autenticação,** selecione o modo **de autenticação,** que tem configuração como Configuração AZURE para SSO.

    b. Na secção **Detalhes de Logon,** na caixa de texto **do Utilizador,** introduza o e-mail do utilizador como `brittasimon@contoso.com` .

    c. Na caixa de texto **da palavra-passe,** introduza a palavra-passe do utilizador.

1. No separador **Organização,** execute os seguintes passos:

    ![Configuração de risco org](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Selecione a opção como organização **Level1.**

    b. Na secção **local de trabalho primária da pessoa,** na caixa de texto **localização,** digite a sua localização.

    c. Na secção **de empregados,** selecione **Estatuto do Empregado** como Casual.

    d. Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no telha de risco no Painel de Acesso, deverá ser automaticamente inscrito no Riskware para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)