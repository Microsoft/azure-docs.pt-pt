---
title: 'Tutorial: Integração do Diretório Ativo Azure com a MobileIron [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o MobileIron.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 3e4bbd5b-290e-4951-971b-ec0c1c11aaa2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea4e997c6f2d0826c8914c671d625cc1c49bb018
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160456"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Tutorial: Integração de Diretório Ativo Azure com a MobileIron

Neste tutorial, aprende-se a integrar o MobileIron com o Azure Ative Directory (Azure AD).
Integrar o MobileIron com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a MobileIron.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no MobileIron (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o MobileIron, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de inscrição única mobileIron ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* MobileIron suporta **SP e IDP** iniciadoS SSO

## <a name="adding-mobileiron-from-the-gallery"></a>Adicionando MobileIron da galeria

Para configurar a integração do MobileIron em Azure AD, precisa de adicionar o MobileIron da galeria à sua lista de aplicações saaS geridas.

**Para adicionar MobileIron da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **MobileIron**, selecione **MobileIron** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![MobileIron na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o single sign-on azure com mobileIron com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no MobileIron.

Para configurar e testar o único sinal de Azure AD com o MobileIron, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure mobileIron single sign-On](#configure-mobileiron-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador](#create-mobileiron-test-user)** de teste MobileIron - para ter uma contrapartida da Britta Simon no MobileIron que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure com o MobileIron, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **MobileIron,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos se pretender configurar a aplicação no modo iniciado do **IDP:**

    ![MobileIron Domain e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://www.mobileiron.com/<key>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<host>.mobileiron.com/saml/SSO/alias/<key>`

    c. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    ![MobileIron Domain e URLs informações únicas de inscrição](common/metadata-upload-additional-signon.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<host>.mobileiron.com/user/login.html`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta e URL de sinal. Você receberá os valores da chave e anfitrião do portal administrativo do MobileIron que é explicado mais tarde no tutorial.

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

### <a name="configure-mobileiron-single-sign-on"></a>Configure MobileIron Single Sign-On

1. Numa janela de navegador web diferente, inicie sessão no site da sua empresa MobileIron como administrador.

2. Vá à **Admin** > **Identity** e selecione a opção **AAD** no campo **de configuração idp** da nuvem.

    ![Configure o botão de administrador de entrada única](./media/mobileiron-tutorial/tutorial_mobileiron_admin.png)

3. Copie os valores de **Chave** e **Anfitrião** e cole-os para completar os URLs na secção **de Configuração SAML Básica** no portal Azure.

    ![Configure o botão de administrador de entrada única](./media/mobileiron-tutorial/key.png)

4. No ficheiro de **metadados de exportação de AAD e importar para MobileIron Cloud Field** clique **em Escolher Ficheiro** para fazer o upload dos metadados descarregados do portal Azure. Clique **em Feito** uma vez carregado.

    ![Configure o botão de metadados de administração de sinal único](./media/mobileiron-tutorial/tutorial_mobileiron_adminmetadata.png)

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

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao MobileIron.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **MobileIron**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, escreva e selecione **MobileIron**.

    ![O link MobileIron na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-mobileiron-test-user"></a>Criar o utilizador de teste MobileIron

Para permitir que os utilizadores de Anúncios Azure entrem no MobileIron, devem ser aprovisionados no MobileIron.  
No caso do MobileIron, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa MobileIron como administrador.

1. Vá aos **Utilizadores** e clique em **Adicionar** > **Um Utilizador Único**.

    ![Configurar o botão de utilizador de sinal único](./media/mobileiron-tutorial/tutorial_mobileiron_user.png)

1. Na página de diálogo **"Utilizador Único",** execute os seguintes passos:

    ![Configure o botão de adicionar do utilizador de início de sessão individual](./media/mobileiron-tutorial/tutorial_mobileiron_useradd.png)

    a. Na caixa de texto Endereço de endereço brittasimon@contoso.comde **e-mail,** introduza o e-mail do utilizador como .

    b. Na caixa de texto **First Name,** introduza o primeiro nome de utilizador como Britta.

    c. Na caixa de texto **De Apelido,** introduza o último nome de utilizador como Simon.

    d. Clique em **Concluído**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo MobileIron no Painel de Acesso, deve ser automaticamente inscrito no MobileIron para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

