---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Sistema de Monitorização de Temperatura Sem Fios SensoCiência [ Sistema de Monitorização de TemperaturaS Sem Fios] Microsoft Docs'
description: Saiba como configurar um único sinal entre o Diretório Ativo Azure e o Sistema de Monitorização de Temperatura Sem Fios SensoScientific.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee9a924d-ccde-45b0-ab40-877f82f5dfa2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea2f5e33859852388357526052c39fa432471efb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67091274"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Tutorial: Integração do Diretório Ativo Azure com sistema de monitorização da temperatura sem fios SensoScientific

Neste tutorial, aprende-se a integrar o Sistema de Monitorização de TemperaturaS Sem Fios SensoScientific com o Diretório Ativo Azure (Azure AD).
Integrar o Sistema de Monitorização de Temperatura Sem Fios SensoScientific com a AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao Sistema de Monitorização de Temperatura Sem Fios SensoScientific.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Sistema de Monitorização de Temperatura Sem Fios SensoScientific (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Sistema de Monitorização de Temperatura Sem Fios SensoScientific, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* SensoScientific Wireless Temperature Monitoring System single sign-on

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Sistema de Monitorização de Temperatura Sem Fios SensoScientific suporta **IDP** iniciado SSO

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>Adicionar sistema de monitorização de temperatura sem fios SensoScientific a partir da galeria

Para configurar a integração do Sistema de Monitorização de Temperatura Sem Fios Senso Scientific em Azure AD, é necessário adicionar o Sistema de Monitorização de Temperatura Sem Fios SensoScientific da galeria à sua lista de aplicações geridas saaS.

**Para adicionar o Sistema de Monitorização de Temperatura Sem Fios Senso Científico a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite o Sistema de Monitorização de **Temperatura Sem Fios SensoScientific,** selecione Sistema de Monitorização de **Temperatura Sem Fios SensoScientific** a partir do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

    ![Sistema de Monitorização de Temperatura Sem Fios Senso Na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sinal de Azure AD com o Sistema de Monitorização de Temperatura Sem Fios SensoScientific baseado num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Sistema de Monitorização de Temperatura Sem Fios SensoScientific.

Para configurar e testar o único sinal de Acesso Azure AD com o Sistema de Monitorização de Temperatura Sem Fios SensoScientific, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. Configure o sistema de monitorização de **[temperatura sem fios sensoScientific](#configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Crie o utilizador do sistema de monitorização de **[temperatura sem fios SensoScientific](#create-sensoscientific-wireless-temperature-monitoring-system-test-user)** - para ter uma contrapartida de Britta Simon no Sistema de Monitorização de Temperatura Sem Fios SensoScientific que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sinal de Azure AD com o Sistema de Monitorização de Temperatura Sem Fios SensoScientific, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações do Sistema de Monitorização de **Temperatura Sem Fios SensoScientific,** selecione **um único sinal.**

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure.

    ![SensoScientific Wireless Temperature Monitoring System Domain and URLs single sign-on](common/preintegrated.png)

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **Configuração sensoScientific Temperature Temperature Temperature Monitoring System,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on"></a>Configure SensoScientific Wireless Temperature Monitoring System Single Sign-On

1. Inscreva-se na aplicação sensoScientific Wireless Temperature Monitoring System como administrador.

1. No menu de navegação no topo, clique em **Configurar** configurar e vá **configurar** sob **o signo único** para abrir as definições de sinal único e executar os seguintes passos:

    ![Configurar um único sinal](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png)

    a. Selecione **Nome emitente** como Anúncio Azure.

    b. Na caixa de texto **URL emitente,** cola o **identificador Azure AD** que copiou do portal Azure.

    c. Na caixa de texto URL do serviço de **assinatura única,** colhe o URL de **Login** que copiou do portal Azure.

    d. Na caixa de texto URL do **serviço de inscrição única,** colá o URL de **logout** que copiou do portal Azure.

    e. Navegue no certificado que descarregou do portal Azure e faça upload aqui.

    f. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **User name** tipo `brittasimon@yourcompanydomain.extension`de campo do nome do utilizador . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sinal de Acesso Azure, concedendo acesso ao Sistema de Monitorização de Temperatura Sem Fios Senso.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Sistema de Monitorização de Temperatura Sem Fios Senso.**

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Sistema de Monitorização de Temperatura Sem Fios Senso.**

    ![A ligação do Sistema de Monitorização de Temperatura Sem Fios SensoScientific na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>Criar o utilizador de teste do sistema de monitorização de temperatura sem fios SensoScientific

Para permitir que os utilizadores de Anúncios Azure insinuem no Sistema de Monitorização de Temperatura Sem Fios SensoScientific, devem ser aprovisionados no Sistema de Monitorização de Temperatura Sem Fios SensoScientific. Trabalhe com a equipa de suporte do Sistema de Monitorização de Temperatura [Sem Fios SensoScientific](https://www.sensoscientific.com/contact-us/)para adicionar os utilizadores na plataforma SensoScientific Wireless Temperature Monitoring System. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no sistema de monitorização de temperatura sem fios SensoScientific no Painel de Acesso, deve ser automaticamente inscrito no Sistema de Monitorização de Temperatura Sem Fios SensoScientific para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

