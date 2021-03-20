---
title: 'Tutorial: Integração do Diretório Ativo Azure com grafite | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Hosted Graphite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 786c6309f685af6b8e42108c490ce86c7844bc81
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92443233"
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Tutorial: Integração do Diretório Ativo Azure com Grafite Hospedada

Neste tutorial, aprende-se a integrar aGraphite Hospedada com o Azure Ative Directory (Azure AD).
Integrar a Grafite Hospedada com Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso aGraphite Hospedado.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Grafite Hospedada (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a Grafite Hospedada, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada por grafite única hospedada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Graphite hospedado suporta **SP e IDP** iniciado SSO
* Grafite hospedada suporta **provisão do** utilizador Just In Time

## <a name="adding-hosted-graphite-from-the-gallery"></a>Adicionar Grafite Hospedada da galeria

Para configurar a integração da Grafite Hospedada em AZure AD, é necessário adicionar grafite hospedado da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Grafite Hospedado da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Grafite Hospedeira,** selecioneGraphite **Hospedeira** do painel de resultados e clique em Adicionar o botão **Adicionar** para adicionar a aplicação.

     ![Grafite hospedada na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com grafite hospedado com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sessão a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado emGraphite Hospedado.

Para configurar e testar o Azure AD com grafite acolhida, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o signo único](#configure-hosted-graphite-single-sign-on)** de grafite alojado - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Hosted Graphite test user](#create-hosted-graphite-test-user)** - para ter uma contraparte de Britta Simon emGraphite Hospedada que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com grafite acolô, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **de Grafite Hospedada,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![A screenshot mostra a Configuração BÁSICA SAML, onde pode introduzir o Identificador, Responder U R L e selecionar Guardar.](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://www.hostedgraphite.com/metadata/<user id>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://www.hostedgraphite.com/complete/saml/<user id>`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![Screenshot mostra Definir U R Ls adicionais onde pode introduzir um sinal em U R L.](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://www.hostedgraphite.com/login/saml/<user id>/`

    > [!NOTE]
    > Por favor, note que estes não são os valores reais. Tem de atualizar estes valores com o identificador real, URL de resposta e sinal de URL. Para obter estes valores, pode aceder à configuração DOMS do Access->no seu lado da Aplicação ou contactar [a equipa de suporte de grafite hospedada](mailto:help@hostedgraphite.com).

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

7. Na secção **de Grafite Acolôdo,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-hosted-graphite-single-sign-on"></a>Configurar grafite Sign-On

1. Inscreva-se no seu inquilino de Grafite hospedado como administrador.

2. Aceda à **página de configuração SAML** na barra lateral **(Acesso -> configuração SAML**).

    ![A screenshot mostra o menu Access com configuração SAML selecionada.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

3. Confirme que estes URls correspondem à sua configuração feita na secção **de Configuração SAML Básica** do portal Azure.

    ![A screenshot mostra a configuração básica do SAML.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

4. Nas caixas de texto de  **ID de Entidade ou Emitente** e **SSO Login URL,** cole o valor do Identificador de **Ad Azure** e URL de **login** que copiou do portal Azure.

    ![A screenshot mostra entradas para Fornecedor de Identidade.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)

5. Selecione **Read-only** como **função de utilizador predefinida**.

    ![A screenshot mostra a função de utilizador predefinida, que é apenas para ler.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

6. Abra o certificado codificado base-64 no bloco de notas descarregado do portal Azure, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o para a caixa de texto **do Certificado X.509.**

    ![A screenshot mostra o certificado X dot 509.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

7. Clique no botão **Guardar.**

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

Nesta secção, você permite que Britta Simon utilize a Azure single sign-on, concedendo acesso aGraphite Hospedado.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecioneGraphite **Anfitrião**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, **selecioneGraphite Anfitrião**.

    ![O link de grafite hospedado na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-hosted-graphite-test-user"></a>Criar utilizador de teste de grafite hospedado

Nesta secção, um utilizador chamado Britta Simon é criado emGraphite Hospedado. A Graphite hospedada suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir emGraphite Hospedado, um novo é criado após a autenticação.

> [!NOTE]
> Se necessitar de criar um utilizador manualmente, tem de contactar a equipa de suporte de grafite hospedeira através de <mailto:help@hostedgraphite.com> .

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo de Grafite Hospedado no Painel de Acesso, deverá ser automaticamente inscrito no Grafite Hospedado para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)