---
title: 'Tutorial: Integração do Azure Ative Directory com M-Files Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o M-Files.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: 8cf4df682a5c141e39a3860547a5b6130e2a3002
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856940"
---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Tutorial: Integração do Diretório Ativo Azure com M-Files

Neste tutorial, aprende-se a integrar os Ficheiros M com o Azure Ative Directory (Azure AD).
A integração de M-Files com Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso a Ficheiros M.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos nos Ficheiros M (SÚM's-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com ficheiros M, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* M-Files assinatura única ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* M-Files suporta **SSO** iniciado SP

## <a name="adding-m-files-from-the-gallery"></a>Adicionar M-Files da galeria

Para configurar a integração de M-Files em Azure AD, é necessário adicionar Ficheiros M da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar M-Files da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, tipo **M-Files**, selecione **M-Files** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![Ficheiros M na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com Ficheiros M baseado num utilizador de teste chamado **Britta Simon**.
Para um único s-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Ficheiros M.

Para configurar e testar o Azure AD com um único sinal de acesso com ficheiros M, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure m-files único sign-on](#configure-m-files-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create M-Files test user](#create-m-files-test-user)** - para ter uma contraparte de Britta Simon em Ficheiros M que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com um único sign-on com ficheiros M, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **M-Files,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![M-Files Domain e URLs informações únicas de súmis](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<tenantname>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<tenantname>.cloudvault.m-files.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do cliente M-Files](mailto:support@m-files.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **'Configurar Ficheiros M',** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-m-files-single-sign-on"></a>Configurar M-Files Sign-On única

1. Para obter sSO configurado para a sua aplicação, contacte [a equipa de suporte M-Files](mailto:support@m-files.com) e forneça-lhes os Metadados descarregados.
   
    >[!NOTE]
    >Siga os próximos passos se quiser configurar sSO para a sua aplicação de ambiente de trabalho M-File. Não são necessários passos adicionais se apenas pretender configurar sSO para a versão web M-Files.  

1. Siga os próximos passos para configurar a aplicação de ambiente de trabalho M-File para ativar SSO com Azure AD. Para descarregar M-Files, aceda à página [de descarregamento de M-Files.](https://www.m-files.com/en/download-latest-version)

1. Abra a janela **definições de ambiente de trabalho M-Files.** Em seguida, clique em **Adicionar**.
   
    ![A screenshot mostra as definições de ambiente de trabalho dos ficheiros M onde pode selecionar Adicionar.](./media/m-files-tutorial/tutorial_m_files_10.png)

1. Na janela Propriedades de **Conexão de Abóbada** de Documento, execute os seguintes passos:
   
    ![A screenshot mostra propriedades de conexão de abóbada de documento onde pode introduzir os valores descritos.](./media/m-files-tutorial/tutorial_m_files_11.png)  

    Sob o tipo de secção servidor, os valores são os seguintes:  

    a. Para **nome,** tipo `<tenant-name>.cloudvault.m-files.com` . 
 
    b. Para **o número de**porta , tipo **4466**. 

    c. Para **protocolo**, selecione **HTTPS**. 

    d. No campo **autenticação,** selecione **Utilizador Do Windows Específico**. Em seguida, é solicitado com uma página de assinatura. Insira as suas credenciais AD AZure. 

    e. Para o **Cofre no Servidor,** selecione o cofre correspondente no servidor.
 
    f. Clique em **OK**.

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

Nesta secção, permite que Britta Simon utilize o Azure como único sinal, permitindo o acesso aos Ficheiros M.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **M-Files**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **M-Files**.

    ![O link M-Files na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-m-files-test-user"></a>Criar utilizador de teste de M-Files

O objetivo desta secção é criar um utilizador chamado Britta Simon em M-Files. Trabalhe com a  [equipa de suporte M-Files](mailto:support@m-files.com) para adicionar os utilizadores nos Ficheiros M.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo M-Files no Painel de Acesso, deverá ser automaticamente inscrito nos Ficheiros M para os quais configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

