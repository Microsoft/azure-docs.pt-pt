---
title: 'Tutorial: Integração do Azure Ative Directory com a SAML SSO para a Bitbucket por resolução GmbH Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SAML SSO para a Bitbucket por resolução GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: 5e0b1d8d2cb80886131a88c01a6c24102ad7c77c
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673565"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Tutorial: Integração do Azure Ative Directory com a SAML SSO para a Bitbucket por resolução GmbH

Neste tutorial, aprende-se a integrar o SAML SSO para a Bitbucket através da resolução GmbH com o Azure Ative Directory (Azure AD).
A integração do SSO SAML para a Bitbucket por resolução a GmbH com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a SAML SSO para Bitbucket por resolução GmbH.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no SSO SAML para Bitbucket por resolução GmbH (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a SAML SSO para a Bitbucket por resolução GmbH, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* SAML SSO para Bitbucket por resolução Assinatura ativada pela GmbH

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* SAML SSO para Bitbucket por resolução GmbH suporta **SP e IDP** iniciado SSO
* SAML SSO para Bitbucket por resolução A GmbH suporta o fornecimento do utilizador **Just In Time**


## <a name="adding-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>Adicionar SSO SAML para Bitbucket por resolução GmbH da galeria

Para configurar a integração do SSO SAML para a Bitbucket através da resolução GmbH em Azure AD, precisa de adicionar SSO SAML para a Bitbucket através da resolução GmbH da galeria à sua lista de aplicações geridas para o SaaS.

**Para adicionar SSO SAML para Bitbucket por resolução GmbH da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva SAML SSO para Bitbucket por resolução GmbH**, selecione **SAML SSO para Bitbucket por resolução GmbH** do painel de resultados e clique em **Adicionar** botão para adicionar a aplicação.

    ![SAML SSO para Bitbucket por resolução GmbH na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com sSO saml sso para bitbucket por resolução GmbH com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no SSO SAML para a Bitbucket por resolução.

Para configurar e testar o único sinal de Azure AD com o SAML SSO para a Bitbucket por resolução GmbH, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o SSO SAML para bitbucket por resolução GmbH Single Sign-On](#configure-saml-sso-for-bitbucket-by-resolution-gmbh-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Crie o SAML SSO para bitbucket por resolução utilizador](#create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)** de teste GmbH - para ter uma contraparte de Britta Simon em SSO SAML para a Bitbucket por resolução GmbH que está ligada à representação AD Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD um único sinal de lançamento com o SAML SSO para a Bitbucket por resolução GmbH, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), no **SSO SAML para Bitbucket por resolução página** de integração de aplicações GmbH, selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** execute os seguintes passos se desejar configurar a aplicação no modo iniciado pelo **IDP:**

    ![A screenshot mostra a Configuração BÁSICA SAML, onde pode introduzir o Identificador, Responder U R L e selecionar Guardar.](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![Screenshot mostra Definir U R Ls adicionais onde pode introduzir um sinal em U R L.](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a SAML SSO para a Bitbucket por resolução a equipa de suporte do cliente gmbH](https://marketplace.atlassian.com/apps/1217045/saml-single-sign-on-sso-bitbucket?hosting=server&tab=support) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

### <a name="configure-saml-sso-for-bitbucket-by-resolution-gmbh-single-sign-on"></a>Configure o SSO SAML para a Bitbucket por resolução GmbH Single Sign-On

1. Iniciar s-on no seu SSO SAML para Bitbucket por resolução do site da empresa GmbH como administrador.

2. No lado direito da barra de ferramentas principal, clique em **Definições**.

3. Aceda à secção CONTAS, clique em **SAML SingleSignOn** na barra de menus.

    ![O Samlsingle](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

4. Na **página de configuração do plugin SAML SIngleSignOn**, clique em **Adicionar idp**. 

    ![O Add idp](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

5. Na página escolha a sua página **de fornecedor de identidade SAML,** execute os seguintes passos:

    ![O fornecedor de identidade](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. Selecione **Idp Type** como **AZURE AD**.

    b. Na caixa de texto **'Nome',** digite o nome.

    c. Na caixa de texto **Descrição,** digite a descrição.

    d. Clique em **Seguinte**.

6. Na **página de configuração do fornecedor de identidade**, clique em **Seguinte**.

    ![A identidade config](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

7.  Na página **de metadados do Idp Import SAML,** clique em **Carregar Ficheiro** para carregar o ficheiro **METADADATA XML** que descarregou a partir do portal Azure.

    ![O idpmetadata](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)

8. Clique em **Seguinte**.

9. Clique em **Guardar definições**.

    ![A poupança](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.

    b. No tipo de campo **nome de utilizador** **brittasimon \@ yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon utilize o Azure single sign-on, concedendo acesso a SSO SAML para Bitbucket por resolução GmbH.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **SAML SSO para Bitbucket por resolução GmbH**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, escreva e selecione **SAML SSO para Bitbucket por resolução GmbH**.

    ![O SSO SAML para Bitbucket por resolução Ligação GmbH na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>Criar SSO SAML para Bitbucket por resolução Utilizador de teste GmbH

O objetivo desta secção é criar um utilizador chamado Britta Simon na SAML SSO para a Bitbucket por resolução GmbH. SAML SSO for Bitbucket por resolução A GmbH suporta o provisionamento just-in-time e também os utilizadores podem ser criados manualmente, contacte [a SAML SSO para a Bitbucket através da resolução da equipa de suporte do Cliente GmbH,](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) conforme o seu requisito.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no SSO SAML para Bitbucket por resolução, o azulejo GmbH no Painel de Acesso, deverá ser automaticamente inscrito no SSO SAML para Bitbucket por resolução gmbH para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)