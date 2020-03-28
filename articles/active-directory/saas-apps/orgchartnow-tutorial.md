---
title: 'Tutorial: Integração do Diretório Ativo Azure com a OrgChart Now [) Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o OrgChart Now.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: b96606b5558e0fbb81733b2f548a89bfb38d5f99
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095432"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Tutorial: Integração de Diretório Ativo Azure com orgChart now

Neste tutorial, aprende-se a integrar o OrgChart Now com o Azure Ative Directory (Azure AD).
Integrar o OrgChart Now com o Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao OrgChart Now.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no OrgChart Now (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a OrgChart Now, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* OrgChart Now única subscrição ativada por sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* OrgChart Now suporta **SP** e **IDP** iniciadoS SSO

## <a name="adding-orgchart-now-from-the-gallery"></a>Adicionando OrgChart Agora da galeria

Para configurar a integração do OrgChart Now em Azure AD, precisa de adicionar orgChart Now da galeria à sua lista de aplicações saaS geridas.

**Para adicionar OrgChart Agora na galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva OrgChart Now**, selecione **OrgChart Agora** a partir do painel de resultados e, em seguida, clique em adicionar o botão **adicionar** a aplicação.

     ![OrgChart Agora na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com orgChart Now com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no OrgChart Now.

Para configurar e testar o único sinal de Azure AD com orgChart Now, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure orgchart agora um único sign-on](#configure-orgchart-now-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create OrgChart Now test user](#create-orgchart-now-test-user)** - para ter uma contrapartida de Britta Simon em OrgChart Now que está ligada à representação azure AD do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com orgChart Now, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **OrgChart Now,** selecione **single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** Se desejar configurar a aplicação no modo iniciado **idp,** execute o seguinte passo:

    ![OrgChart Now Domain e URLs informações únicas de inscrição](common/idp-identifier.png)

    Na caixa de texto **identificador,** digite um URL:`https://sso2.orgchartnow.com`

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    ![image](common/both-preintegrated-signon.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>`é o **identificador Azure AD** copiado da secção **OrgChart Now,** descrito mais tarde no tutorial.

6. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

7. Na secção **Configurar orgchart now,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-orgchart-now-single-sign-on"></a>Configure orgchart agora um único sign-on

Para configurar um único sinal no lado **OrgChart Now,** você precisa enviar os metadados da **Federação xML** descarregados e URLs copiados apropriados do portal Azure para a equipa de [suporte OrgChart Now](mailto:ocnsupport@officeworksoftware.com). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No tipo de campo de **nome do utilizador****brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao OrgChart Now.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **OrgChart Now**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **OrgChart Now**.

    ![O link OrgChart Now na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-orgchart-now-test-user"></a>Criar o utilizador de teste OrgChart Now

Para permitir que os utilizadores de Anúncios Azure entrem no OrgChart Now, devem ser aprovisionados no OrgChart Now. 

1. OrgChart Now suporta o fornecimento just-in-time, que é por padrão ativado. Um novo utilizador é criado durante uma tentativa de aceder ao OrgChart Now se ainda não existir. A funcionalidade de provisionamento de utilizadores just-in-time só criará um utilizador **de leitura** quando um pedido SSO provém de um IDP reconhecido e o e-mail na afirmação SAML não for encontrado na lista de utilizadores. Para esta funcionalidade de fornecimento automático, é necessário criar um grupo de acesso intitulado **General** in OrgChart Now. Por favor, siga os passos abaixo para criar um grupo de acesso:

    a. Vá à opção **Manage Groups** depois de clicar na **engrenagem** no canto superior direito da UI.

    ![OrgChart Agora grupos](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Selecione o ícone **Adicionar** e nomeie o grupo **General** e, em seguida, clique em **OK**. 

    ![OrgChart Agora adicionar](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Selecione as pastas(s) que deseja que os utilizadores gerais ou apenas de leitura possam aceder:

    ![Pastas OrgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Bloqueie** as pastas de modo a que apenas os utilizadores da Admin as possam modificar. Em seguida, prima **OK**.

    ![OrgChart Agora bloqueio](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Para criar utilizadores **de Administrador** e **ler/escrever** utilizadores, deve criar manualmente um utilizador para ter acesso ao seu nível de privilégio através do SSO. Para fornecer uma conta de utilizador, execute os seguintes passos:

    a. Inicie sessão na OrgChart Now como administrador de segurança.

    b.  Clique em **Definições** no canto superior direito e depois navegue para **Gerir utilizadores**.

    ![Definições orgChart Now](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Clique em **Adicionar** e execute os seguintes passos:

    ![OrgChart Agora gerir](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * Na caixa de texto ID do **utilizador,** introduza o ID do utilizador como **o\@brittasimon contoso.com**.

    * Na caixa de texto **email Address,** introduza o e-mail de utilizador como **\@brittasimon contoso.com**.

    * Clique em **Adicionar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo OrgChart Now no Painel de Acesso, deve ser automaticamente inscrito no OrgChart Now para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

