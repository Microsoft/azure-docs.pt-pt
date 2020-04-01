---
title: 'Tutorial: Integração do Diretório Ativo Azure com saml SSO para Bitbucket por resolução GmbH [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SAML SSO para bitbucket por resolução GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: fc947df1-f24e-43ae-9a34-518293583d69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57ae83aee563d4893f767331fff2289a4595cc60
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73157643"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Tutorial: Integração de Diretório Ativo Azure com SAML SSO para Bitbucket por resolução GmbH

Neste tutorial, aprende-se a integrar o SAML SSO para bitbucket por resolução GmbH com o Azure Ative Directory (Azure AD).
Integrar o SAML SSO para bitbucket por resolução GmbH com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a SAML SSO para Bitbucket por resolução GmbH.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no SAML SSO para bitbucket por resolução GmbH (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o SAML SSO para bitbucket por resolução GmbH, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* SAML SSO para Bitbucket por resolução GmbH assinatura única ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* SAML SSO for Bitbucket por resolução A GmbH suporta **SP e IDP** iniciado SSO
* SAML SSO para Bitbucket por resolução A GmbH suporta o fornecimento de utilizadores **Just In Time**


## <a name="adding-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>Adicionar SAML SSO para Bitbucket por resolução GmbH da galeria

Para configurar a integração do SAML SSO para bitbucket por resolução GmbH em Azure AD, você precisa adicionar SAML SSO para Bitbucket por resolução GmbH da galeria para a sua lista de aplicações SaaS geridas.

**Para adicionar SAML SSO para Bitbucket por resolução GmbH a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SAML SSO para Bitbucket por resolução GmbH,** selecione **SAML SSO para Bitbucket por resolução GmbH** do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![SAML SSO para Bitbucket por resolução GmbH na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com saml SSO para Bitbucket por resolução GmbH com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no SAML SSO for Bitbucket por resolução.

Para configurar e testar o único signo azure ad com o SAML SSO para Bitbucket por resolução GmbH, você precisa completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o SAML SSO para Bitbucket por resolução GmbH Single Sign-On](#configure-saml-sso-for-bitbucket-by-resolution-gmbh-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie SAML SSO para Bitbucket por resolução O utilizador](#create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)** de teste GmbH - para ter uma contrapartida da Britta Simon no SAML SSO para bitbucket por resolução GmbH que está ligada à representação azure AD do utilizador.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único signo da Azure AD com o SAML SSO para bitbucket por resolução GmbH, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), no **SAML SSO para Bitbucket por resolução página** de integração de aplicações GmbH, selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos se pretender configurar a aplicação no modo iniciado do **IDP:**

    ![SAML SSO para Bitbucket por resolução GmbH Domain e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<server-base-url>/plugins/servlet/samlsso`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<server-base-url>/plugins/servlet/samlsso`

    c. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    ![SAML SSO para Bitbucket por resolução GmbH Domain e URLs informações únicas de inscrição](common/metadata-upload-additional-signon.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta real e URL de sinalização. Contacte a [SAML SSO para bitbucket por resolução A equipa](https://marketplace.atlassian.com/apps/1217045/saml-single-sign-on-sso-bitbucket?hosting=server&tab=support) de suporte ao Cliente GmbH para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

### <a name="configure-saml-sso-for-bitbucket-by-resolution-gmbh-single-sign-on"></a>Configure SAML SSO para Bitbucket por resolução GmbH Single Sign-On

1. Inscreva-se no seu SAML SSO para Bitbucket por resolução do site da empresa GmbH como administrador.

2. No lado direito da barra de ferramentas principal, clique em **Definições**.

3. Vá à secção CONTAS, clique no **SAML SingleSignOn** na barra de menus.

    ![O Samlsingle](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

4. Na página de configuração do **plugin SAML SIngleSignOn,** clique em **Adicionar idp**. 

    ![O Add idp](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

5. Na página Escolha da Sua Página de Fornecedor de **Identidade SAML,** execute os seguintes passos:

    ![O fornecedor de identidade](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. Selecione **Idp Type** como **AZURE AD**.

    b. Na caixa de texto **Name,** digite o nome.

    c. Na caixa de texto **descrição,** digite a descrição.

    d. Clique em **Seguinte**.

6. Na página de configuração do **fornecedor de identidade,** clique em **Seguinte**.

    ![O config de identidade](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

7.  Na página **de metadados De saml idp importado,** clique em **Ficheiro de Carregamento** para carregar o ficheiro **METADATA XML** que descarregou do portal Azure.

    ![Os idpmetametametametade](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)
    
8. Clique em **Seguinte**.

9. Clique em **Guardar definições**.

    ![A defesa](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)

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

Nesta secção, permite que a Britta Simon utilize um único sign-on Azure, concedendo acesso ao SAML SSO para Bitbucket por resolução GmbH.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações,** em seguida, selecione **SAML SSO para Bitbucket por resolução GmbH**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, digite e selecione **SAML SSO para Bitbucket por resolução GmbH**.

    ![O SAML SSO para Bitbucket por resolução Ligação GmbH na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>Crie SAML SSO para Bitbucket por resolução Utilizador de teste GmbH

O objetivo desta secção é criar um utilizador chamado Britta Simon no SAML SSO para Bitbucket por resolução GmbH. SAML SSO for Bitbucket por resolução A GmbH suporta o fornecimento just-in-time e também os utilizadores podem ser criados manualmente, contacte [a SAML SSO para bitbucket por resolução A equipa](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) de suporte ao Cliente GmbH, de acordo com o seu requisito.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no SAML SSO para Bitbucket por resolução De azulejos GmbH no Painel de Acesso, deve ser automaticamente inscrito no SAML SSO para Bitbucket por resolução GmbH para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

