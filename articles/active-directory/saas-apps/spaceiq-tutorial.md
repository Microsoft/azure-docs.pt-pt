---
title: 'Tutorial: Integração do Diretório Ativo Azure com o SpaceIQ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SpaceIQ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5b55ae29-491f-401f-9299-d3a6b64a1b99
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: b3358ad473ede9e8d78a835e8c68e690e5340638
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67090097"
---
# <a name="tutorial-azure-active-directory-integration-with-spaceiq"></a>Tutorial: Integração de Diretório Ativo Azure com spaceIQ

Neste tutorial, aprende-se a integrar o SpaceIQ com o Azure Ative Directory (Azure AD).
Integrar o SpaceIQ com o Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao SpaceIQ.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no SpaceIQ (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o SpaceIQ, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura de inscrição única SpaceIQ

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* SpaceIQ suporta **IDP** iniciado SSO

## <a name="adding-spaceiq-from-the-gallery"></a>Adicionando SpaceIQ da galeria

Para configurar a integração do SpaceIQ em Azure AD, precisa adicionar spaceIQ da galeria à sua lista de aplicações saaS geridas.

**Para adicionar spaceIQ da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SpaceIQ**, selecione **SpaceIQ** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

    ![SpaceIQ na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com spaceIQ com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no SpaceIQ.

Para configurar e testar o único sinal de Azure AD com o SpaceIQ, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o SpaceIQ Single Sign-On](#configure-spaceiq-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create SpaceIQ test user](#create-spaceiq-test-user)** - para ter uma contrapartida de Britta Simon no SpaceIQ que está ligada à representação azure AD do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com o SpaceIQ, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **SpaceIQ,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na configuração de um único sign-on com a página **SAML,** execute os seguintes passos:

    ![SpaceIQ Domain e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **identificador,** digite o URL:`https://api.spaceiq.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://api.spaceiq.com/saml/<instanceid>/callback`

    > [!NOTE]
    > Atualize estes valores com o URL de Resposta real e identificador que é explicado mais tarde no tutorial.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **'Instalar SpaceIQ',** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-spaceiq-single-sign-on"></a>Configure SpaceIQ Single Sign-On

1. Abra uma nova janela do navegador e, em seguida, inscreva-se no seu ambiente SpaceIQ como administrador.

1. Assim que estiver registado, clique no sinal do puzzle no topo direito e clique em **Integrações**

    ![Definições da conta](./media/spaceiq-tutorial/setting1.png) 

1. Em **todas as provisões & SSO,** clique no azulejo **Azure** para adicionar uma instância de Azure como IDP.

    ![Ícone SAML](./media/spaceiq-tutorial/setting2.png)

1. Na caixa de diálogo **SSO,** execute os seguintes passos:

    ![Definições de autenticação SAML](./media/spaceiq-tutorial/setting3.png)

    a. Na caixa **URL do Emitente SAML,** colá o valor do **identificador Azure AD** copiado da janela de configuração da aplicação Azure AD.

    b. Copie o valor do URL de **fim de ligação sAML CallBack (apenas** para leitura) e cole o valor na caixa **DEURL** de resposta na secção **de configuração Básica SAML** no portal Azure.

    c. Copie o valor do **Público SAML URI (apenas** para leitura) e cole o valor na caixa **de identificação** na secção **de Configuração SAML Básica** no portal Azure.

    d. Abra o ficheiro de certificado descarregado no bloco de notas, copie o conteúdo e, em seguida, cole-o na caixa de **certificadoX.509.**

    e. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No tipo de campo de **nome do utilizador**`brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao SpaceIQ.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **SpaceIQ**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **SpaceIQ**.

    ![O link SpaceIQ na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-spaceiq-test-user"></a>Criar o utilizador de teste SpaceIQ

Nesta secção, cria-se uma utilizadora chamada Britta Simon no SpaceIQ. Work [SpaceIQ equipa](mailto:eng@spaceiq.com) de suporte para adicionar os utilizadores na plataforma SpaceIQ. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SpaceIQ no Painel de Acesso, deve ser automaticamente inscrito no SpaceIQ para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

