---
title: 'Tutorial: Integração do Diretório Ativo Azure com o ArcGIS Online [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o ArcGIS Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74cf0c1e055570df7702465df79dcdfbd8ea9e9f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74232097"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Tutorial: Integração de Diretório Ativo Azure com o ArcGIS Online

Neste tutorial, aprende-se a integrar o ArcGIS Online com o Azure Ative Directory (Azure AD).
Integrar o ArcGIS Online com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao ArcGIS Online.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no ArcGIS Online (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o ArcGIS Online, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de inscrição única arcGIS Online

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* ArcGIS Online suporta **SP** iniciado SSO

## <a name="adding-arcgis-online-from-the-gallery"></a>Adicionar ArcGIS Online da galeria

Para configurar a integração do ArcGIS Online em Azure AD, precisa adicionar o ArcGIS Online da galeria à sua lista de aplicações saaS geridas.

**Para adicionar o ArcGIS Online a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **ArcGIS Online,** selecione **ArcGIS Online** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![ArcGIS Online na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on do Azure AD com o ArcGIS Online com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no ArcGIS Online.

Para configurar e testar o único sinal de Azure AD com o ArcGIS Online, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Single Sign-On do ArcGIS](#configure-arcgis-online-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador de teste ArcGIS Online](#create-arcgis-online-test-user)** - para ter uma contrapartida de Britta Simon no ArcGIS Online que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com o ArcGIS Online, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **ArcGIS Online,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![ArcGIS Domínio Online e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<companyname>.maps.arcgis.com`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`<companyname>.maps.arcgis.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de suporte ao [Cliente Online arcGIS](https://support.esri.com/en/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Para automatizar a configuração dentro do **ArcGIS Online,** precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![image](./media/arcgis-tutorial/install_extension.png)

7. Depois de adicionar extensão ao navegador, clique em **configurar o ArcGIS Online** irá direcioná-lo para a aplicação ArcGIS Online. A partir daí, forneça as credenciais de administração para assinar no ArcGIS Online. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos na secção **Configure ArcGIS Online Single Sign-On**.

### <a name="configure-arcgis-online-single-sign-on"></a>Configure ArcGIS On-Line Single Sign-On

1. Se pretender configurar manualmente o ArcGIS Online, abra uma nova janela do navegador web e inicie sessão no site da empresa ArcGIS como administrador e execute os seguintes passos:

2. Clique em **definições de EDIÇÃO**.

    ![Editar Definições](./media/arcgis-tutorial/ic784742.png "Editar Definições")

3. Clique em **Segurança**.

    ![Segurança](./media/arcgis-tutorial/ic784743.png "Segurança")

4. Em **Logins empresariais,** clique em **DEFINIR FORNECEDOR DE IDENTIDADE**.

    ![Logins empresariais](./media/arcgis-tutorial/ic784744.png "Logins empresariais")

5. Na página de configuração do Fornecedor de **Identidade definida,** execute os seguintes passos:

    ![Definir Fornecedor de Identidade](./media/arcgis-tutorial/ic784745.png "Definir Fornecedor de Identidade")

    a. Na caixa de texto **Name,** digite o nome da sua organização.

    b. Para **metadados para o Fornecedor**de Identidade Da Empresa será fornecido usando , selecione **Um Ficheiro**.

    c. Para fazer o upload do ficheiro de metadados descarregado, clique em **Escolher ficheiro**.

    d. Clique em **definir fornecedor de identidade**.

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

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao ArcGIS Online.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **ArcGIS Online**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, digite e selecione **ArcGIS Online**.

    ![O link Online ArcGIS na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-arcgis-online-test-user"></a>Criar o utilizador de teste online ArcGIS

Para permitir que os utilizadores de Anúncios Azure entrem no ArcGIS Online, devem ser aprovisionados no ArcGIS Online.  
No caso do ArcGIS Online, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Faça login no seu inquilino **ArcGIS.**

2. Clique em **MEMBROS DE CONVITE**.
   
    ![Convidar deputados](./media/arcgis-tutorial/ic784747.png "Convidar deputados")

3. Selecione **Adicionar membros automaticamente sem enviar um e-mail**, e, em seguida, clicar **em NEXT**.
   
    ![Adicionar Membros automaticamente](./media/arcgis-tutorial/ic784748.png "Adicionar Membros automaticamente")

4. Na página de diálogo **dos Deputados,** execute os seguintes passos:
   
     ![Adicionar e rever](./media/arcgis-tutorial/ic784749.png "Adicionar e rever")
    
     a. Insira o **e-mail**, **primeiro nome**e **o apelido** de uma conta Azure AD válida que pretende fornecer.
  
     b. Clique em **ADICIONAR E REVER**.
5. Reveja os dados que inseriu e, em seguida, clique em **ADICIONAR MEMBROS**.
   
    ![Adicionar membro](./media/arcgis-tutorial/ic784750.png "Adicionar membro")
        
    > [!NOTE]
    > O titular da conta Azure Ative Directory receberá um e-mail e seguirá um link para confirmar a sua conta antes de se tornar ativo.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Online ArcGIS no Painel de Acesso, deverá ser automaticamente inscrito no ArcGIS Online para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

