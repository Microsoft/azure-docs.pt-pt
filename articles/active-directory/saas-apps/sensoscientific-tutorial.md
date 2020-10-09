---
title: 'Tutorial: Integração do Azure Ative Directory com o Sistema de Monitorização da Temperatura Sem Fios SensoScientific Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Sistema de Monitorização da Temperatura Sem Fios SensoScientific.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: 61807f90caad9e6be2a25e54dcaa837595afc729
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88543181"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Tutorial: Integração do Diretório Ativo Azure com o Sistema de Monitorização da Temperatura Sem Fios SensoScientific

Neste tutorial, aprende-se a integrar o Sistema de Monitorização da Temperatura Sem Fios SensoScientific com o Azure Ative Directory (Azure AD).
A integração do Sistema de Monitorização da Temperatura Sem Fios SensoScientific com a AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso ao Sistema de Monitorização da Temperatura Sem Fios SensoScientific.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Sistema de Monitorização da Temperatura Sem Fios SensoScientific (Registo Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD do Azure com o Sistema de Monitorização da Temperatura Sem Fios SensoScientific, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* SensoScientific Wireless Temperature Monitoring System single sign-on enabled subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Sistema de Monitorização da Temperatura Sem Fios SensoScientific suporta SSO iniciado no **IDP**

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>Adicionar sistema de monitorização de temperatura sem fios SensoScientific da galeria

Para configurar a integração do Sistema de Monitorização da Temperatura Sem Fios SensoScientific em AD Azure, é necessário adicionar o Sistema de Monitorização da Temperatura Sem Fios sensoScientífico da galeria à sua lista de aplicações geridas para o SaaS.

**Para adicionar o Sistema de Monitorização da Temperatura Sem Fios SensoScientific da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva SensoScientific Wireless Temperature Monitoring System**, selecione **SensoScientific Wireless Temperature Monitoring System** from result panel then click **Add** button to add the application.

    ![Sistema de Monitorização da Temperatura Sem Fios SensoScientific na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sinal de sinalização com o Sistema de Monitorização da Temperatura Sem Fios SensoScientific baseado num utilizador de teste chamado **Britta Simon**.
Para um único sinal de funcionamento, é necessário estabelecer uma relação de ligação entre um utilizador AD Azure e o utilizador relacionado no Sistema de Monitorização da Temperatura Sem Fios SensoScientific.

Para configurar e testar o único sinal de Adure com o Sistema de Monitorização da Temperatura Sem Fios SensoScientific, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o sistema de monitorização da temperatura sem fios SensoScientific Single Sign-On](#configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. Crie o utilizador do Sistema de **[Monitorização da Temperatura Sem Fios SensoScientific](#create-sensoscientific-wireless-temperature-monitoring-system-test-user)** - para ter uma contraparte de Britta Simon no Sistema de Monitorização da Temperatura Sem Fios SensoScientific que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o Sistema de Monitorização da Temperatura Sem Fios SensoScientific, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação do Sistema de **Monitorização da Temperatura Sem Fios SensoScientific,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure.

    ![SensoScientific Wireless Temperature Monitoring System Domain and URLs single sign-on](common/preintegrated.png)

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Do Sistema de Monitorização da Temperatura Sem Fios SensoScientific,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on"></a>Configurar o sistema de monitorização da temperatura sem fios SensoScientific Sign-On

1. Inscreva-se na aplicação sensoScientific Wireless Temperature Monitoring System como administrador.

1. No menu de navegação no topo, clique em **Configuração** e goto **Configure** sob **o Signo Único Para** abrir o único sinal nas definições e executar os seguintes passos:

    ![Configurar Sign-On Individuais](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png)

    a. **Selecione nome do emitente** como Azure AD.

    b. Na caixa de texto URL do **emitente,** cole o **identificador Azure AD** que copiou do portal Azure.

    c. Na caixa de texto **URL de serviço de Sign-On único,** cole o URL de **login** que copiou do portal Azure.

    d. Na caixa de texto **URL de serviço de Sign-Out única,** cole o URL **logout** que copiou do portal Azure.

    e. Navegue no certificado que descarregou do portal Azure e faça o upload aqui.

    f. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **do nome do utilizador** `brittasimon@yourcompanydomain.extension` . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, permite à Britta Simon utilizar o Azure single sign-on, permitindo o acesso ao Sistema de Monitorização da Temperatura Sem Fios SensoScientific.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **SensoScientific Wireless Temperature Monitoring System**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, **selecione SensoScientific Wireless Temperature Monitoring System**.

    ![A ligação do Sistema de Monitorização da Temperatura Sem Fios SensoScientific na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>Criar o utilizador do sistema de monitorização da temperatura sem fios SensoScientific

Para permitir que os utilizadores de Azure AD inscrevam-se no Sistema de Monitorização da Temperatura Sem Fios SensoScientific, devem ser ateados no Sistema de Monitorização da Temperatura Sem Fios SensoScientific. Trabalhe com a equipa de suporte do Sistema de [Monitorização da Temperatura Sem Fios SensoScientific](https://www.sensoscientific.com/contact-us/)   para adicionar os utilizadores na plataforma do Sistema de Monitorização da Temperatura Sem Fios SensoScientific. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no sistema de monitorização da temperatura sem fios SensoScientific no Painel de Acesso, deverá ser automaticamente inscrito no Sistema de Monitorização da Temperatura Sem Fios sensoScientífico para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

