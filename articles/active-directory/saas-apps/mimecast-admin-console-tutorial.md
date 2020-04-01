---
title: 'Tutorial: Integração de Diretório Ativo Azure com consola de administrador mimecast [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Mimecast Admin Console.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9edadd6462052f82f92c05c1678f845ece856cfb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160664"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Tutorial: Integração de Diretório Ativo Azure com consola de administrador mimecast

Neste tutorial, aprende-se a integrar a Consola de Admin mimecast com o Diretório Ativo Azure (Azure AD).
Integrar a Consola de Administrador mimecast com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso à Consola de Admin Mimecast.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Consola de Admin mimecast (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Anúncio Azure com a Consola de Admin mimecast, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura única de assinatura ativada pela Consola de Administrador mimecast

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Consola mimecast Admin suporta **SP** iniciado

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>Adicionar consola de administrador mimecast da galeria

Para configurar a integração da Consola de Admin Mimecast em Azure AD, você precisa adicionar a Consola de Admin Mimecast da galeria à sua lista de aplicações geridas saaS.

**Para adicionar a Consola de Admin Mimecast da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite a **Consola de Admin mimecast,** selecione **Mimecast Admin Console** do painel de resultados e, em seguida, clique em adicionar o botão **Adicionar** a aplicação.

     ![Consola de admina Mimecast na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure ad com a Consola de Admin mimecast com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Consola de Administração Mimecast.

Para configurar e testar o único sinal de Azure AD com a Consola de Admin mimecast, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Único Sinal da Consola de Administração Mimecast](#configure-mimecast-admin-console-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Crie o utilizador de teste da **[Consola de Admin Mimecast](#create-mimecast-admin-console-test-user)** - para ter uma contrapartida da Britta Simon na Consola de Admin Mimecast que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com a Consola de Admin mimecast, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações da **Mimecast Admin Console,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Mimecast Admin Console Domain e URLs informações únicas de inscrição](common/sp-signonurl.png)

    Na caixa de texto **smS sign-on** URL, escreva o URL:
    
    | |
    | -- |
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|

    > [!NOTE] 
    > O sinal no URL é específico da região.

4. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção De configurar a **Consola de Administrador Mimecast,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="configure-mimecast-admin-console-single-sign-on"></a>Configure Mimecast Admin Console Single Sign-On

1. Numa janela de navegador web diferente, inicie sessão na consola de administrador mimecast Admin como administrador.

2. Ir à **Aplicação de \> Serviços.**

    ![Serviços](./media/mimecast-admin-console-tutorial/ic794998.png "Serviços")

3. Clique em **Perfis de Autenticação**.

    ![Perfis de Autenticação](./media/mimecast-admin-console-tutorial/ic794999.png "Perfis de Autenticação")
    
4. Clique em **Novo Perfil de Autenticação**.

    ![Novos perfis de autenticação](./media/mimecast-admin-console-tutorial/ic795000.png "Novos perfis de autenticação")

5. Na secção Perfil de **Autenticação,** execute os seguintes passos:

    ![Perfil de autenticação](./media/mimecast-admin-console-tutorial/ic795015.png "Perfil de autenticação")
    
    a. Na caixa de texto **Descrição,** digite um nome para a sua configuração.
    
    b. Selecione **impor a autenticação SAML para consola de administrador mimecast**.
    
    c. Como **Fornecedor,** **selecione Azure Ative Directory**.
    
    d. Identificador de **anúncios De Ad,** que copiou do portal Azure para a caixa de texto **URL emitente.**
    
    e. Colhe **o URL de Login,** que copiou do portal Azure para a caixa de texto URL **login.**

    f. Colhe **o URL de Login,** que copiou do portal Azure para a caixa de texto url de **logout.**
    
    >[!NOTE]
    >O valor URL de Login e o valor URL de Logout são para a Consola de Admin Mimecast o mesmo.
    
    g. Abra o certificado base-64 descarregado do portal Azure no bloco*--* de notas, remova a*--* primeira linha (" ") e a última linha (" "), copie o conteúdo restante do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto do Fornecedor de **Identidade (Metadados).**
    
    h. **Selecione permitir um único sinal .**
    
    i. Clique em **Guardar**.

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

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso à Consola de Admin mimecast.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações,** em seguida, selecione **A Consola de Admina Mimecast**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, escreva e selecione **Mimecast Admin Console**.

    ![O link da Consola de Administrador Mimecast na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-mimecast-admin-console-test-user"></a>Criar o utilizador de teste de consola de admina Mimecast

Para permitir que os utilizadores de Anúncios Azure entrem na Consola de Admin mimecast, devem ser aprovisionados na Consola de Admin mimecast. No caso da Consola de Administrador Mimecast, o fornecimento é uma tarefa manual.

* Tem de registar um domínio antes de poder criar utilizadores.

**Para configurar o fornecimento do utilizador, execute os seguintes passos:**

1. Inscreva-se na consola **de administrador da Mimecast Admin.**

2. Ir ao **Diretórios \> Interno.**
   
    ![Diretórios](./media/mimecast-admin-console-tutorial/ic795003.png "Diretórios")

3. Clique **em registar novo domínio**.
   
    ![Registar Novo Domínio](./media/mimecast-admin-console-tutorial/ic795004.png "Registar Novo Domínio")

4. Depois de criado o seu novo domínio, clique em **Novo Endereço**.
   
    ![Novo Endereço](./media/mimecast-admin-console-tutorial/ic795005.png "Novo Endereço")

5. No novo diálogo de endereço, execute os seguintes passos:
   
    ![Guardar](./media/mimecast-admin-console-tutorial/ic795006.png "Guardar")
   
    a. Digite o **Endereço de E-mail,** **Nome Global,** **Palavra-passe,** e **confirme** os atributos de senha de uma conta Azure AD válida que pretende fornecer nas caixas de texto relacionadas.

    b. Clique em **Guardar**.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador da Mimecast Admin Console ou APIs fornecidas pela Mimecast Admin Console para fornecer contas de utilizador da AD Azure. 

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo mimecast Admin Console no Painel de Acesso, deve ser automaticamente inscrito na Consola de Administrador Mimecast para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

