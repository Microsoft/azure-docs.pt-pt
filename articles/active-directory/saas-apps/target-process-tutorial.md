---
title: 'Tutorial: Integração do Diretório Ativo Azure com | targetProcess Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o TargetProcess.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: ef21ba46ff64ec2b3c47e9659660fd51f84fbf2e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92521225"
---
# <a name="tutorial-azure-active-directory-integration-with-targetprocess"></a>Tutorial: Integração do Diretório Ativo Azure com TargetProcess

Neste tutorial, aprende-se a integrar o TargetProcess com o Azure Ative Directory (Azure AD).
A integração do TargetProcess com Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso ao TargetProcess.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no TargetProcess (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração Azure AD com o TargetProcess, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada para o TargetProcess

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* TargetProcess suporta **SSO** iniciado SP
* TargetProcess suporta **provisão do** utilizador just in time

## <a name="adding-targetprocess-from-the-gallery"></a>Adicionar TargetProcess a partir da galeria

Para configurar a integração do TargetProcess em Ad Azure, precisa adicionar TargetProcess da galeria à sua lista de aplicações geridas do SaaS.

**Para adicionar TargetProcess na galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **TargetProcess**, selecione **TargetProcess** a partir do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![TargetProcess na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar Azure AD SSO

Nesta secção, configura e testa o Azure AD com o TargetProcess com base num utilizador de teste chamado **Britta Simon**.
Para um único pré-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no TargetProcess.

Para configurar e testar o Azure AD com o TargetProcess, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Target Process Single Sign-On](#configure-targetprocess-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create TargetProcess test user](#create-targetprocess-test-user)** - para ter uma contraparte de Britta Simon no TargetProcess que está ligada à representação AD AZure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o TargetProcess, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **TargetProcess,** selecione **'Único s-on'.**

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Informações de súmis de processamento de alvos e URLs](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<subdomain>.tpondemand.com/`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<subdomain>.tpondemand.com/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente TargetProcess](mailto:support@targetprocess.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **'Configurar',** copy's' (S) apropriados, de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-targetprocess-single-sign-on"></a>Configurar o Sign-On único do target Process Process

1. Para automatizar a configuração dentro **do TargetProcess,** é necessário instalar a **extensão do navegador 'As aplicações' Secure's,** clicando **em instalar a extensão**.

    ![image](./media/target-process-tutorial/install_extension.png)

1. Depois de adicionar extensão ao navegador, clique na **configuração TargetProcess** irá direcioná-lo para a aplicação TargetProcess. A partir daí, forneça as credenciais de administração para assinar no TargetProcess. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração](common/setup-sso.png)

    **Se pretender configurar a aplicação manualmente, execute os seguintes passos:**

1. Iniciar sôm no seu TargetProcess como administrador.

1. No menu em cima, clique em **Configuração**.

    ![Configuração](./media/target-process-tutorial/tutorial_target_process_05.png)

1. Clique no **separador Definições.**

    ![Definições](./media/target-process-tutorial/tutorial_target_process_06.png)

1. Clique **no separador 'S'c.'** único.

    ![clique em Sign-On Única](./media/target-process-tutorial/tutorial_target_process_07.png)

1. No diálogo de definições de inscrição única, execute os seguintes passos:

    ![Configurar Sign-On Individuais](./media/target-process-tutorial/tutorial_target_process_08.png)

    a. Clique **em Ativar o Sign-on único.**

    b. Na caixa de texto **URL de acesso,** cole o valor do URL de **login** que copiou do portal Azure.

    c. Abra o seu certificado descarregado no bloco de notas, copie o conteúdo e, em seguida, cole-o na caixa de texto **Certificate.**

    d. clique **em Enable JIT Provisioning**.

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
  
    b. No tipo de campo **nome de utilizador** **brittasimon \@ yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, permite que Britta Simon utilize o Azure como único sinal, concedendo acesso ao TargetProcess.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **TargetProcess**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **TargetProcess**.

    ![O link TargetProcess na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-targetprocess-test-user"></a>Criar utilizador de teste targetProcess

Nesta secção, um utilizador chamado Britta Simon é criado no TargetProcess. O TargetProcess suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no TargetProcess, um novo é criado após a autenticação.

> [!Note]
> Se precisar de criar um utilizador manualmente, contacte a [equipa de suporte targetProcess](mailto:support@targetprocess.com).

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo TargetProcess no Painel de Acesso, deverá ser automaticamente inscrito no TargetProcess para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)