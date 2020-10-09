---
title: 'Tutorial: Integração do Azure Ative Directory com a Kantega SSO para o Bambu Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Kantega SSO para o Bamboo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 5b1034454bbdee32b33222321ded2421b83bfd0d
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850783"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bamboo"></a>Tutorial: Integração do Azure Ative Directory com a Kantega SSO para bambu

Neste tutorial, você aprende a integrar Kantega SSO para bambu com Azure Ative Directory (Azure AD).
A integração da Kantega SSO para bambu com Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Kantega SSO para Bambu.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no SSO da Kantega para bambu (Sign-on único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a Kantega SSO para bambu, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* SSO Kantega para assinatura ativada única de bambu

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Kantega SSO para Bambu suporta **SP e IDP** iniciado SSO

## <a name="adding-kantega-sso-for-bamboo-from-the-gallery"></a>Adicionando SSO Kantega para Bambu da galeria

Para configurar a integração do Kantega SSO para Bamboo em AD Azure, precisa adicionar O SSO kantega para bambu da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Kantega SSO para Bambu da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Kantega SSO para Bambu**, selecione **Kantega SSO para Bambu** a partir do painel de resultados e clique em **Adicionar** botão para adicionar a aplicação.

    ![SSO de Kantega para Bambu na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Kantega SSO for Bamboo com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no SSO kantega para bambu.

Para configurar e testar o Azure AD com o Kantega SSO para bambu, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Kantega SSO para o Sign-On Único de Bambu](#configure-kantega-sso-for-bamboo-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Kantega SSO for Bamboo test user](#create-kantega-sso-for-bamboo-test-user)** - para ter uma contraparte de Britta Simon em Kantega SSO para Bambu que está ligada à representação AD Ad Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com Kantega SSO para Bambu, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página **Kantega SSO para** integração de aplicações de bambu, selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![A screenshot mostra a Configuração BÁSICA SAML, onde pode introduzir o Identificador, Responder U R L e selecionar Guardar.](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![Screenshot mostra Definir U R Ls adicionais onde pode introduzir um sinal em U R L.](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL Sign-On. Estes valores são recebidos durante a configuração do plugin de Bambu, que é explicado mais tarde no tutorial.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

7. Na secção **Configurar o SSO kantega para** bambu, copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-kantega-sso-for-bamboo-single-sign-on"></a>Configure Kantega SSO para Sign-On single de bambu

1. Numa janela diferente do navegador web, inscreva-se no seu servidor bamboo no local como administrador.

1. Hover on cog e clique nos **Add-ons**.

    ![A screenshot mostra add-ons selecionados a partir do menu Definições.](./media/kantegassoforbamboo-tutorial/addon1.png)

1. Na secção de separadores de add-ons, clique **em Localizar novos addons**. Pesque **o SSO da Kantega para bambu (SAML & Kerberos)** e clique em Instalar o botão **Instalar** o novo plugin SAML.

    ![A screenshot mostra a Administração de Bambu com Kantega S S O para Bambu selecionado.](./media/kantegassoforbamboo-tutorial/addon2.png)

1. A instalação plugin irá arrancar.

    ![Screenshot mostra instalação de progresso para Kantega S S O para bambu.](./media/kantegassoforbamboo-tutorial/addon21.png)

1. Uma vez concluída a instalação. Clique em **Close** (Fechar).

    ![A imagem mostra o botão Close.](./media/kantegassoforbamboo-tutorial/addon33.png)

1. Clique em **Gerir**.

    ![A imagem mostra o botão Gerir.](./media/kantegassoforbamboo-tutorial/addon34.png)

1. Clique **em Configurar** para configurar o novo plugin.

    ![A screenshot mostra add-ons instalados pelo utilizador com configuração selecionadas.](./media/kantegassoforbamboo-tutorial/addon3.png)

1. Na secção **SAML.** Selecione **Azure Ative Directory (Azure AD)** a partir do **dropdown do fornecedor** de identidade Add.

    ![A screenshot mostra Kantega Single Sign-On com Azure A D selecionado como fornecedor de identidade.](./media/kantegassoforbamboo-tutorial/addon4.png)

1. Selecione o nível de subscrição como **Basic**.

    ![Screenshot mostra Prepare Azure A D com Basic selecionado.](./media/kantegassoforbamboo-tutorial/addon5.png)

1. Na secção propriedades da **App,** execute os seguintes passos:

    ![O Screenshot mostra a secção de propriedades da App onde pode fornecer as informações neste passo.](./media/kantegassoforbamboo-tutorial/addon6.png)

    a. Copie o valor **ID URI** da aplicação e use-o como **Identificador, URL de resposta e URL de Sign-On** na secção de **Configuração Básica SAML** no portal Azure.

    b. Clique em **Seguinte**.

1. Na secção de **importação de metadados,** execute os seguintes passos:

    ![A screenshot mostra a secção de importação de metadados onde pode navegar para um ficheiro de metadados.](./media/kantegassoforbamboo-tutorial/addon7.png)

    a. Selecione **o ficheiro Metadados no meu computador**e carreja o ficheiro metadados, que descarregou a partir do portal Azure.

    b. Clique em **Seguinte**.

1. Na secção **nome e sSo,** execute os seguintes passos:

    ![A screenshot mostra o local de Nome e S O onde Azure A D é o nome do fornecedor de identidade.](./media/kantegassoforbamboo-tutorial/addon8.png)

    a. Adicionar Nome do Fornecedor de Identidade na caixa de texto **do fornecedor de identidade** (por exemplo, Azure AD).

    b. Clique em **Seguinte**.

1. Verifique o certificado de assinatura e clique em **Seguinte**.

    ![A imagem mostra a verificação da assinatura.](./media/kantegassoforbamboo-tutorial/addon9.png)

1. Na secção **de contas de utilizador de Bamboo,** execute os seguintes passos:

    ![O Screenshot mostra contas de utilizadores de Bamboo onde tem a opção de criar utilizadores.](./media/kantegassoforbamboo-tutorial/addon10.png)

    a. Selecione **Criar utilizadores no Diretório interno de Bamboo se necessário** e introduzir o nome apropriado do grupo para utilizadores (pode ser um número múltiplo. de grupos separados por vírgula).

    b. Clique em **Seguinte**.

1. Clique em **Concluir**.

    ![A imagem mostra a página Resumo.](./media/kantegassoforbamboo-tutorial/addon11.png)

1. Nos **domínios conhecidos para a secção AD Azure,** execute os seguintes passos:

    ![A screenshot mostra os domínios conhecidos para Azure A D onde pode executar estes passos.](./media/kantegassoforbamboo-tutorial/addon12.png)

    a. Selecione **Domínios conhecidos** do painel esquerdo da página.

    b. Introduza o nome de domínio na caixa de texto **de domínios conhecidos.**

    c. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador**`brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao SSO Kantega para Bambu.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Kantega SSO para Bambu**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Kantega SSO para Bambu.**

    ![O SSO Kantega para o link bamboo na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-kantega-sso-for-bamboo-test-user"></a>Criar SSO Kantega para utilizador de teste de Bambu

Para permitir que os utilizadores de Azure AD inscrevam-se em Bamboo, devem ser a provisionados em Bambu. No caso de Kantega SSO para Bambu, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu servidor bamboo no local como administrador.

1. Hover on cog e clique na **gestão**do Utilizador .

    ![A screenshot mostra a Gestão do Utilizador selecionada no menu Definições.](./media/kantegassoforbamboo-tutorial/user1.png)

1. Clique em **Utilizadores**. Na secção **'Adicionar utilizador',** execute os seguintes passos:

    ![A screenshot mostra o painel de utilizador Adicionar onde pode executar estes passos.](./media/kantegassoforbamboo-tutorial/user2.png)

    a. Na caixa de texto **username,** escreva o e-mail do utilizador como Brittasimon@contoso.com .

    b. Na caixa de texto **da palavra-passe,** digite a palavra-passe do utilizador.

    c. Na caixa de texto **confirmar palavra-passe,** reentre na palavra-passe do utilizador.

    d. Na caixa de texto **'Nome Completo',** escreva o nome completo do utilizador como Britta Simon.

    e. Na caixa de texto **por e-mail,** digite o endereço de e-mail do utilizador como Brittasimon@contoso.com .

    f. Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no SSO kantega para azulejos de bambu no Painel de Acesso, deverá ser automaticamente inscrito no SSO kantega para bambu para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
