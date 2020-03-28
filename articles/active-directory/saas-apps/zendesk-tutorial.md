---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Zendesk Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Zendesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4887457f457ff533a0eaf10be7db1627a950f5bd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233272"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Tutorial: Integração do Diretório Ativo Azure com a Zendesk

Neste tutorial, aprende-se a integrar a Zendesk com o Azure Ative Directory (Azure AD).
Integrar a Zendesk com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso a Zendesk.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Zendesk (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Zendesk, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por um único sinal de Zendesk

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Zendesk suporta **SP** iniciado SSO

* Zendesk suporta fornecimento [ **automatizado** de utilizadores](zendesk-provisioning-tutorial.md)

## <a name="adding-zendesk-from-the-gallery"></a>Adicionando Zendesk da galeria

Para configurar a integração da Zendesk em Azure AD, precisa adicionar Zendesk da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Zendesk da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Zendesk**, selecione **Zendesk** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Zendesk na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sign-on azure com a Zendesk com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Zendesk.

Para configurar e testar o único sinal de Azure AD com a Zendesk, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Postdesk Single Sign-On](#configure-zendesk-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador](#create-zendesk-test-user)** de teste Zendesk - para ter uma contrapartida de Britta Simon em Zendesk que esteja ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com zendesk, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Zendesk,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Zendesk Domain e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<subdomain>.zendesk.com`

   b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<subdomain>.zendesk.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de suporte do [Cliente Zendesk](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. A aplicação Zendesk espera as afirmações da SAML num formato específico. Não existem atributos SAML obrigatórios, mas opcionalmente pode gerir a partir da secção **Deatributos** do Utilizador na página de integração de aplicações. Na configuração do Open Sign-On com a página **SAML,** clique no botão **Editar** para abrir o diálogo **de Atributos do Utilizador.**

    ![image](common/edit-attribute.png)

6. Na secção **Reivindicações** do Utilizador no diálogo **de Atributos** do Utilizador, configure o atributo token SAML como mostrado na imagem acima e execute os seguintes passos:

    a. Clique **Em adicionar nova alegação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Name,** digite o nome do atributo mostrado para essa linha.

    c. Deixe o espaço de **nome em** branco.

    d. Selecione Origem como **Atributo**.

    e. Na lista de **atributos Fonte,** selecione o valor de atributo apropriado.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

    > [!NOTE]
    > Utiliza atributos de extensão para adicionar atributos que não estão em AD Azure por padrão. Clique [em atributos de utilizador que podem ser definidos no SAML](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) para obter a lista completa de atributos SAML que **zendesk** aceita.

7. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** copie a **impressão digital** e guarde-a no seu computador.

    ![Copiar valor de impressão digital](common/copy-certificatethumbprint.png)

8. Na secção **De configurar zendesk,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

9. Há duas maneiras em que zendesk pode ser configurado - Automático e Manual.
  
10. Para automatizar a configuração dentro do Zendesk, precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![image](./media/zendesk-tutorial/install_extension.png)

11. Depois de adicionar extensão ao navegador, clique em **Configurar Zendesk** irá direcioná-lo para a aplicação Zendesk. A partir daí, forneça as credenciais de administração para assinar em Zendesk. A extensão do navegador configurará automaticamente a aplicação para si e automatizará a secção **Configure Zendesk Single Sign-On**.

    ![image](./media/zendesk-tutorial/d2_saml.png)

### <a name="configure-zendesk-single-sign-on"></a>Configure O Único Sign-On da Zendesk

1. Se quiser configurar o Zendesk manualmente, abra uma nova janela do navegador web e inicie sessão no site da empresa Zendesk como administrador e execute os seguintes passos:

2. Clique **em Admin**.

3. No painel de navegação à esquerda, clique em **Definições**, e, em seguida, clique em **Segurança**.

4. Na página **de Segurança,** execute os seguintes passos:

    ![Segurança](././media/zendesk-tutorial/ic773089.png "Segurança")

    ![Início de sessão único](././media/zendesk-tutorial/ic773090.png "Início de sessão único")

    a. Clique no separador **Agentes de & de Administração.**

    b. Selecione **um único sinal (SSO) e SAML,** e, em seguida, selecione **SAML**.

    c. Na caixa de texto **URL SAML SSO,** colá o valor do URL de **Login** que copiou do portal Azure.

    d. Na caixa de texto url de **logout remoto,** colá o valor do URL de **Logout** que copiou do portal Azure.

    e. Na caixa de texto **Impressões Digitais de Certificado,** cola o valor de **impressão digital** do certificado que copiou do portal Azure.

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
  
    b. No **tipo** de campo de nome utilizador **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso à Zendesk.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações**e, em seguida, selecione **Zendesk**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, digite e selecione **Zendesk**.

    ![O link Zendesk na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-zendesk-test-user"></a>Criar o utilizador de teste Zendesk

O objetivo desta secção é criar uma utilizadora chamada Britta Simon em Zendesk. A Zendesk suporta o fornecimento automático de utilizadores, que é por defeito. Pode encontrar mais detalhes [aqui](Zendesk-provisioning-tutorial.md) sobre como configurar o fornecimento automático de utilizadores.

**Se necessitar de criar manualmente o utilizador, execute os seguintes passos:**

> [!NOTE]
> **As** contas de utilizador final são automaticamente aprovisionadas ao iniciar a sua sessão. **As** contas do Agente e **da Admin** têm de ser aprovisionadas manualmente na **Zendesk** antes de iniciarem a sua inscrição.

1. Faça login no seu inquilino **Zendesk.**

2. Selecione o separador Lista de **Clientes.**

3. Selecione o separador **Utilizador** e clique em **Adicionar**.

    ![Adicionar utilizador](././media/zendesk-tutorial/ic773632.png "Adicionar utilizador")
4. Digite o **Nome** e **O E-mail** de uma conta AD Azure existente que pretende fornecer e, em seguida, clique em **Guardar**.

    ![Novo utilizador](././media/zendesk-tutorial/ic773633.png "Novo utilizador")

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador Zendesk ou APIs fornecidas pela Zendesk para fornecer contas de utilizador da Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Zendesk no Painel de Acesso, deverá ser automaticamente inscrito no Zendesk para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar o fornecimento de utilizadores](zendesk-provisioning-tutorial.md)