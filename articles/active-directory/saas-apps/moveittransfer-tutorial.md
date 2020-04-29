---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Transferência MOVEit - Integração azure AD [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o MOVEit Transfer - Integração de Anúncios Azure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 43383e82d983c998d159728997da4757cc364999
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73161317"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Tutorial: Integração do Diretório Ativo Azure com a Transferência MOVEit - Integração azure AD

Neste tutorial, aprende-se a integrar a MOVEit Transfer - Integração Azure AD com o Azure Ative Directory (Azure AD).
Integrar a Transferência MOVEit - Integração de Anúncios Azure com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso à integração de AD MOVEit - Azure.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na MOVEit Transfer - Integração AD Azure (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Transferência MOVEit - Integração de Anúncios Azure, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* MOVEit Transfer - Assinatura única de assinatura ativada de integração da AD Azure

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* MOVEit Transfer - Integração azure AD apoia **SP** iniciado SSO

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Adicionar Transferência MOVEit - Integração da AD Azure a partir da galeria

Para configurar a integração da MOVEit Transfer - Integração Azure AD no Azure AD, é necessário adicionar a integração da MOVEit Transfer - Azure AD da galeria à sua lista de aplicações geridas do SaaS.

**Para adicionar a MOVEit Transfer - Integração azure AD da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **mOVEit Transfer - Integração Azure AD,** **selecione MOVEit Transfer - Azure AD integração** do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![Transferência MOVEit - Integração da AD Azure na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com a MOVEit Transfer - Integração Azure AD com base num utilizador de teste chamado **Britta Simon**.
Para que o início de um único início funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Transferência MOVEit - Integração de Anúncios Azure.

Para configurar e testar o único signo da Azure AD com a MOVEit Transfer - Integração Azure AD, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure a Transferência MOVEit - Integração AD Azure Single Sign-On](#configure-moveit-transfer---azure-ad-integration-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Criar a **[Transferência MOVEit - utilizador](#create-moveit-transfer---azure-ad-integration-test-user)** de testes de integração azure AD - para ter uma contrapartida da Britta Simon na Transferência MOVEit - Integração ad Azure que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com a MOVEit Transfer - Integração Azure AD, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações de integração da **MOVEit Transfer - Azure AD,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** se tiver ficheiro de **metadados do Prestador**de Serviços, execute os seguintes passos:

    a. Clique no **ficheiro de metadados de upload**.

    ![Upload ficheiro de metadados](common/upload-metadata.png)

    b. Clique no logotipo da **pasta** para selecionar o ficheiro de metadados e clicar em **Carregar**.

    ![escolher ficheiro de metadados](common/browse-upload-metadata.png)

    c. Após o ficheiro de metadados ser carregado com sucesso, o valor **URL** do **Identificador** e resposta fica povoado automaticamente na secção **basic SAML De configuração:**

    ![Transferência MOVEit - Domínio de integração da AD Azure e informações únicas de inscrição](common/sp-identifier-reply.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://contoso.com`

    > [!NOTE]
    > O valor **do URL de inscrição** não é real. Atualize o valor com o URL de Sign-On real. Contacte a MOVEit Transfer - Equipa de apoio ao Cliente de [integração da Azure AD](https://community.ipswitch.com/s/support) para obter o valor. Pode descarregar o **ficheiro metadados** do fornecedor de serviços a partir do URL de **Metadados** do Fornecedor de Serviços, que é explicado mais tarde na **Configuração MOVEit Transfer - Integração AD Azure Single Sign-On** do tutorial. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

4. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na secção de **integração MOVEit Transfer - Azure AD,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-moveit-transfer---azure-ad-integration-single-sign-on"></a>Configure MOVEit Transfer - Azure AD integração Single Sign-On

1. Inscreva-se no seu inquilino da MOVEit Transfer como administrador.

2. No painel de navegação à esquerda, clique em **Definições**.

    ![Secção de definições no lado da aplicação](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

3. Clique no link **Signon Single,** que está em políticas de **segurança -> Utilizador Auth**.

    ![Políticas de segurança no lado da app](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

4. Clique no link URL de Metadados para descarregar o documento de metadados.

    ![URL de metadados do fornecedor de serviços](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
   * Verifique os **fósforos de entidade** **Identificador** na secção **de configuração Básica SAML** .
   * Verifique o URL de localização **do Serviço de Crédito do Crédito** corresponde ao URL **RESPOSTA** na secção **de Configuração SAML Básica.**
    
     ![Configure o único sinal no lado da aplicação](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

5. Clique em adicionar botão fornecedor de **identidade** para adicionar um novo Fornecedor de Identidade Federado.

    ![Adicionar Fornecedor de Identidade](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

6. Clique em **Navegar...** para selecionar o ficheiro de metadados que descarregou do portal Azure e, em seguida, clique em **Adicionar Fornecedor** de Identidade para carregar o ficheiro descarregado.

    ![Provedor de Identidade SAML](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

7. Selecione "**Sim**" como **Ativado** nas Definições do **Fornecedor de Identidade Federada de Edição...** página e clique em **Guardar**.

    ![Definições de fornecedor de identidade federada](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

8. Na página de Definições de Utilizador do **Fornecedor de Identidade Federada,** execute as seguintes ações:
    
    ![Editar Definições de Fornecedor de Identidade Federada](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Selecione **SAML NameID** como nome de início de **sessão**.
    
    b. Selecione **Outro** como **nome completo** e na caixa de **texto denominada Atributo** coloque o valor: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Selecione **Outros** como **E-mail** e na `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`caixa de texto do nome **atributo** coloque o valor: .
    
    d. Selecione **Sim** como **auto-criar conta no signon**.
    
    e. Clique no botão **Guardar.**

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **tipo** de campo de nome utilizador **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso à MOVEit Transfer - Integração Azure AD.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **MOVEit Transfer - Integração AD Azure**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **MOVEit Transfer - Integração Azure AD**.

    ![O link de integração da MOVEit - Azure AD na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Criar transferência mOVEit - utilizador de teste de integração de Anúncios Azure

O objetivo desta secção é criar uma utilizadora chamada Britta Simon na MOVEit Transfer - Azure AD integração. A MOVEit Transfer - Integração da AD Azure suporta o provisionamento just-in-time, o que você permitiu. Não há nenhum item de ação para si nesta secção. Um novo utilizador é criado durante uma tentativa de aceder à integração da MOVEit Transfer - Azure AD se ainda não existir.

>[!NOTE]
>Se precisar de criar um utilizador manualmente, tem de contactar a equipa de suporte ao Cliente de integração da [MOVEit Transfer - Azure AD](https://community.ipswitch.com/s/support).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo de integração da MOVEit Transfer - Azure AD no Painel de Acesso, deverá ser automaticamente inscrito na Transferência MOVEit - Integração De AD Azure para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

