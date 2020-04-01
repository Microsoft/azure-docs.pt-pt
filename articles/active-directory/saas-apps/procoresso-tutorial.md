---
title: 'Tutorial: Integração do Diretório Ativo Azure com procore SSO [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Procore SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca6863a6b02e867afd732ce1662136051b8afec8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093671"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Tutorial: Integração de Diretório Ativo Azure com Procore SSO

Neste tutorial, aprende-se a integrar o Procore SSO com o Azure Ative Directory (Azure AD).
Integrar o Procore SSO com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Procore SSO.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Procore SSO (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Procore SSO, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura de sinal único Procore SSO ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Procore SSO suporta **IDP** iniciado SSO

## <a name="adding-procore-sso-from-the-gallery"></a>Adicionando Procore SSO da galeria

Para configurar a integração do Procore SSO no Azure AD, é necessário adicionar o Procore SSO da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Procore SSO da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Procore SSO,** selecione **Procore SSO** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

    ![Procore SSO na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com procore SSO com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Procore SSO.

Para configurar e testar o único sinal de Azure AD com o Procore SSO, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único Procore SSO](#configure-procore-sso-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Crie um utilizador de **[teste Procore SSO](#create-procore-sso-test-user)** - para ter uma contrapartida da Britta Simon no Procore SSO que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure com o Procore SSO, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Procore SSO,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure.

    ![Procore SSO Domain e URLs informações únicas de inscrição](common/preintegrated.png)

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na secção **SSO De Configuração Procore,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-procore-sso-single-sign-on"></a>Configure Procore SSO Single Sign-On

1. Para configurar um único sinal no lado **Procore SSO,** inscreva-se no site da sua empresa procore como administrador.

2. A partir da caixa de ferramentas, clique no **Administrador** para abrir a página de definições SSO.

    ![Configurar um único sinal](./media/procoresso-tutorial/procore_tool_admin.png)

3. Colar os valores nas caixas descritos abaixo...

    ![Configurar um único sinal](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. Na caixa de texto **URL single Sign On Emitr,** colá o valor do **Identificador Azure AD** que copiou do portal Azure.

    b. Na caixa **de URL do Sinal SAML no alvo,** colá-lo o valor do URL de **Login** que copiou do portal Azure.

    c. Agora abra o **Federation Metadata XML** descarregado acima do portal Azure e copie o certificado na etiqueta denominada **X509Certificate**. Colhe o valor copiado na caixa de certificado sinuosa **do Sinal Único x509.**

4. Clique em **Guardar Alterações**.

5. Depois destas definições, tem de enviar o nome de **domínio** (por **exemplo, contoso.com)** através do qual está a iniciar sessão no Procore para a [equipa procore Support](https://support.procore.com/) e ativará o SSO federado para esse domínio.

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

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao Procore SSO.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Procore SSO**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Procore SSO**.

    ![O link Procore SSO na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-procore-sso-test-user"></a>Criar o utilizador de teste Procore SSO

Siga os passos abaixo para criar um utilizador procore de teste no lado Procore SSO.

1. Inscreva-se no site da sua empresa procore como administrador.    

2. A partir da caixa de ferramentas, clique no **Diretório** para abrir a página de diretório da empresa.

    ![Configurar um único sinal](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Clique em **Adicionar uma** opção Pessoa para abrir o formulário e introduzir as seguintes opções -

    ![Configurar um único sinal](./media/procoresso-tutorial/Procore_user_add.png)

    a. Na caixa de texto **First Name,** digite o primeiro nome do utilizador como **Britta**.

    b. Na caixa de texto **De apelido,** digite o apelido do utilizador como **Simon**.

    c. Na caixa de texto do Endereço de BrittaSimon@contoso.com **E-mail,** digite o endereço de e-mail do utilizador como .

    d. Selecione **o modelo de permissão** como modelo de **permissão de aplicação mais tarde**.

    e. Clique em **Criar**.

4. Verifique e atualize os detalhes para o novo contacto.

    ![Configurar um único sinal](./media/procoresso-tutorial/Procore_user_check.png)

5. Clique em **Guardar e Enviar Convite** (se for necessário um convite por correio) ou **guardar** (Guardar diretamente) para completar o registo do utilizador.
    
    ![Configurar um único sinal](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Procore SSO no Painel de Acesso, deve ser automaticamente inscrito no Procore SSO para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

