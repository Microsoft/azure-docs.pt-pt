---
title: 'Tutorial: Integração do Azure Ative Directory com o ArcGIS Online Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o ArcGIS Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: 08644c8b3c7967a2ba604cbddb58df2b29c6fc3e
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92457780"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Tutorial: Integração do Azure Ative Directory com o ArcGIS Online

Neste tutorial, aprende-se a integrar o ArcGIS Online com o Azure Ative Directory (Azure AD).
A integração do ArcGIS Online com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao ArcGIS Online.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no ArcGIS Online (Sign-on Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o ArcGIS Online, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada pelo ArcGIS Online

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* ArcGIS Online suporta **SSO** iniciado sp

## <a name="adding-arcgis-online-from-the-gallery"></a>Adicionar ArcGIS Online da galeria

Para configurar a integração do ArcGIS Online no AD Azure, é necessário adicionar o ArcGIS Online da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar ArcGIS Online da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite ArcGIS Online,** selecione **ArcGIS Online** a partir do painel de resultados e clique em Adicionar botão **adicionar** a aplicação.

     ![ArcGIS Online na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o ArcGIS Online com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no ArcGIS Online.

Para configurar e testar o Azure AD com o ArcGIS Online, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único Online ArcGIS -](#configure-arcgis-online-single-sign-on)** para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create ArcGIS Online test user](#create-arcgis-online-test-user)** - para ter uma contraparte de Britta Simon no ArcGIS Online que está ligada à representação AD AD Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o ArcGIS Online, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Online ArcGIS,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![ArcGIS On-Line Domain e URLs informações únicas de súmis](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<companyname>.maps.arcgis.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `<companyname>.maps.arcgis.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte ao Cliente Online ArcGIS](https://support.esri.com/en/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Para automatizar a configuração dentro do **ArcGIS Online,** é necessário instalar a **extensão do navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![image](./media/arcgis-tutorial/install_extension.png)

7. Depois de adicionar extensão ao navegador, clique na **configuração ArcGIS Online** irá direcioná-lo para a aplicação ArcGIS Online. A partir daí, forneça as credenciais de administração para assinar no ArcGIS Online. A extensão do navegador configurará automaticamente a aplicação para si e automatizará passos na secção **Configure ArcGIS Online Single Sign-On**.

### <a name="configure-arcgis-online-single-sign-on"></a>Configurar o Sign-On único online do ArcGIS

1. Se pretender configurar o ArcGIS Online manualmente, abra uma nova janela do navegador web e inicie sessão no site da empresa ArcGIS como administrador e execute os seguintes passos:

2. CLIQUE **EM EDITAR DEFINIÇÕES**.

    ![Editar Definições](./media/arcgis-tutorial/ic784742.png "Editar Definições")

3. Clique **em Segurança**.

    ![Segurança](./media/arcgis-tutorial/ic784743.png "Segurança")

4. Em **Logins empresariais,** clique **em SET IDENTITY PROVIDER**.

    ![Logins empresariais](./media/arcgis-tutorial/ic784744.png "Logins empresariais")

5. Na página de configuração **do Fornecedor de Identidade Definido,** execute os seguintes passos:

    ![Definir Fornecedor de Identidade](./media/arcgis-tutorial/ic784745.png "Definir Fornecedor de Identidade")

    a. Na caixa de texto **Name,** escreva o nome da sua organização.

    b. Para **metadados para o Fornecedor de Identidade da Empresa será fornecido utilizando**, selecione Um **Ficheiro**.

    c. Para fazer o upload do seu ficheiro de metadados descarregado, clique em **Escolher o ficheiro**.

    d. CLIQUE **NO FORNECEDOR DE IDENTIDADE DEFINIDO.**

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao ArcGIS Online.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **ArcGIS Online**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, escreva e selecione **ArcGIS Online**.

    ![O link Online ArcGIS na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-arcgis-online-test-user"></a>Criar utilizador de teste ArcGIS Online

Para permitir que os utilizadores de Azure AD acedam ao ArcGIS Online, devem ser a provisionados no ArcGIS Online.  
No caso do ArcGIS Online, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Faça login no seu inquilino **ArcGIS.**

2. Clique **em CONVIDAR MEMBROS.**
   
    ![Convidar membros](./media/arcgis-tutorial/ic784747.png "Convidar membros")

3. **Selecione Adicionar os membros automaticamente sem enviar um e-mail**e, em seguida, clique em **SEGUINTE**.
   
    ![Adicionar membros automaticamente](./media/arcgis-tutorial/ic784748.png "Adicionar membros automaticamente")

4. Na página de diálogo dos **membros,** execute os seguintes passos:
   
     ![Adicionar e rever](./media/arcgis-tutorial/ic784749.png "Adicionar e rever")
    
     a. Introduza o **e-mail**, **Nome próprio**e **Último Nome** de uma conta AD válida que pretende obter.
  
     b. CLIQUE **EM ADICIONAR E REVER**.
5. Reveja os dados introduzidos e, em seguida, clique em **ADD MEMBERS**.
   
    ![Adicionar membro](./media/arcgis-tutorial/ic784750.png "Adicionar membro")
        
    > [!NOTE]
    > O titular da conta Azure Ative Directory receberá um e-mail e seguirá um link para confirmar a sua conta antes de ficar ativa.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo ArcGIS Online no Painel de Acesso, deverá ser automaticamente inscrito no ArcGIS Online para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)