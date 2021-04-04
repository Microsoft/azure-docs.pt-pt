---
title: 'Tutorial: Integração do Azure Ative Directory com SilkRoad Life Suite | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a SilkRoad Life Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 89d086ce136885e203e300f04bdbf3ade0affeb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516057"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Tutorial: Integração do Azure Ative Directory com a SilkRoad Life Suite

Neste tutorial, você aprende a integrar a SilkRoad Life Suite com o Azure Ative Directory (Azure AD).
A integração da SilkRoad Life Suite com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso à SilkRoad Life Suite.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na SilkRoad Life Suite (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a SilkRoad Life Suite, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* SilkRoad Life Suite assinatura ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* SilkRoad Life Suite suporta **SSO** iniciado SP

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Adicionando SilkRoad Life Suite da galeria

Para configurar a integração da SilkRoad Life Suite em Azure AD, você precisa adicionar SilkRoad Life Suite da galeria à sua lista de aplicações geridas saaS.

**Para adicionar SilkRoad Life Suite da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SilkRoad Life Suite**, selecione **SilkRoad Life Suite** a partir do painel de resultados e clique em **Adicionar** botão adicionar a aplicação.

    ![SilkRoad Life Suite na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com SilkRoad Life Suite com base em um utilizador de teste chamado **Britta Simon**.
Para um único sign-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na SilkRoad Life Suite.

Para configurar e testar o Azure AD com a SilkRoad Life Suite, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure silkRoad Life Suite Single Sign-On](#configure-silkroad-life-suite-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create SilkRoad Life Suite test user](#create-silkroad-life-suite-test-user)** - para ter uma contraparte de Britta Simon na SilkRoad Life Suite que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com SilkRoad Life Suite, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **SilkRoad Life Suite,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** se tiver **um ficheiro de metadados do Fornecedor de Serviços,** execute os seguintes passos:

    > [!NOTE]
    > O ficheiro **de metadados do Fornecedor de Serviços** será explicado mais tarde neste tutorial.

    a. Clique **em Carregar o ficheiro de metadados**.

    ![A screenshot mostra a configuração básica do SAML com o link de ficheiro de metadados upload.](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o ficheiro de metadados e clique em **Upload**.

    ![A screenshot mostra uma caixa de diálogo onde pode selecionar e carregar um ficheiro.](common/browse-upload-metadata.png)

    c. Uma vez que o ficheiro de metadados é carregado com sucesso, os valores de URL **de identificação** e **resposta** são preenchidos automaticamente na secção de Configuração BÁSICA SAML:

    ![A screenshot mostra a Configuração BÁSICA SAML, onde pode introduzir o Identificador, Responder U R L e selecionar Guardar.](common/sp-identifier-reply.png)

    > [!Note]
    > Se os valores de URL **de identificação** e **resposta** não estiverem a ser automaticamente poluídos, preencha os valores manualmente de acordo com o seu requisito.

    d. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.silkroad-eng.com/Authentication/`

5. Na secção **de Configuração Básica SAML,** se não tiver **um ficheiro de metadados do Fornecedor de Serviços,** execute os seguintes passos:

    ![SilkRoad Life Suite Domain e URLs informações únicas de súpr-on](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.silkroad-eng.com/Authentication/`

    b. Na caixa **identifier,** digite um URL utilizando o seguinte padrão:

    - `https://<subdomain>.silkroad-eng.com/Authentication/SP`
    - `https://<subdomain>.silkroad.com/Authentication/SP`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    - `https://<subdomain>.silkroad-eng.com/Authentication/`
    - `https://<subdomain>.silkroad.com/Authentication/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Sign-On real, identifier e responder URL. Contacte [a equipa de suporte do cliente SilkRoad Life Suite](https://www.silkroad.com/locations/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

7. Na secção **set up SilkRoad Life Suite,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-silkroad-life-suite-single-sign-on"></a>Configurar SilkRoad Life Suite Single Sign-On

1. Inscreva-se no site da empresa SilkRoad como administrador.

    > [!NOTE]
    > Para obter acesso ao pedido de autenticação SilkRoad Life Suite para configurar a federação com o Microsoft Azure AD, contacte a SilkRoad Support ou o seu representante da SilkRoad Services.

1. Vá ao **Fornecedor de Serviços** e, em seguida, clique em **Detalhes da Federação**.

    ![A screenshot mostra detalhes da Federação selecionados do Fornecedor de Serviços.](./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png)

1. Clique **em Baixar Metadados da Federação** e, em seguida, guarde o ficheiro de metadados no seu computador. Utilize metadados da Federação Descarregada como **ficheiro de metadados do Fornecedor de Serviços** na secção **de Configuração Básica SAML** no portal Azure.

    ![A screenshot mostra a ligação de metadados da Federação de Descarregamento.](./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png)

1. Na sua aplicação **SilkRoad,** clique em **Fontes de Autenticação**.

    ![A screenshot mostra fontes de autenticação selecionadas.](./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png) 

1. Clique **em Adicionar Fonte de Autenticação**.

    ![A screenshot mostra a ligação Add Authentication Source.](./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png)

1. Na secção **Fonte de Autenticação adicionar,** execute os seguintes passos:

    ![O Screenshot mostra Adicionar Fonte de Autenticação com o Fornecedor de Identidade Criar utilizando o botão de dados de ficheiro selecionado.](./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png)
  
    a. No **âmbito da Opção 2 - Ficheiro de Metadados,** clique em **Procurar** para carregar o ficheiro de metadados descarregado a partir do portal Azure.
  
    b. Clique **em Criar Fornecedor de Identidade utilizando dados de ficheiros.**

1. Na secção **Fontes de Autenticação,** clique em **Editar**.

    ![A screenshot mostra Fontes de Autenticação com a opção Edit selecionada.](./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png)

1. No diálogo **Editar Autenticação Fonte,** execute os seguintes passos:

    ![A screenshot mostra a caixa de diálogo Editar Autenticação Fonte onde pode introduzir os valores descritos.](./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png)

    a. Como **Ativado**, selecione **Sim**.

    b. Na caixa de texto **EntityId,** cole o valor do **Identificador AD Azure** que copiou do portal Azure.

    c. Na caixa de texto **IdP Description,** digite uma descrição para a sua configuração (por exemplo: *Azure AD SSO*).

    d. Na caixa de texto **do Ficheiro Metadados,** faça o upload do ficheiro **metadados** que descarregou a partir do portal Azure.
  
    e. Na caixa de texto **IdP Name,** digite um nome específico à sua configuração (por exemplo: *Azure SP*).
  
    f. Na caixa de texto URL do **Serviço Logout,** cole o valor do **URL logout** que copiou do portal Azure.

    exemplo, Na caixa de texto **URL de serviço de acesso,** cole o valor do URL de **login** que copiou do portal Azure.

    h. Clique em **Guardar**.

1. Desative todas as outras fontes de autenticação.

    ![A screenshot mostra Fontes de Autenticação onde pode desativar outras fontes. ](./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png)

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso à SilkRoad Life Suite.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações,** em seguida, selecione **SilkRoad Life Suite**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **SilkRoad Life Suite**.

    ![O link SilkRoad Life Suite na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-silkroad-life-suite-test-user"></a>Crie o utilizador de teste SilkRoad Life Suite

Nesta secção, você cria um utilizador chamado Britta Simon em SilkRoad Life Suite. Trabalhe com a [equipa de suporte do Cliente SilkRoad Life Suite](https://www.silkroad.com/locations/) para adicionar os utilizadores na plataforma SilkRoad Life Suite. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo silkRoad Life Suite no Painel de Acesso, deverá ser automaticamente inscrito na Suite SilkRoad Life para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)