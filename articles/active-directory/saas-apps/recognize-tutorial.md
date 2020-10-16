---
title: 'Tutorial: Integração do Azure Ative Directory com o Recognise / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Recognise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 86c2c7383f3850b0c51831f94e673c3941894a08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88548897"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Tutorial: Integração do Diretório Ativo Azure com Reconhecimento

Neste tutorial, aprende-se a integrar o Recognise com o Azure Ative Directory (Azure AD).
Integrar a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Reconhecer.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos para reconhecer (Único S-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o Recognise, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Reconhecer uma única subscrição ativada por sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Reconhecer suportes **SP** iniciado SSO

## <a name="adding-recognize-from-the-gallery"></a>Adicionar Reconhecimento da galeria

Para configurar a integração do Recognise em AD Azure, é necessário adicionar Reconhecimento da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Reconhecimento da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva Reconheça**, selecione **Reconheça** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![Reconhecer na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa a Azure AD um único sinal de sação com Reconhecimento baseado num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Reconhecimento.

Para configurar e testar o Azure AD com o Recognise, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure Reconheça o Sign-On único](#configure-recognize-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Recognise test user](#create-recognize-test-user)** - ter uma contraparte de Britta Simon in Recognise que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD um único sinal de inscrição com o Recognise, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Recognise,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** se tiver **um ficheiro de metadados do Fornecedor de Serviços,** execute os seguintes passos:

    >[!NOTE]
    >Obtém-se o **ficheiro de metadados do Fornecedor** de Serviços na secção **Configure Recognise Single Sign-On** do tutorial.

    a. Clique **em Carregar o ficheiro de metadados**.

    ![Carregar ficheiro de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o ficheiro de metadados e clique em **Upload**.

    ![escolher arquivo de metadados](common/browse-upload-metadata.png)

    c. Após o carregamento do ficheiro de metadados com sucesso, o valor **do Identificador** é preenchido automaticamente na secção de Configuração BÁSICA SAML.

    ![Reconhecer informações de súmis e URLs únicas](common/sp-identifier.png)

     Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão:  `https://recognizeapp.com/<your-domain>/saml/sso`

    > [!Note]
    > Se o valor do **Identificador** não for preenchido automaticamente, obterá o valor do Identificador abrindo o URL de Metadados do Fornecedor de Serviços a partir da secção Definições SSO que é explicada mais tarde na secção **Configure Reconhecer Único Sinal-On** do tutorial. O valor url de inscrição não é real. Atualize o valor com o URL de inscrição real. Contacte [a equipa de suporte do Cliente](mailto:support@recognizeapp.com) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **'Reconhecer' Configurar,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-recognize-single-sign-on"></a>Configurar reconhecer Sign-On única

1. Numa janela diferente do navegador web, inscreva-se no seu inquilino reconhecido como administrador.

2. No canto superior direito, clique no **Menu**. Vá ao **Administrador da Empresa.**
   
    ![Configurar Sign-On única no lado da app](./media/recognize-tutorial/tutorial_recognize_000.png)

3. No painel de navegação à esquerda, clique em **Definições**.
   
    ![Configurar Sign-On única no lado da app](./media/recognize-tutorial/tutorial_recognize_001.png)

4. Execute os seguintes passos na secção **Definições SSO.**
   
    ![Configurar Sign-On única no lado da app](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Como **Enable SSO**, selecione **ON**.

    b. Na caixa de texto **IDP Entity ID,** cole o valor do **Identificador AD Azure** que copiou do portal Azure.
    
    c. Na url textbox **Sso target,** cole o valor do URL de **login** que copiou do portal Azure.
    
    d. Na url textbox **do slo target,** cole o valor do **URL logout** que copiou do portal Azure. 
    
    e. Abra o seu ficheiro **Certificado (Base64)** descarregado no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **certificate.**
    
    f. Clique no botão **Guardar definições.** 

5. Ao lado da secção **definições SSO,** copie o URL no **url do fornecedor de serviços metadados**.
   
    ![Configurar Sign-On única no lado da app](./media/recognize-tutorial/tutorial_recognize_003.png)

6. Abra a **ligação URL dos metadados** num browser em branco para descarregar o documento de metadados. Em seguida, copie o valor do EntityDescriptor (entidadeID) a partir do ficheiro e cole-o na caixa de texto **Identifier** na **Configuração Básica DO SAML** no portal Azure.
    
    ![Configurar Sign-On única no lado da app](./media/recognize-tutorial/tutorial_recognize_004.png)

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao Reconhecimento.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Reconhecer**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, **selecione Recognise**.

    ![O link Reconhecer na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-recognize-test-user"></a>Criar Utilizador de teste reconhecer

Para permitir que os utilizadores de Azure AD acedam ao Reconhecimento, devem ser a provisionados no Recognise. No caso de Reconhecer, o provisionamento é uma tarefa manual.

Esta aplicação não suporta o fornecimento scim, mas tem uma sincronização alternativa de utilizador que fornece os utilizadores. 

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa Recognise como administrador.

2. No canto superior direito, clique no **Menu**. Vá ao **Administrador da Empresa.**

3. No painel de navegação à esquerda, clique em **Definições**.

4. Execute os seguintes passos na secção **'Sincronização do Utilizador'.**
   
    ![Novo Utilizador](./media/recognize-tutorial/tutorial_recognize_005.png "Novo Utilizador")
   
    a. Como **Sync Enabled**, selecione **ON**.
   
    b. Como **Escolher o fornecedor de sincronização**, selecione Microsoft / Office **365**.
   
    c. Clique **em Executar 'Sincronização do utilizador'.**

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Reconhecer no Painel de Acesso, deverá ser automaticamente inscrito no Reconhecimento para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

