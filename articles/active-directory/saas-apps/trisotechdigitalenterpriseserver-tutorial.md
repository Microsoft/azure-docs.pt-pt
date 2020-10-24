---
title: 'Tutorial: Integração do Azure Ative Directory com o Trisotech Digital Enterprise Server Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Trisotech Digital Enterprise Server.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: dfad185718dcc4a1c06ba3a4ed22961615ccd37e
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92486961"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>Tutorial: Integração do Azure Ative Directory com o Trisotech Digital Enterprise Server

Neste tutorial, aprende-se a integrar o Trisotech Digital Enterprise Server com o Azure Ative Directory (Azure AD).
A integração do Trisotech Digital Enterprise Server com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Trisotech Digital Enterprise Server.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Trisotech Digital Enterprise Server (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração Azure AD com o Trisotech Digital Enterprise Server, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Trisotech Digital Enterprise Server única subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Trisotech Digital Enterprise Server suporta **SSO** iniciado SP

* O Servidor De Empresa Digital Trisotech suporta o fornecimento **de utilizadores just in time**

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>Adicionar o Servidor De Empresa Digital Trisotech da galeria

Para configurar a integração do Trisotech Digital Enterprise Server no Azure AD, é necessário adicionar o Trisotech Digital Enterprise Server da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar o Trisotech Digital Enterprise Server da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Trisotech Digital Enterprise Server**, selecione **Trisotech Digital Enterprise Server** a partir do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![Trisotech Digital Enterprise Server na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o Trisotech Digital Enterprise Server com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Trisotech Digital Enterprise Server.

Para configurar e testar o único sinal de Ad AD com o Trisotech Digital Enterprise Server, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On único do Servidor de Empresas Digitais Trisotech](#configure-trisotech-digital-enterprise-server-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. Crie o utilizador de **[teste Trisotech Digital Enterprise Server](#create-trisotech-digital-enterprise-server-test-user)** - para ter uma contraparte de Britta Simon no Trisotech Digital Enterprise Server que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o único sinal de Ad da Azure com o Trisotech Digital Enterprise Server, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **trisotech Digital Enterprise Server,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Trisotech Digital Enterprise Server Domain e URLs informações únicas de súmis](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<companyname>.trisotech.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<companyname>.trisotech.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte ao cliente da Trisotech Digital Enterprise Server](mailto:support@trisotech.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

### <a name="configure-trisotech-digital-enterprise-server-single-sign-on"></a>Configurar o servidor de empresa digital Trisotech Single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Trisotech Digital Enterprise Server Como administrador.

2. Clique no **ícone menu** e, em seguida, selecione **Administração**.

    ![A screenshot mostra o ícone da Administração no Microsoft Digital Enterprise Server.](./media/trisotechdigitalenterpriseserver-tutorial/user1.png)

3. Selecione **User Provider**.

    ![A screenshot mostra o User Provider selecionado no menu.](./media/trisotechdigitalenterpriseserver-tutorial/user2.png)

4. Na secção **Configurações do Fornecedor de Utilizador,** execute os seguintes passos:

    ![A screenshot mostra as configurações do fornecedor do utilizador onde pode introduzir os valores descritos.](./media/trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. Selecione **O Idioma de Marcação de Afirmação Garantido 2 (SAML 2)** a partir do dropdown no **Método de Autenticação**.

    b. Na caixa de texto **URL dos metadados,** cole o valor do **url da Federação de Metadados** da App, que copiou para formar o portal Azure.

    c. Na caixa de texto ID do **aplicativo,** introduza o URL utilizando o seguinte padrão: `https://<companyname>.trisotech.com` .

    d. Clique em **Guardar**

    e. Introduza o nome de domínio na caixa de texto **"Domínios Permitidos" (significa vazio significa todos)** que atribui automaticamente licenças para utilizadores que correspondam aos Domínios Permitidos

    f. Clique em **Guardar**

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

Nesta secção, permite à Britta Simon utilizar o Azure single sign-on, permitindo o acesso ao Trisotech Digital Enterprise Server.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Trisotech Digital Enterprise Server**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Trisotech Digital Enterprise Server**.

    ![O link Trisotech Digital Enterprise Server na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-trisotech-digital-enterprise-server-test-user"></a>Criar utilizador de teste de servidor de empresa digital Trisotech

Nesta secção, um utilizador chamado Britta Simon é criado no Trisotech Digital Enterprise Server. O Trisotech Digital Enterprise Server suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Trisotech Digital Enterprise Server, um novo é criado após a autenticação.

>[!Note]
>Se necessitar de criar um utilizador manualmente, contacte a [equipa de suporte do Servidor Digital da Empresa Trisotech](mailto:support@trisotech.com).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Trisotech Digital Enterprise Server no Painel de Acesso, deverá ser automaticamente inscrito no Servidor De Empresa Digital Trisotech para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

