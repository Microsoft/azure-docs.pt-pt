---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Kantega SSO para FishEye/Crucible [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Kantega SSO para FishEye/Crucible.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9fe951fd-1530-4d33-a1a4-390385b99ce9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f262f94c451d5dcffd933bdebb1374b8733b9fd8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67595177"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-fisheyecrucible"></a>Tutorial: Integração do Diretório Ativo Azure com kantega SSO para FishEye/Crucible

Neste tutorial, aprende-se a integrar o Kantega SSO para fisheye/crucible com o Azure Ative Directory (Azure AD).
Integrar o Kantega SSO para FishEye/Crucible com AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Kantega SSO para FishEye/Crucible.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Kantega SSO para FishEye/Crucible (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Kantega SSO para FishEye/Crucible, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Kantega SSO para assinatura única ativada pelo FishEye/Crucible

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Kantega SSO para FishEye/Crucible suporta **SP e IDP** iniciadoS SSO

## <a name="adding-kantega-sso-for-fisheyecrucible-from-the-gallery"></a>Adicionando Kantega SSO para FishEye/Crucible da galeria

Para configurar a integração do Kantega SSO para FishEye/Crucible em Azure AD, você precisa adicionar Kantega SSO para FishEye/Crucible da galeria para a sua lista de aplicações SaaS geridas.

**Para adicionar Kantega SSO para FishEye/Crucible da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Kantega SSO para FishEye/Crucible,** **selecione Kantega SSO para FishEye/Crucible** do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

    ![Kantega SSO para FishEye/Crucible na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sinal de Azure AD com o Kantega SSO para FishEye/Crucible com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Kantega SSO para fisheye/crucible.

Para configurar e testar o único sinal de Azure AD com o SSO Kantega para FishEye/Crucible, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure kantega SSO para FishEye/Crucible Single Sign-On](#configure-kantega-sso-for-fisheyecrucible-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o Kantega SSO para o utilizador de teste FishEye/Crucible](#create-kantega-sso-for-fisheyecrucible-test-user)** - para ter uma contrapartida de Britta Simon em Kantega SSO para FishEye/Crucible que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com kantega SSO para FishEye/Crucible, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página kantega SSO para integração de aplicações **FishEye/Crucible,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado **idp,** execute os seguintes passos:

    ![Kantega SSO para FishEye/Domínio Crucible e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    ![Kantega SSO para FishEye/Domínio Crucible e URLs informações únicas de inscrição](common/metadata-upload-additional-signon.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta e URL de sinal. Estes valores são recebidos durante a configuração do plugin FishEye/Crucible, que é explicado mais tarde no tutorial.

6. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

7. Na **configuração do Kantega SSO para a secção FishEye/Crucible,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-kantega-sso-for-fisheyecrucible-single-sign-on"></a>Configure Kantega SSO para FishEye/Crucible Single Sign-On

1. Numa janela de navegador web diferente, inscreva-se no seu servidor fishEye/Crucible no local como administrador.

1. Passe sobre a engrenagem e clique nos **Add-ons**.

    ![Configurar um único sinal](./media/kantegassoforfisheyecrucible-tutorial/addon1.png)

1. Na secção definições do sistema, clique em **encontrar novos addons**. 

    ![Configurar um único sinal](./media/kantegassoforfisheyecrucible-tutorial/add-on2.png)

1. Procure o **Kantega SSO por Crucible** e clique em **instalar** o novo plugin SAML.

    ![Configurar um único sinal](./media/kantegassoforfisheyecrucible-tutorial/addon2.png)

1. A instalação de plugin começa. 

    ![Configurar um único sinal](./media/kantegassoforfisheyecrucible-tutorial/addon33.png)

1. Uma vez concluída a instalação. Clique em **Fechar**.

    ![Configurar um único sinal](./media/kantegassoforfisheyecrucible-tutorial/addon34.png)

1.  Clique em **Gerir**.

    ![Configurar um único sinal](./media/kantegassoforfisheyecrucible-tutorial/addon35.png)

1. Clique em **Configurar** para configurar o novo plugin. 

    ![Configurar um único sinal](./media/kantegassoforfisheyecrucible-tutorial/addon3.png)

1. Na secção **SAML.** Selecione **Azure Ative Directory (Azure AD)** a partir do dropdown do fornecedor de **identidade Add.**

    ![Configurar um único sinal](./media/kantegassoforfisheyecrucible-tutorial/addon4.png)

1. Selecione o nível de subscrição como **Basic**.

    ![Configurar um único sinal](./media/kantegassoforfisheyecrucible-tutorial/addon5.png)

1. Na secção propriedades da **App,** execute os seguintes passos:

    ![Configurar um único sinal](./media/kantegassoforfisheyecrucible-tutorial/addon6.png)

    a. Copie o valor DO ID URI da **aplicação** e use-o como **Identifier, Answer URL e Url de Início de Sinal** na secção básica de **configuração SAML** no portal Azure.

    b. Clique em **Seguinte**.

1. Na secção de importação de **Metadados,** execute os seguintes passos:

    ![Configurar um único sinal](./media/kantegassoforfisheyecrucible-tutorial/addon7.png)

    a. Selecione **ficheiro Metadados no meu computador**e faça upload do ficheiro de metadados, que descarregou do portal Azure.

    b. Clique em **Seguinte**.

1. Na secção de **localização Nome e SSO,** execute os seguintes passos:

    ![Configurar um único sinal](./media/kantegassoforfisheyecrucible-tutorial/addon8.png)

    a. Adicione o Nome do Fornecedor de Identidade na **denominação** de código de identidade (por exemplo, Azure AD).

    b. Clique em **Seguinte**.

1. Verifique o certificado de assinatura e clique **em Seguinte**.   

    ![Configurar um único sinal](./media/kantegassoforfisheyecrucible-tutorial/addon9.png)

1. Na secção de contas de **utilizador fishEye,** execute os seguintes passos:

    ![Configurar um único sinal](./media/kantegassoforfisheyecrucible-tutorial/addon10.png)

    a. Selecione **Criar utilizadores no Diretório Interno do FishEye, se necessário,** e introduza o nome apropriado do grupo para os utilizadores (pode ser múltiplos não. de grupos separados por vírina).

    b. Clique em **Seguinte**.

1. Clique em **Concluir**.

    ![Configurar um único sinal](./media/kantegassoforfisheyecrucible-tutorial/addon11.png)

1. Nos **domínios conhecidos da secção Azure AD,** execute os seguintes passos:  

    ![Configurar um único sinal](./media/kantegassoforfisheyecrucible-tutorial/addon12.png)

    a. Selecione **domínios conhecidos** do painel esquerdo da página.

    b. Introduza o nome de domínio na caixa de texto dos **domínios conhecidos.**

    c. Clique em **Guardar**.

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

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao Kantega SSO para FishEye/Crucible.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Kantega SSO para FishEye/Crucible**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Kantega SSO para FishEye/Crucible**.

    ![O XSO Kantega para FishEye/Link Crucible na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-kantega-sso-for-fisheyecrucible-test-user"></a>Crie O SSO Kantega para o utilizador de teste FishEye/Crucible

Para permitir que os utilizadores de Anúncios Azure assinem o FishEye/Crucible, devem ser aprovisionados no FishEye/Crucible. Em Kantega SSO para FishEye/Crucible, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu servidor no local crucible como administrador.

1. Passe sobre a engrenagem e clique nos **Utilizadores**.

    ![Adicionar Empregado](./media/kantegassoforfisheyecrucible-tutorial/user1.png)

1. Na secção **de separadores dos Utilizadores,** clique em **Adicionar utilizador**.

    ![Adicionar Empregado](./media/kantegassoforfisheyecrucible-tutorial/user2.png)

1. Na página de diálogo **Adicionar Novo Utilizador,** execute os seguintes passos:

    ![Adicionar Empregado](./media/kantegassoforfisheyecrucible-tutorial/user3.png)

    a. Na caixa de texto **username,** digite o e-mail do utilizador como Brittasimon@contoso.com.

    b. Na caixa de texto 'Nome de **exibição',** digite o nome do utilizador como Britta Simon.

    c. Na caixa de texto de endereço de Brittasimon@contoso.com **e-mail,** digite o endereço de e-mail do utilizador como .

    d. Na caixa de texto **Password,** digite a palavra-passe do utilizador.

    e. Na caixa de texto **Confirmar Password,** reintroduza a palavra-passe do utilizador.

    f. Clique em **Adicionar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no Kantega SSO para azulejos FishEye/Crucible no Painel de Acesso, deve ser automaticamente inscrito no Kantega SSO para FishEye/Crucible para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)