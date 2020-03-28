---
title: 'Tutorial: Integração de Diretório Sonárgico Azure com riskware [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Riskware.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 6eaa1be81d3ac0733c0829bc45e1b62f8aae5755
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "72027112"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Tutorial: Integração de Diretório Sonárte Azure com Riskware

Neste tutorial, aprende-se a integrar o Riskware com o Azure Ative Directory (Azure AD).
Integrar o Riskware com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao Riskware.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Riskware (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Riskware, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura de inscrição única de riskware

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Riskware suporta **SP** iniciado SSO

## <a name="adding-riskware-from-the-gallery"></a>Adicionar Riskware da galeria

Para configurar a integração do Riskware em Azure AD, precisa de adicionar riskware da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Riskware da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Riskware**, selecione **Riskware** do painel de resultados e, em seguida, clique em adicionar o botão **Adicionar** a aplicação.

    ![Riskware na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com riskware com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Riskware.

Para configurar e testar o único sinal de Azure AD com o Riskware, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Riskware Single Sign-On](#configure-riskware-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Create Riskware test user](#create-riskware-test-user)** - para ter uma contraparte de Britta Simon em Riskware que esteja ligada à representação da AD Azure do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com riskware, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Riskware,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Riskware Domain e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:
    
    | Ambiente| Padrão URL|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PROD| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMONSTRAÇÃO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. Na caixa de texto **identificador (Id** da entidade), escreva o URL:
    
    | Ambiente| Padrão URL|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | PROD| `https://riskcloud.net/prod` |
    | DEMONSTRAÇÃO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > O valor do Signon on URL não é real. Atualize o valor com o URL de Sign-On real. Contacte a equipa de suporte do [Cliente Riskware](mailto:support@pansoftware.com.au) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na secção **'Configurar 'Riskware',** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-riskware-single-sign-on"></a>Configure Riskware Single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Riskware como administrador.

1. Na parte superior direita, clique em **Manutenção** para abrir a página de manutenção.

    ![Configurações de software de risco mantêm](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na página de manutenção, clique na **Autenticação.**

    ![Authen de configuração de software de risco](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. Na página de **Configuração de Autenticação,** execute os seguintes passos:

    ![Authenconfig configuração de software de risco](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Selecione **Tipo** como **SAML** para autenticação.

    b. Na caixa de texto **Código,** digite o seu código como AZURE_UAT.

    c. Na caixa de texto **Descrição,** digite a sua descrição como configuração AZURE para SSO.

    d. Na caixa de texto **Single Sign On Page,** colá o valor URL do **Login,** que copiou do portal Azure.

    e. No **Sign out Page** textbox, colhe o valor URL de **Logout,** que copiou do portal Azure.

    f. Na caixa de texto **post form field,** digite o nome de campo presente na Resposta Postal que contém SAML como SAMLResponse

    g. Na caixa de texto **XML Identity Tag Name,** digite atributo, que contém o identificador único na resposta SAML como NameID.

    h. Abra os **Metadados Xml** descarregados do portal Azure no bloco de notas, copie o certificado do ficheiro Metadados e cole-o na caixa de texto **do Certificado**

    i. Na caixa de texto **URL do Consumidor,** colhe o valor do URL de **Resposta,** que obtém da equipa de suporte.

    j. Na caixa de texto **Emitida,** cola o valor do **Identificador,** que obtém da equipa de suporte.

    > [!Note]
    > Contacte a equipa de suporte ao [cliente riskware](mailto:support@pansoftware.com.au) para obter estes valores

    k. Selecione **Use post** checkbox.

    l. Selecione Use a caixa de verificação **SAML Request.**

    m. Clique em **Guardar**.

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

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao Riskware.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Riskware**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Riskware**.

    ![O link Riskware na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-riskware-test-user"></a>Criar utilizador de teste de Riskware

Para permitir que os utilizadores da AD Azure assinem o Riskware, estes devem ser aprovisionados no Riskware. No Riskware, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no Riskware como administrador de segurança.

1. Na parte superior direita, clique em **Manutenção** para abrir a página de manutenção. 

    ![Configuração de software de risco mantém](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na página de manutenção, clique em **Pessoas.**

    ![Pessoas de configuração de software de risco](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Selecione o separador **Details** e execute os seguintes passos:

    ![Detalhes de configuração de software de risco](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Selecione **Pessoa Tipo** como Empregado.

    b. Na caixa de texto **First Name,** introduza o primeiro nome de utilizador como **Britta**.

    c. Na caixa de texto **Sobrenome,** introduza o último nome de utilizador como **Simon**.

1. No separador **Segurança,** execute os seguintes passos:

    ![Segurança de configuração de software de risco](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. Na secção **autenticação,** selecione o modo **de autenticação,** que configura como configuração AZURE para SSO.

    b. Na secção Detalhes de **Início,** na caixa de texto `brittasimon@contoso.com`ID do **utilizador,** introduza o e-mail do utilizador como .

    c. Na caixa de texto **Password,** introduza a palavra-passe do utilizador.

1. No separador **Organização,** execute os seguintes passos:

    ![Configuração de software de risco org](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Selecione a opção como organização **Level1.**

    b. Na secção local **de trabalho primário da pessoa,** na caixa de texto **local,** digite a sua localização.

    c. Na secção **Empregado,** selecione **Estatuto de Empregado** como Casual.

    d. Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Riskware no Painel de Acesso, deve ser automaticamente inscrito no Riskware para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
