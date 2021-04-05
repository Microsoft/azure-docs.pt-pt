---
title: 'Tutorial: Integração do Azure Ative Directory com o OrgChart Now | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o OrgChart Now.
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
ms.openlocfilehash: fc6b90be8e951c0e30c99d0bb16f85e49cf991f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522330"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Tutorial: Integração do Azure Ative Directory com o OrgChart Now

Neste tutorial, aprende-se a integrar o OrgChart Now com o Azure Ative Directory (Azure AD).
A integração do OrgChart Now com a Ad Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a OrgChart Now.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no OrgChart Now (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o OrgChart Now, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* OrgChart Agora subscrição ativada única

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* OrgChart Now suporta **SP** e **IDP** iniciado SSO

## <a name="adding-orgchart-now-from-the-gallery"></a>Adicionar OrgChart Agora da galeria

Para configurar a integração do OrgChart Now no AD Azure, é necessário adicionar o OrgChart Now da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar OrgChart Now da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **OrgChart Now**, selecione **OrgChart Now** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![OrgChart Agora na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o OrgChart Now com base num utilizador de teste chamado **Britta Simon**.
Para um único s-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no OrgChart Now.

Para configurar e testar o Azure AD com o OrgChart Now, tem de completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure OrgChart Now Single Sign-On](#configure-orgchart-now-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create OrgChart Now test user](#create-orgchart-now-test-user)** - para ter uma contraparte de Britta Simon em OrgChart Now que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com o OrgChart Now, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **OrgChart Now,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute o seguinte passo:

    ![OrgChart Now Domain e URLs informações únicas de entrada](common/idp-identifier.png)

    Na caixa de texto **identifier,** digite um URL:  `https://sso2.orgchartnow.com`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![image](common/both-preintegrated-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>` é o **identificador AZure AD** copiado da secção **Configuração OrgChart Now,** descrito mais tarde em tutorial.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

7. Na secção **Configurar OrgChart Now,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-orgchart-now-single-sign-on"></a>Configure OrgChart Agora Single Sign-On

Para configurar um único sign-on **orgChart Now** lado, você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipa de [suporte OrgChart Now](mailto:ocnsupport@officeworksoftware.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao OrgChart Now.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **OrgChart Now**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **OrgChart Now**.

    ![O link OrgChart Now na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-orgchart-now-test-user"></a>Criar orgChart agora utilizador de teste

Para permitir que os utilizadores de Azure AD iniciem sessão no OrgChart Now, devem ser a provisionados no OrgChart Now. 

1. O OrgChart Now suporta o provisionamento just-in-time, que é por padrão ativado. Um novo utilizador é criado durante uma tentativa de aceder ao OrgChart Now se ainda não existir. A funcionalidade de provisionamento do utilizador just-in-time só criará um utilizador **apenas de leitura** quando um pedido de SSO provém de um IDP reconhecido e o e-mail na afirmação SAML não é encontrado na lista de utilizadores. Para esta funcionalidade de provisionamento automático, precisa de criar um grupo de acesso intitulado **General** em OrgChart Now. Siga os passos abaixo para criar um grupo de acesso:

    a. Aceda à opção **'Gerir Grupos'** depois de clicar na **engrenagem** no canto superior direito da UI.

    ![Grupos OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Selecione o ícone **Adicionar** e nomeie o grupo **General** e, em seguida, clique em **OK**. 

    ![OrgChart Agora adicionar](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Selecione as pastas que deseja que os utilizadores gerais ou apenas de leitura possam aceder:

    ![Pastas OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Bloqueie** as pastas para que apenas os utilizadores de Administração as possam modificar. Em seguida, prima **OK**.

    ![OrgChart Agora travar](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Para criar utilizadores **de Administração** e **ler/escrever** utilizadores, tem de criar manualmente um utilizador para ter acesso ao seu nível de privilégio através do SSO. Para obter uma conta de utilizador, execute os seguintes passos:

    a. Faça login no OrgChart Now como administrador de segurança.

    b.  Clique em **Definições** no canto superior direito e, em seguida, navegue para **Gerir utilizadores**.

    ![Definições orgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Clique em **Adicionar** e execute os seguintes passos:

    ![OrgChart Agora gerir](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * Na caixa de texto **do ID** do utilizador, introduza o ID do Utilizador como **\@ brittasimon contoso.com**.

    * Na caixa de texto **do Endereço de E-mail,** insira o e-mail do utilizador como **\@ brittasimon contoso.com**.

    * Clique em **Adicionar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo OrgChart Now no Painel de Acesso, deverá ser automaticamente inscrito no OrgChart Now para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)