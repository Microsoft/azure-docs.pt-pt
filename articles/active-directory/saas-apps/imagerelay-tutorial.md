---
title: 'Tutorial: Integração do Azure Ative Directory com Relé de Imagem / Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o Image Relay.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.openlocfilehash: 0c32dbdafe8d2681d341cb7f2ee6f0ce45123cab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826388"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Tutorial: Integração do Diretório Ativo Azure com Relé de Imagem

Neste tutorial, aprende-se a integrar o Image Relay com o Azure Ative Directory (Azure AD).
A integração do Relé de Imagem com Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Image Relay.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Relé de Imagem (Único Sinstrução) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o Image Relay, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada por Retransmissão de imagem

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Relé de imagem suporta **SSO** iniciado SP

## <a name="adding-image-relay-from-the-gallery"></a>Adicionar Relé de imagem da galeria

Para configurar a integração do Image Relay em Azure AD, é necessário adicionar o Image Relay da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Retransmissão de imagem da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Relé de imagem**, selecione **Relé** de imagem do painel de resultados e, em seguida, clique em Adicionar o botão **Adicionar** a aplicação.

    ![Retransmissão de imagem na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sinal de retransmissão de imagem baseado num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Image Relay.

Para configurar e testar o Azure AD com um único sinal de acesso com relé de imagem, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o único sinal de transmissão de imagem](#configure-image-relay-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Image Relay test user](#create-image-relay-test-user)** - para ter uma contraparte de Britta Simon em Image Relay que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com um único sinal de inscrição com relé de imagem, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Image Relay,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Direito de retransmissão de imagem e informações únicas de súmis](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<companyname>.imagerelay.com/`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente do Relay de Imagem](http://support.imagerelay.com/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção Configurar o **Relé de Imagem,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-image-relay-single-sign-on"></a>Configure relé de imagem Sign-On único

1. Em outra janela do navegador, inscreva-se no site da empresa Image Relay como administrador.

2. Na barra de ferramentas na parte superior, clique na carga de trabalho **& permissões dos Utilizadores.**

    ![A screenshot mostra utilizadores & permissões selecionadas a partir da barra de ferramentas.](./media/imagerelay-tutorial/tutorial_imagerelay_06.png) 

3. Clique **em Criar Nova Permissão**.

    ![A screenshot mostra uma caixa de texto para introduzir o título de Permissão e uma opção para escolher o Tipo de Permissão.](./media/imagerelay-tutorial/tutorial_imagerelay_08.png)

4. Na carga de trabalho **de sinais únicos sobre definições,** selecione **o grupo só pode iniciar sposição através de sinal único na** caixa de verificação e, em seguida, clique em **Guardar**.

    ![A screenshot mostra o sinal único nas definições onde pode selecionar a opção.](./media/imagerelay-tutorial/tutorial_imagerelay_09.png) 

5. Ir para **as Definições de Conta**.

    ![A screenshot mostra a opção barra de ferramentas 'Definições de conta'.](./media/imagerelay-tutorial/tutorial_imagerelay_10.png) 

6. Aceda à carga de trabalho **de sinal único nas definições.**

    ![A screenshot mostra a opção do menu single Sign On Settings.](./media/imagerelay-tutorial/tutorial_imagerelay_11.png)

7. No diálogo **de Definições SAML,** execute os seguintes passos:

    ![A screenshot mostra a caixa de diálogo de definições SAML onde pode introduzir as informações.](./media/imagerelay-tutorial/tutorial_imagerelay_12.png)

    a. Na caixa de texto **URL de login,** cole o valor do URL de **login** que copiou do portal Azure.

    b. Na caixa **de textos LOGOUT URL,**  cole o valor do **URL logout** que copiou do portal Azure.

    c. Como **Formato de Identificação de Nome**, selecione **urna:oasis:names:tc:SAML:1.1:nameid-formato:emailAddress**.

    d. Como **Opções vinculativas de Pedidos do Prestador de Serviços (Relé de Imagem)**, selecione **POST Binding**.

    e. No **certificado x.509,** clique no **Certificado de Atualização**.

    ![A screenshot mostra a opção de atualizar o certificado.](./media/imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Abra o certificado descarregado no bloco de notas, copie o conteúdo e, em seguida, cole-o na caixa de texto **do Certificado x.509.**

    ![A screenshot mostra o certificado x ponto 509.](./media/imagerelay-tutorial/tutorial_imagerelay_18.png)

    exemplo, Na secção **de Provisionamento do Utilizador Just-In-Time,** selecione a **provisão do utilizador "Tempo Justo".**

    ![A screenshot mostra a secção de Provisionamento do Utilizador Just-In-Time com o controlo de ativação selecionado.](./media/imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Selecione o grupo de permissões (por exemplo, **SSO Basic)** que só pode iniciar sing in através de uma única sing-on.

    ![A screenshot mostra a secção de Provisionamento do Utilizador Just-In-Time com S S O Basic selecionado.](./media/imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Clique em **Guardar**.

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

Nesta secção, você permite que Britta Simon utilize a Azure single sign-on, concedendo acesso ao Image Relay.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Image Relay**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Image Relay**.

    ![O link de retransmissão de imagem na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-image-relay-test-user"></a>Criar utilizador de teste de relé de imagem

O objetivo desta secção é criar um utilizador chamado Britta Simon in Image Relay.

**Para criar um utilizador chamado Britta Simon in Image Relay, execute os seguintes passos:**

1. Iniciar s-on no site da empresa Image Relay como administrador.

2. Vá aos **Utilizadores & permissões**     e selecione **Criar Utilizador SSO**.

    ![Screenshot mostra Create S S O User selecionado a partir do menu.](./media/imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. Introduza o **Email**, **Nome Próprio**, **Apelido**e **Empresa** do utilizador que pretende providenciar e selecione o grupo de permissões (por exemplo, SSO Basic) que é o grupo que só pode iniciar sinação através de uma única sintrodução.

    ![Screenshot mostra Criar uma página de utilizador S S O onde pode introduzir as informações necessárias.](./media/imagerelay-tutorial/tutorial_imagerelay_22.png)

4. Clique em **Criar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Relé de Imagem no Painel de Acesso, deverá ser automaticamente inscrito no Relé de Imagem para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)