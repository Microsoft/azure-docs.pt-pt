---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Kantega SSO para confluência Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Kantega SSO para confluência.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d0d99c14-a6ca-45f2-bb84-633126095e7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 27fa0567eefbb50907c0ed6952333230e874c21d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67099043"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-confluence"></a>Tutorial: Integração do Diretório Ativo Azure com a Kantega SSO para Confluence

Neste tutorial, aprende-se a integrar o Kantega SSO para confluência com o Azure Ative Directory (Azure AD).
Integrar o Kantega SSO para confluência com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Kantega SSO para Confluence.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Kantega SSO para confluência (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Kantega SSO para confluência, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Kantega SSO para a subscrição ativada por um único sign-on de Confluência

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Kantega SSO para Confluence suporta **SP e IDP** iniciado SSO

## <a name="adding-kantega-sso-for-confluence-from-the-gallery"></a>Adicionando Kantega SSO para Confluência da galeria

Para configurar a integração do Kantega SSO para confluência em Azure AD, você precisa adicionar Kantega SSO para Confluence da galeria para a sua lista de aplicações geridas saaS.

**Para adicionar Kantega SSO para Confluence a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Kantega SSO para Confluence,** **selecione Kantega SSO para confluência** a partir do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

    ![Kantega SSO para Confluência na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com a Kantega SSO para confluência com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no SSO Kantega para confluência.

Para configurar e testar o único sinal de Azure AD com a Kantega SSO para confluência, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Kantega SSO para confluência single sign-on](#configure-kantega-sso-for-confluence-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o Kantega SSO para o utilizador](#create-kantega-sso-for-confluence-test-user)** do teste de confluência - para ter uma contrapartida de Britta Simon em Kantega SSO para confluência que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure com a Kantega SSO para confluência, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página **kantega SSO para** integração de aplicações de confluência, selecione **single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado **idp,** execute os seguintes passos:

    ![Kantega SSO para confluência de domínio e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    ![Kantega SSO para confluência de domínio e URLs informações únicas de inscrição](common/metadata-upload-additional-signon.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta e URL de sinal. Estes valores são recebidos durante a configuração do plugin confluence, o que é explicado mais tarde no tutorial.

6. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

7. Na **configuração do SSO Kantega para** a secção De Confluência, copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-kantega-sso-for-confluence-single-sign-on"></a>Configure Kantega SSO para Confluência Single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no seu portal de **administração da Confluence** como administrador.

1. Passe sobre a engrenagem e clique nos **Add-ons**.

    ![Configurar um único sinal](./media/kantegassoforconfluence-tutorial/addon1.png)

1. Sob o **separador ATLASSIAN MARKETPLACE,** clique em **Encontrar novos add-ons**.

    ![Configurar um único sinal](./media/kantegassoforconfluence-tutorial/addon.png)

1. Procure na **Kantega SSO a Confluence SAML Kerberos** e clique em **instalar** o novo plugin SAML.

    ![Configurar um único sinal](./media/kantegassoforconfluence-tutorial/addon2.png)

1. A instalação de plugin começa.

    ![Configurar um único sinal](./media/kantegassoforconfluence-tutorial/addon3.png)

1. Uma vez concluída a instalação. Clique em **Fechar**.

    ![Configurar um único sinal](./media/kantegassoforconfluence-tutorial/addon33.png)

1. Clique em **Gerir**.

    ![Configurar um único sinal](./media/kantegassoforconfluence-tutorial/addon34.png)

1. Clique em **Configurar** para configurar o novo plugin.

    ![Configurar um único sinal](./media/kantegassoforconfluence-tutorial/addon35.png)

1. Este novo plugin também pode ser encontrado no separador **DE SEGURANÇA & UTILIZADORES.**

    ![Configurar um único sinal](./media/kantegassoforconfluence-tutorial/addon36.png)

1. Na secção **SAML.** Selecione **Azure Ative Directory (Azure AD)** a partir do dropdown do fornecedor de **identidade Add.**

    ![Configurar um único sinal](./media/kantegassoforconfluence-tutorial/addon4.png)

1. Selecione o nível de subscrição como **Basic**.

    ![Configurar um único sinal](./media/kantegassoforconfluence-tutorial/addon5.png)

1. Na secção propriedades da **App,** execute os seguintes passos:

    ![Configurar um único sinal](./media/kantegassoforconfluence-tutorial/addon6.png)

    a. Copie o valor DO ID URI da **aplicação** e use-o como **Identifier, Answer URL e Url de Início de Sinal** na secção básica de **configuração SAML** no portal Azure.

    b. Clique em **Seguinte**.

1. Na secção de importação de **Metadados,** execute os seguintes passos: 

    ![Configurar um único sinal](./media/kantegassoforconfluence-tutorial/addon7.png)

    a. Selecione **ficheiro Metadados no meu computador**e faça upload do ficheiro de metadados, que descarregou do portal Azure.

    b. Clique em **Seguinte**.

1. Na secção de **localização Nome e SSO,** execute os seguintes passos:

    ![Configurar um único sinal](./media/kantegassoforconfluence-tutorial/addon8.png)

    a. Adicione o Nome do Fornecedor de Identidade na **denominação** de código de identidade (por exemplo, Azure AD).

    b. Clique em **Seguinte**.

1. Verifique o certificado de assinatura e clique **em Seguinte**.

    ![Configurar um único sinal](./media/kantegassoforconfluence-tutorial/addon9.png)

1. Na secção de contas de utilizador da **Confluence,** execute os seguintes passos:

    ![Configurar um único sinal](./media/kantegassoforconfluence-tutorial/addon10.png)

    a. Selecione **Criar utilizadores no Diretório Interno da Confluence, se necessário,** e introduza o nome apropriado do grupo para os utilizadores (pode ser múltiplos não. de grupos separados por vírina).

    b. Clique em **Seguinte**.

1. Clique em **Concluir**.

    ![Configurar um único sinal](./media/kantegassoforconfluence-tutorial/addon11.png)

1. Nos **domínios conhecidos da secção Azure AD,** execute os seguintes passos: 

    ![Configurar um único sinal](./media/kantegassoforconfluence-tutorial/addon12.png)

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
  
    b. No tipo de campo de **nome do utilizador**`brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso à Kantega SSO para confluência.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Kantega SSO para Confluência**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Kantega SSO para Confluence**.

    ![O SSO Kantega para confluência na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-kantega-sso-for-confluence-test-user"></a>Criar Kantega SSO para utilizador de teste de confluência

Para permitir que os utilizadores de AD Azure assinem a Confluence, devem ser aprovisionados em Confluence. No caso do SSO Kantega para confluência, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu Kantega SSO para o site da empresa Confluence como administrador.

1. Passe sobre a engrenagem e clique na gestão do **Utilizador.**

    ![Adicionar Empregado](./media/kantegassoforconfluence-tutorial/user1.png)

1. Na secção Utilizadores, clique no separador **Adicionar Utilizadores.** Na página adicionar um diálogo **do Utilizador,** execute os seguintes passos:

    ![Adicionar Empregado](./media/kantegassoforconfluence-tutorial/user2.png)

    a. Na caixa de texto **username,** digite o e-mail do utilizador como Brittasimon@contoso.com.

    b. Na caixa de texto **Full Name,** digite o nome completo de utilizador como Britta Simon.

    c. Na caixa de texto **e-mail,** Brittasimon@contoso.comdigite o endereço de e-mail do utilizador como .

    d. Na caixa de texto **Password,** digite a palavra-passe para o utilizador.

    e. Clique **em Confirmar A Palavra-passe** reintroduza a palavra-passe.

    f. Clique no botão **Adicionar.**

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no SSO Kantega para confluência no Painel de Acesso, deve ser automaticamente inscrito no SSO Kantega para confluência para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

