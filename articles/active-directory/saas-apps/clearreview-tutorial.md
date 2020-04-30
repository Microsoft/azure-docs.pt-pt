---
title: 'Tutorial: Integração do Diretório Ativo Azure com Clear Review [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Clear Review.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8264159a-11a2-4a8c-8285-4efea0adac8c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f2a0560163f9806053f49944cbec0db2b1a9de8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67105463"
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>Tutorial: Integração de Diretório Ativo Azure com Clear Review

Neste tutorial, aprende-se a integrar a Clear Review com o Azure Ative Directory (Azure AD).
Integrar a Clear Review com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Clear Review.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Clear Review (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Clear Review, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura de sinal único clear Review ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Clear Review suporta **SP e IDP** iniciado SSO

## <a name="adding-clear-review-from-the-gallery"></a>Adicionar revisão clara da galeria

Para configurar a integração da Clear Review no Azure AD, precisa de adicionar clear review da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Clear Review da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva Clear Review**, selecione **Clear Review** do painel de resultados e, em seguida, clique em adicionar o botão **adicionar** a aplicação.

     ![Revisão clara na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o single sign-on azure com clear review com base num utilizador de teste chamado **Britta Simon**.
Para que o início de um único início funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Clear Review.

Para configurar e testar o único sinal de Azure AD com a Clear Review, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure clear review single sign-On](#configure-clear-review-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Clear Review test user](#create-clear-review-test-user)** - para ter uma contrapartida de Britta Simon em Clear Review que está ligada à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com a Clear Review, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Clear Review,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado **idp,** execute os seguintes passos:

    ![Clear Review Domain e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<customer name>.clearreview.com/sso/metadata/`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<customer name>.clearreview.com/sso/acs/`

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    ![Clear Review Domain e URLs informações únicas de inscrição](common/metadata-upload-additional-signon.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<customer name>.clearreview.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta real e URL de sinalização. Contacte a equipa de suporte do [Cliente Clear Review](https://clearreview.com/contact/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

6. A aplicação Clear Review espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem a seguir mostra a lista de atributos predefinidos, onde o identificador de **nomes** é mapeado com **nome de utilizador.userprincipal .** A aplicação Clear Review espera que o identificador de **nomes** seja mapeado com **user.mail,** por isso precisa editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

7. Nos **atributos** do utilizador & diálogo de reclamações, execute os seguintes passos:

    a. Clique no **ícone Editar** à direita do valor do **identificador**de nome .

    ![image](./media/clearreview-tutorial/attribute02.png)

    ![image](./media/clearreview-tutorial/attribute01.png)

    b. A partir da lista de **atributos Fonte,** selecione o valor do atributo **utilizador.mail** para essa linha.

    c. Clique em **Guardar**.

8. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

9. Na secção **'Definir Revisão Clara',** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-clear-review-single-sign-on"></a>Configure a revisão clara de um único sinal

1. Para configurar um único sinal no lado **clear review,** abra o portal **Clear Review** com credenciais de administração.

2. Selecione **Administrador** da navegação esquerda.

    ![Configurar o botão de salvamento de sinal único](./media/clearreview-tutorial/tutorial_clearreview_app_admin1.png)

3. Na secção **Integrações** na parte inferior da página clique no botão **Alterar** à direita das **definições de início de sessão individuais**.

    ![Configurar o botão de salvamento de sinal único](./media/clearreview-tutorial/tutorial_clearreview_app_admin2.png)

4. Execute os seguintes passos na página **de definições de sinal único**

    ![Configurar o botão de salvamento de sinal único](./media/clearreview-tutorial/tutorial_clearreview_app_admin3.png)

    a. Na caixa de texto **URL emitente,** colhe o valor do **Identificador AD Azure** que copiou do portal Azure.

    b. Na caixa de texto **SAML Endpoint,** colá o valor do URL de **Login** que copiou do portal Azure.  

    c. Na caixa de texto **SLO Endpoint,** colhe o valor do URL de **Logout** que copiou do portal Azure.  

    d. Abra o certificado descarregado no bloco de notas e colhe o conteúdo na caixa de texto **x.509.**   

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
  
    b. No **tipo** de campo de nome utilizador **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso à Clear Review.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Clear Review**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Clear Review**.

    ![O link Clear Review na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-clear-review-test-user"></a>Criar utilizador de teste clear review

Nesta secção, cria-se uma utilizadora chamada Britta Simon em Clear Review. Por favor, trabalhe com a equipa de [suporte Clear Review](https://clearreview.com/contact/) para adicionar os utilizadores na plataforma Clear Review.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Clear Review no Painel de Acesso, deverá ser automaticamente inscrito na Clear Review para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

