---
title: 'Tutorial: Integração do Azure Ative Directory com Absorb LMS Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o Absorb LMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2019
ms.author: jeedes
ms.openlocfilehash: 31b0a4fd8d97371eb4e127a2a473893a0487f220
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92319080"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Tutorial: Integração do Diretório Ativo Azure com Absorver LMS

Neste tutorial, aprende-se a integrar o Absorb LMS com o Azure Ative Directory (Azure AD).
A integração do Absorb LMS com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Absorb LMS.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos para absorver LMS (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD Azure com Absorver LMS, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Absorva a assinatura única ativada lMS

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Absorver LMS suporta **IDP** iniciado SSO

## <a name="adding-absorb-lms-from-the-gallery"></a>Adicionar Absorva LMS da galeria

Para configurar a integração do Absorb LMS em AD Azure, é necessário adicionar O Absorvão LMS da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Absorb LMS da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Absorva LMS,** selecione **Absorva LMS** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![Absorva LMS na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sinal de absorção LMS com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Absorb LMS.

Para configurar e testar o único sinal de Ad AD com Absorb LMS, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure Absorva lMS Single Sign-On](#configure-absorb-lms-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Absorb LMS test user](#create-absorb-lms-test-user)** - para ter uma contraparte de Britta Simon em Absorb LMS que está ligada à representação AD AZure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com um único sinal de absorção de LMS, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Absorva LMS,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na **configuração single Sign-On com página SAML,** clique no botão **Editar** para abrir o diálogo **básico de configuração SAML.**

    ![Absorva informações únicas de súmis de LMS e URLs](common/idp-intiated.png)

    Se estiver a utilizar **Absorva 5 - UI** utilize a seguinte configuração:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://company.myabsorb.com/account/saml`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://company.myabsorb.com/account/saml`

    Se estiver a utilizar **o Absorb 5 - New Learner Experience,** utilize a seguinte configuração:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://company.myabsorb.com/api/rest/v2/authentication/saml`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://company.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte [a equipa de suporte do Cliente absorver LMS](https://support.absorblms.com/hc/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. A imagem seguinte mostra a lista de atributos predefinidos, onde como **identificador** de nome é mapeado com **user.userprincipalname**.

    ![image](common/edit-attribute.png)

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

7. Na secção **Configurar absorver LMS,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-absorb-lms-single-sign-on"></a>Configure absorver LMS single Sign-On

1. Numa nova janela do navegador web, inscreva-se no site da empresa Absorção LMS como administrador.

2. Selecione o botão **Conta** no topo direito.

    ![O botão Conta](./media/absorblms-tutorial/1.png)

3. No painel 'Conta', selecione **Definições do Portal**.

    ![A ligação de Definições do Portal](./media/absorblms-tutorial/2.png)

4. Selecione o **separador 'Gestão de Definições SSO'.**

    ![O separador Utilizadores](./media/absorblms-tutorial/managesso.png)

5. Na página **'Gerir as Definições Sign-On' Únicas,** faça o seguinte:

    ![A única página de configuração de sign-on](./media/absorblms-tutorial/settings.png)

    a. Na caixa de texto **Name,** insira o nome como Azure AD Marketplace SSO.

    b. Selecione **SAML** como **método**.

    c. No Bloco de Notas, abra o certificado que descarregou a partir do portal Azure. Remova as **etiquetas ---BEGIN Certificate---** e **---END Certificate---.** Em seguida, na caixa **chave,** cole o conteúdo restante.

    d. Na caixa **Modo,** selecione **Identity Provider Iniciado**.

    e. Na caixa **Id Property,** selecione o atributo que configura como o identificador do utilizador em Azure AD. Por exemplo, se *o identificador de nomes* for selecionado em Azure AD, selecione **o nome de utilizador**.

    f. Selecione **Sha256** como **tipo de assinatura**.

    exemplo, Na caixa **URL de login,** cole o URL de Acesso ao **Utilizador** a partir da página **Propriedades** da aplicação do portal Azure.

    h. No **URL logo,** cole o valor **URL de inscrição** que copiou a partir da janela **de inscrição** de configuração do portal Azure.

    i. Toggle **Redirecionar automaticamente** para **on**.

6. **Selecione Save.**

    ![O único permite que o SSO Login altere](./media/absorblms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador**`brittasimon\@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon utilize a Azure single sign-on, concedendo acesso a Absorb LMS.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Absorva LMS**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, escreva e selecione **Absorb LMS**.

    ![O link Absorver LMS na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-absorb-lms-test-user"></a>Criar absorver o utilizador de teste LMS

Para que os utilizadores de Azure AD inscrevam-se para absorver LMS, devem ser configurados em Absorver LMS. No caso do Absorver LMS, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento do utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa Absorva LMS como administrador.

2. No painel **de Utilizadores,** selecione **Utilizadores**.

    ![A ligação dos Utilizadores](./media/absorblms-tutorial/absorblms_userssub.png)

3. Selecione **o separador Utilizador.**

    ![A lista de lançamentos adicionais](./media/absorblms-tutorial/absorblms_createuser.png)

4. Na página **'Adicionar Utilizador',** faça o seguinte:

    ![A página do Utilizador adicionar](./media/absorblms-tutorial/user.png)

    a. Na caixa **First Name,** escreva o primeiro nome, como **Britta.**

    b. Na caixa Do **Último Nome,** escreva o apelido, tal como **Simão.**

    c. Na caixa **username,** escreva um nome completo, como **Britta Simon**.

    d. Na **caixa palavra-passe,** escreva a palavra-passe do utilizador.

    e. Na caixa **'Confirmar palavra-passe',** retipe a palavra-passe.

    f. Desa esta medida **é ativa** para **ative**.

5. **Selecione Save.**

    ![O único permite que o SSO Login altere](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > Por predefinição, o Provisionamento do Utilizador não está ativado em SSO. Se o cliente quiser ativar esta funcionalidade, tem de a configurar como mencionado [nesta](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) documentação. Note também que a Provisioing do Utilizador só está disponível no **Absorb 5 - New Learner Experience** com URL ACS-`https://company.myabsorb.com/api/rest/v2/authentication/saml`

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Absorver LMS no Painel de Acesso, deverá ser automaticamente inscrito no Absorver LMS para o qual configura SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)