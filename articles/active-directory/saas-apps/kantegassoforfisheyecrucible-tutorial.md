---
title: 'Tutorial: Integração do Azure Ative Directory com a Kantega SSO para o FishEye/Crucible | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Kantega SSO para o FishEye/Crucible.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 06a4e8aa1ad74f47526f3a39931632953bfaaec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459191"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-fisheyecrucible"></a>Tutorial: Integração do Azure Ative Directory com a Kantega SSO para FishEye/Crucible

Neste tutorial, você aprende a integrar Kantega SSO para FishEye/Crucible com Azure Ative Directory (Azure AD).
A integração do SSO da Kantega para o FishEye/Crucible com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Kantega SSO para FishEye/Crucible.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no SSO da Kantega para o FishEye/Crucible (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com a Kantega SSO para o FishEye/Crucible, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* SSO Kantega para subscrição ativada por FishEye/Crucible

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Kantega SSO para FishEye/Crucible suporta **SP e IDP** iniciado SSO

## <a name="adding-kantega-sso-for-fisheyecrucible-from-the-gallery"></a>Adicionar SSO Kantega para FishEye/Crucible da galeria

Para configurar a integração do Kantega SSO para FishEye/Crucible em AD Azure, você precisa adicionar Kantega SSO para FishEye/Crucible da galeria à sua lista de aplicações geridas saaS.

**Para adicionar Kantega SSO para FishEye/Crucible da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Kantega SSO para FishEye/Crucible**, selecione **Kantega SSO para FishEye/Crucible** a partir do painel de resultados e clique em **Adicionar** botão adicionar a aplicação.

    ![Kantega SSO para FishEye/Crucible na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Kantega SSO for FishEye/Crucible com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no SSO kantega para o FishEye/Crucible.

Para configurar e testar o único sinal de Ad AD com a Kantega SSO para o FishEye/Crucible, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o SSO Kantega para o FishEye/Crucible Single Sign-On](#configure-kantega-sso-for-fisheyecrucible-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Kantega SSO for FishEye/Crucible test user](#create-kantega-sso-for-fisheyecrucible-test-user)** - para ter uma contraparte de Britta Simon em Kantega SSO para FishEye/Crucible que está ligada à representação AD Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com Kantega SSO para FishEye/Crucible, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página Kantega SSO para integração de aplicações **FishEye/Crucible,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![Screenshot que mostra a secção "Configuração Básica S A M L" com a caixa de texto "Identifier" e "Answer U R L" realçada e o botão "Save" selecionado.](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![SSO Kantega para o domínio fisheye/crucible e informações únicas de sign-on](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL Sign-On. Estes valores são recebidos durante a configuração do plugin FishEye/Crucible, que é explicado mais tarde no tutorial.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

7. Na **secção Configurar o SSO Kantega para o FishEye/Crucible,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-kantega-sso-for-fisheyecrucible-single-sign-on"></a>Configure o SSO Kantega para o FishEye/Crucible Single Sign-On

1. Numa janela diferente do navegador web, inscreva-se no seu servidor FishEye/Crucible como administrador.

1. Hover on cog e clique nos **Add-ons**.

    ![Screenshot que mostra o ícone "Cog" e "Add-ons" selecionados.](./media/kantegassoforfisheyecrucible-tutorial/addon1.png)

1. Na secção Definições do Sistema, clique **em Localizar novos addons**. 

    ![Screenshot que mostra a secção "Definições do sistema" com "Find New Add-ons" selecionado.](./media/kantegassoforfisheyecrucible-tutorial/add-on2.png)

1. **Pesquise o SSO da Kantega para obter o Crucible** e clique no botão **Instalar** para instalar o novo plugin SAML.

    ![Screenshot que mostra a página "Attlasian Marketplace for FishEye" com "Kantega S S O for Crucible" na caixa de pesquisa e o botão "Instalar" selecionado.](./media/kantegassoforfisheyecrucible-tutorial/addon2.png)

1. A instalação plugin começa. 

    ![Screenshot que mostra o diálogo "Instalar" para o plugin.](./media/kantegassoforfisheyecrucible-tutorial/addon33.png)

1. Uma vez concluída a instalação. Clique em **Fechar**.

    ![Screenshot que mostra o diálogo "Instalado e pronto a ir" e o botão "Fechar" selecionado.](./media/kantegassoforfisheyecrucible-tutorial/addon34.png)

1.  Clique em **Gerir**.

    ![Screenshot que mostra a página de aplicação "Kantega S S O for Crucible S A M L & Kerberos" e o botão "Gerir" selecionado.](./media/kantegassoforfisheyecrucible-tutorial/addon35.png)

1. Clique **em Configurar** para configurar o novo plugin. 

    ![Screenshot que mostra a página "Add-ons instalado pelo utilizador" e o botão "Configurar" selecionado.](./media/kantegassoforfisheyecrucible-tutorial/addon3.png)

1. Na secção **SAML.** Selecione **Azure Ative Directory (Azure AD)** a partir do **dropdown do fornecedor** de identidade Add.

    ![Screenshot que mostra a página "Add-ons - Kantega Single Sign-on" com o drop-down "Add identity provider" e "Azure Ative Directory (Azure AD)" selecionado. ](./media/kantegassoforfisheyecrucible-tutorial/addon4.png)

1. Selecione o nível de subscrição como **Basic**.

    ![Screenshot que mostra a secção "Preparação Azure A D" com "Básico" selecionado.](./media/kantegassoforfisheyecrucible-tutorial/addon5.png)

1. Na secção propriedades da **App,** execute os seguintes passos:

    ![Screenshot que mostra a secção "Propriedades de aplicação" com a caixa de texto "App I D U R I" selecionada.](./media/kantegassoforfisheyecrucible-tutorial/addon6.png)

    a. Copie o valor **ID URI** da aplicação e use-o como **Identificador, URL de resposta e URL de Sign-On** na secção de **Configuração Básica SAML** no portal Azure.

    b. Clique em **Seguinte**.

1. Na secção de **importação de metadados,** execute os seguintes passos:

    ![Screenshot que mostra a secção "Importação de Metadados" com "Ficheiro metadados no meu computador" selecionado.](./media/kantegassoforfisheyecrucible-tutorial/addon7.png)

    a. Selecione **o ficheiro Metadados no meu computador** e carreja o ficheiro metadados, que descarregou a partir do portal Azure.

    b. Clique em **Seguinte**.

1. Na secção **nome e sSo,** execute os seguintes passos:

    ![Screenshot que mostra a localização "Nome e S O" com a caixa de texto "Nome do fornecedor de identidade" realçada e o botão "Seguinte" selecionado.](./media/kantegassoforfisheyecrucible-tutorial/addon8.png)

    a. Adicionar Nome do Fornecedor de Identidade na caixa de texto **do fornecedor de identidade** (por exemplo, Azure AD).

    b. Clique em **Seguinte**.

1. Verifique o certificado de assinatura e clique em **Seguinte**.   

    ![Screenshot que mostra a informação da secção "Verificação de assinatura" e o botão "Seguinte" selecionado.](./media/kantegassoforfisheyecrucible-tutorial/addon9.png)

1. Na secção **de contas de utilizador do FishEye,** execute os seguintes passos:

    ![Screenshot que mostra a secção "Contas de utilizador FishEye" com a opção "Criar utilizadores no Diretório Interno do FishEye, se necessário" e o botão "Seguinte" selecionado.](./media/kantegassoforfisheyecrucible-tutorial/addon10.png)

    a. Selecione **Criar utilizadores no Diretório interno do FishEye se necessário** e introduzir o nome apropriado do grupo para utilizadores (pode ser um número múltiplo. de grupos separados por vírgula).

    b. Clique em **Seguinte**.

1. Clique em **Concluir**.

    ![Screenshot que mostra a secção "Resumo" com o botão "Finish" selecionado.](./media/kantegassoforfisheyecrucible-tutorial/addon11.png)

1. Nos **domínios conhecidos para a secção AD Azure,** execute os seguintes passos:  

    ![Screenshot que mostra os domínios "Domínios conhecidos para Azure A D" com o botão "Guardar" selecionado.](./media/kantegassoforfisheyecrucible-tutorial/addon12.png)

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
  
    b. No tipo de campo **do nome do utilizador** `brittasimon@yourcompanydomain.extension` . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a Kantega SSO para FishEye/Crucible.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Kantega SSO para FishEye/Crucible**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Kantega SSO para FishEye/Crucible**.

    ![O SSO Kantega para FishEye/Crucible na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-kantega-sso-for-fisheyecrucible-test-user"></a>Criar O SSO Kantega para o utilizador de teste FishEye/Crucible

Para permitir que os utilizadores de Azure AD inscrevam-se no FishEye/Crucible, devem ser a provisionados no FishEye/Crucible. No SSO kantega para FishEye/Crucible, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu servidor Crucible no local como administrador.

1. Passe na engrenagem e clique nos **Utilizadores.**

    ![Screenshot que mostra o ícone "Cog" selecionado e "Utilizadores" selecionados a partir do drop-down.](./media/kantegassoforfisheyecrucible-tutorial/user1.png)

1. Na secção de separadores **do utilizador,** clique **em Adicionar utilizador**.

    ![Screenshot que mostra a secção "Utilizadores" com o botão "Adicionar utilizador" selecionado.](./media/kantegassoforfisheyecrucible-tutorial/user2.png)

1. Na página de diálogo **adicionar novo utilizador,** execute os seguintes passos:

    ![Adicionar Empregado](./media/kantegassoforfisheyecrucible-tutorial/user3.png)

    a. Na caixa de texto **username,** escreva o e-mail do utilizador como Brittasimon@contoso.com .

    b. Na caixa de texto **'Display Name',** escreva o nome do utilizador como Britta Simon.

    c. Na caixa de texto **do endereço de e-mail,** digite o endereço de e-mail do utilizador como Brittasimon@contoso.com .

    d. Na caixa de texto **da palavra-passe,** digite a palavra-passe do utilizador.

    e. Na caixa de texto **confirmar palavra-passe,** reentre na palavra-passe do utilizador.

    f. Clique em **Adicionar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no SSO Kantega para o FishEye/Azulejo Crucible no Painel de Acesso, deverá ser automaticamente inscrito no SSO kantega para o FishEye/Crucible para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)