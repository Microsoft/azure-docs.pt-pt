---
title: 'Tutorial: Integração do Azure Ative Directory com AOVEit Transfer - Integração AD AZure / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o MOVEit Transfer - Integração Azure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: c79596747be655e372958e06021c50f23ff0fe03
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92508303"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Tutorial: Integração do Azure Ative Directory com AOVEit Transfer - Integração AZURE AD

Neste tutorial, aprende-se a integrar a MOVEit Transfer - Integração AD AZure com o Azure Ative Directory (Azure AD).
Integrar a MOVEit Transfer - A integração AD AD azure fornece-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso à MOVEit Transfer - Integração AZURE AD.
* Pode permitir que os seus utilizadores se inscrevam automaticamente na MOVEit Transfer - Integração AD Azure (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a MOVEit Transfer - Integração AD Azure, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* MOVEit Transfer - Azure AD integração única sign-on ativada subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* MOVEit Transfer - Integração AD AZure suporta **SSO** iniciado SP

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Adicionar transferência MOVEit - Integração AD AZure da galeria

Para configurar a integração da MOVEit Transfer - Integração AD Azure em Azure AD, você precisa adicionar a integração MOVEit AD da galeria à sua lista de aplicações geridas saaS.

**Para adicionar MOVEit Transfer - Integração AD AZure da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, tipo **MOVEit Transfer - Integração AD Azure**, selecione **MOVEit Transfer - Integração AD AZure** a partir do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![Transferência MOVEit - Integração AD AD Azure na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com a integração MOVEit AD com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na MOVEit Transfer - Azure AD.

Para configurar e testar o Azure AD com a MOVEit Transfer - Integração AD Azure, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure A Transferência de MOVEit - Integração AD AD Única Sign-On](#configure-moveit-transfer---azure-ad-integration-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Criar MOVEit Transfer - utilizador de teste de integração AD Azure](#create-moveit-transfer---azure-ad-integration-test-user)** - para ter uma contrapartida de Britta Simon na MOVEit Transfer - Integração AZURE AD que está ligada à representação AD Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com a MOVEit Transfer - Integração AD Azure, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **de integração MOVEit Transfer - Azure AD,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** se tiver **um ficheiro de metadados do Fornecedor de Serviços,** execute os seguintes passos:

    a. Clique **em Carregar o ficheiro de metadados**.

    ![Carregar ficheiro de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o ficheiro de metadados e clique em **Upload**.

    ![escolher arquivo de metadados](common/browse-upload-metadata.png)

    c. Após o carregamento com sucesso do ficheiro de metadados, o valor DE URL **de identificação** e **resposta** é preenchido automaticamente na secção **de Configuração BÁSICA SAML:**

    ![MOVEit Transfer - Domínio de integração AD Azure e informações únicas de súbdido](common/sp-identifier-reply.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://contoso.com`

    > [!NOTE]
    > O valor **url de inscrição** não é real. Atualize o valor com o URL de Sign-On real. Contacte [a MOVEit Transfer - Azure AD integração Equipa de suporte ao cliente](https://community.ipswitch.com/s/support) para obter o valor. Pode descarregar o **ficheiro de metadados do Fornecedor** de Serviços a partir do URL de **Metadados do Fornecedor** de Serviços, que é explicado mais tarde na secção **Configure MOVEit Transfer - Azure AD integração única sign-on** do tutorial. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configuração MOVEit Transfer - Azure AD integration,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-moveit-transfer---azure-ad-integration-single-sign-on"></a>Configure MOVEit Transfer - Azure AD integração single Sign-On

1. Inscreva-se no seu inquilino da MOVEit Transfer como administrador.

2. No painel de navegação à esquerda, clique em **Definições**.

    ![Secção de definições no lado da aplicação](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

3. Clique no link **Single Signon,** que está em **Políticas de Segurança -> User Auth**.

    ![Políticas de segurança no lado da app](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

4. Clique no link URL dos metadados para descarregar o documento de metadados.

    ![URL de metadados do fornecedor de serviços](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
   * Verifique se **a entidadeID** corresponde **ao identificador** na secção **de Configuração Básica SAML** .
   * Verifique **afirmaçõesConsumerService** Location URL corresponde **a URL de resposta** na secção de **configuração básica saml.**
    
     ![Configurar Sign-On única no lado da app](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

5. Clique no botão **Adicionar Fornecedor de Identidade** para adicionar um novo Fornecedor de Identidade Federado.

    ![Adicionar Fornecedor de Identidade](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

6. Clique **em Procurar...** para selecionar o ficheiro de metadados que descarregou a partir do portal Azure e, em seguida, clique em **Adicionar Fornecedor de Identidade** para carregar o ficheiro descarregado.

    ![Fornecedor de identidade SAML](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

7. Selecione "**Sim**" como **Ativado** nas Definições do **Fornecedor de Identidade Federada de Edição...** e clique em **Guardar**.

    ![Definições federadas do fornecedor de identidade](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

8. Na página **'Configurações de Utilizador do Fornecedor de Identidade Federada' de Edição,** execute as seguintes ações:
    
    ![Editar Definições de fornecedor de identidade federadas](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Selecione **SAML NameID** como **nome de Login**.
    
    b. Selecione **Outro** **como nome completo** e na caixa de texto do nome **Atributo** coloque o valor: `http://schemas.microsoft.com/identity/claims/displayname` .
    
    c. Selecione **Outros** como **Email** e na caixa de texto **do nome Atributo** coloque o valor: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .
    
    d. Selecione **Sim** como **Conta de criação automática no signon**.
    
    e. Clique no botão **Guardar.**

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

Nesta secção, você permite que Britta Simon use a Azure single sign-on, concedendo acesso à integração MOVEit AD.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **MOVEit Transfer - Azure AD integração**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **MOVEit Transfer - Integração AD Azure**.

    ![A transferência MOVEit - Ligação de integração AD AZure na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Criar transferência MOVEit - Utilizador de teste de integração AD Azure

O objetivo desta secção é criar um utilizador chamado Britta Simon na MOVEit Transfer - Integração AD AZure. MOVEit Transfer - A integração AZure AD suporta o provisionamento just-in-time, que você ativou. Não há nenhum item de ação para si nesta secção. Um novo utilizador é criado durante uma tentativa de aceder à integração MOVEit Transfer - Azure AD se ainda não existir.

>[!NOTE]
>Se precisar de criar um utilizador manualmente, tem de contactar a equipa de suporte ao cliente de [integração MOVEit Transfer - Azure AD](https://community.ipswitch.com/s/support).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar na integração MOVEit Transfer - Azure AD no Painel de Acesso, deverá ser automaticamente inscrito na integração MOVEit Transfer - Azure AD para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)