---
title: 'Tutorial: Integração do Diretório Ativo Azure com conexão SAML ambiente virtual ON24 [ Conexão SAML ambiente virtual] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a ON24 Virtual Environment SAML Connection.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d4028fb5-b2ad-4c5d-b123-7b675c509d64
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: 801a631b56a11e68c444ede846ff82195cd7627f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095715"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Tutorial: Integração do Diretório Ativo Azure com conexão SAML ambiente virtual ON24

Neste tutorial, aprende-se a integrar a conexão ON24 Virtual Environment SAML com o Azure Ative Directory (Azure AD).
Integrar a conexão On24 Ambiente Virtual SAML com a AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso à conexão SAML ambiente virtual ON24.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na On24 Virtual Environment SAML Connection (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a conexão SAML ambiente virtual ON24, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* ON24 Virtual Environment SAML Connection única subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* ON24 Virtual Environment SAML Connection suporta **SP** e **IDP** iniciado SSO

## <a name="adding-on24-virtual-environment-saml-connection-from-the-gallery"></a>Adicionar conexão SAML ambiente virtual ON24 da galeria

Para configurar a integração da conexão ON24 Virtual Environment SAML em Azure AD, você precisa adicionar on24 Virtual Environment SAML Connection da galeria à sua lista de aplicações geridas saaS.

**Para adicionar on24 Virtual Environment SAML Connection from the gallery, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **on24 Virtual Environment SAML Connection**, selecione **ON24 Virtual Environment SAML Connection** from result panel e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![On24 Virtual Environment SAML Connection na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sinal de Azure AD com conexão SAML ambiente virtual ON24 com base num utilizador de teste chamado **Britta Simon**.
Para um único início de sessão funcionar, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em On24 Virtual Environment SAML Connection.

Para configurar e testar o único sinal de Acesso Azure AD com a conexão SAML ambiente virtual ON24, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure on24 Ambiente Virtual SAML Ligação Única Sinal-On](#configure-on24-virtual-environment-saml-connection-single-sign-on)** - para configurar as definições de início de sinal único no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Crie o utilizador do teste de **[conexão SAML ambiente virtual ON24](#create-on24-virtual-environment-saml-connection-test-user)** - para ter uma contrapartida da Britta Simon em on24 Virtual Environment SAML Connection que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sinal de Azure AD com a conexão On24 Virtual Environment SAML, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **on24 De** conexão saml ambiente virtual, selecione **single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** Se desejar configurar a aplicação no modo iniciado **idp,** execute os seguintes passos:

    ![ON24 Virtual Environment SAML Connection Domain and URLs single sign-on information](common/idp-relay.png)

    a. Na caixa de texto **identificador,** digite um URL:

     **URL de Ambiente de Produção**
    
    `SAML-VSHOW.on24.com`

    `SAML-Gateway.on24.com` 

    `SAP PROD SAML-EliteAudience.on24.com` 
                
     **URL do ambiente qa**
    
    `SAMLQA-VSHOW.on24.com` 

    `SAMLQA-Gateway.on24.com` 

    `SAMLQA-EliteAudience.on24.com`

    b. Na caixa de texto **URL resposta,** escreva um URL:

     **URL de Ambiente de Produção**
    
    `https://federation.on24.com/sp/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2`

     **URL do ambiente qa**
    
    `https://qafederation.on24.com/sp/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2`
     
    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` 

    c. Clique em **definir URLs adicionais**. 

    d. Na caixa de texto **do Estado relé,** escreva um URL:`https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

5.  Se desejar configurar a aplicação no modo iniciado **sp,** execute o seguinte passo:

    ![ON24 Virtual Environment SAML Connection Domain and URLs single sign-on information](common/both-signonurl.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://vshow.on24.com/vshow/<INSTANCENAME>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o estado real de retransmissão e URL de sign-on. Contacte a equipa de suporte do Cliente de [Conexão On24 Para](https://www.on24.com/contact-us/) obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

4. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na **secção on24 virtual ambiente sAML Connection,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-on24-virtual-environment-saml-connection-single-sign-on"></a>Configure ON24 Ambiente Virtual SAML Ligação Única Sign-On

Para configurar um único sinal no lado **on24 do ambiente virtual SAML Connection,** você precisa enviar o descarregamento da Federação **Metadados XML** e URLs copiados apropriados do portal Azure para a equipa de [suporte on24 Virtual Environment SAML Connection](https://www.on24.com/about-us/support/). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

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

Nesta secção, permite que a Britta Simon utilize um único sign-on Azure, concedendo acesso à Conexão SAML ambiente virtual ON24.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **on24 Virtual Environment SAML Connection**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **ON24 Virtual Environment SAML Connection**.

    ![O link on24 Virtual Environment SAML Connection na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-on24-virtual-environment-saml-connection-test-user"></a>Criar o utilizador de teste de ligação saml ambiente virtual ON24

Nesta secção, cria-se um utilizador chamado Britta Simon em On24 Virtual Environment SAML Connection. Trabalhe com a equipa de [suporte on24 Virtual Environment SAML Connection](https://www.on24.com/about-us/support/) para adicionar os utilizadores na plataforma ON24 Virtual Environment SAML Connection. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo on24 Virtual Environment SAML Connection no Painel de Acesso, deve ser automaticamente inscrito na conexão On24 Virtual Environment SAML para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

