---
title: 'Tutorial: Integração do Diretório Ativo Azure com help scout | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Help Scout.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: 7c5e8210bc8b805d72149fd2ef3335c1d637a58f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92445259"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Tutorial: Integração do Diretório Ativo Azure com Help Scout

Neste tutorial, aprende-se a integrar o Help Scout com o Azure Ative Directory (Azure AD).
Integrar o Help Scout com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Help Scout.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos para Ajudar o Scout (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o Help Scout, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Ajuda a escutar uma única subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Help Scout apoia **SP e IDP** iniciado SSO
* Help Scout suporta **provisão do** utilizador just in time

## <a name="adding-help-scout-from-the-gallery"></a>Adicionando ajuda scout da galeria

Para configurar a integração do Help Scout no Azure AD, precisa de adicionar o Help Scout da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **escreva Help Scout** na caixa de pesquisa.
1. Selecione **Help Scout** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Help Scout com base em um utilizador de teste chamado **B.Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Help Scout.

Para configurar e testar o Azure AD com o Help Scout, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Help Scout SSO](#configure-help-scout-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Help Scout test user](#create-help-scout-test-user)** - para ter uma contraparte de B.Simon em Help Scout que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o Help Scout, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **help scout,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

1. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

1. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![A screenshot mostra a Configuração BÁSICA SAML, onde pode introduzir o Identificador, Responder U R L e selecionar Guardar.](common/idp-intiated.png)

    a. **Identifier** é o **Audience URI (Service Provider Entity ID)** de Help Scout, começa com `urn:`

    b. **URL de resposta** é o **URL pós-back (URL de serviço ao consumidor de afirmação)** de Help Scout, começa com `https://` 

    > [!NOTE]
    > Os valores nestes URLs são apenas para demonstração. É necessário atualizar estes valores a partir de URL de resposta real e identificador. Obtém estes valores a partir do **separador 'Sign-On' único** na secção de Autenticação, que é explicado mais tarde no tutorial.

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![Screenshot mostra Definir U R Ls adicionais onde pode introduzir um sinal em U R L.](common/metadata-upload-additional-signon.png)

    Na caixa de texto **url de entrada de inscrição,** digite um URL como: `https://secure.helpscout.net/members/login/`

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **De Escoteiro de Ajuda** configurar, copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado B.Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **B.Simon**.
  
    b. No campo **de nome de utilizador** **B.Simon \@ a sua empresa.extensão.**  
    Por exemplo, B.Simon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que B.Simon use a Azure single sign-on, concedendo acesso a Help Scout.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **Help Scout**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Help Scout**.

    ![O link Help Scout na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No **diálogo de Utilizadores e grupos** selecione **B.Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

## <a name="configure-help-scout-sso"></a>Configurar ajuda Ajuda Scout SSO

1. Para automatizar a configuração dentro do Help Scout, é necessário instalar a extensão do **navegador 'As aplicações' Secure's,** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar O Scout de Ajuda** irá direcioná-lo para a aplicação Help Scout. A partir daí, forneça as credenciais de administração para assinar em Help Scout. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar o Help Scout manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Help Scout como administrador e execute os seguintes passos:

1. Clique em **Gerir** a partir do menu superior e, em seguida, selecione **Empresa** a partir do menu suspenso.

    ![A screenshot mostra o menu Gerir com a Empresa selecionada.](./media/helpscout-tutorial/settings1.png)

1. Selecione **autenticação** a partir do painel de navegação esquerdo.

    ![A screenshot mostra a autenticação selecionada.](./media/helpscout-tutorial/settings2.png)

1. Isto leva-o à secção de definições DE SAML e executa os seguintes passos:

    ![A screenshot mostra o separador Single Sign-On onde introduz as informações especificadas.](./media/helpscout-tutorial/settings3.png)

    a. Copie o valor **URL pós-back (URL do serviço de apoio à afirmação)** e cole o valor na caixa de texto **URL de resposta** na secção **configuração de SAML básico** no portal Azure.

    b. Copie o valor **URI do Público (ID da Entidade fornecedora de serviços)** e cole o valor na caixa de texto **identifier** na secção **configuração de SAML básico** no portal Azure.

1. Toggle **Ativar o SAML** e executar os seguintes passos:

    ![A screenshot mostra o separador Single Sign-On onde ativa a SAML e adiciona outras informações.](./media/helpscout-tutorial/settings4.png)

    a. Na caixa de texto **de URL Sign-On única,** cole o valor do URL de **login,** que copiou do portal Azure.

    b. Clique **em 'Upload Certificate'** para fazer o upload do **Certificado (Base64)** descarregado a partir do portal Azure.

    c. Insira o (s) domínio(s) e.x.x da sua organização na caixa de `contoso.com` texto **de Domínios de E-mail.** Pode separar vários domínios com uma vírgula. Sempre que um Utilizador ou Administrador de Help Scout que insira esse domínio específico na [página de login](https://secure.helpscout.net/members/login/) do Help Scout será encaminhado para Fornecedor de Identidade para autenticar com as suas credenciais.

    d. Por último, pode alternar **o Sign-on Force SAML** se quiser que os Utilizadores apenas iniciem sessão para Ajudar o Scout através deste método. Se ainda quiser deixar a opção de assinar com as suas credenciais de escuteiro, pode deixá-la desligada. Mesmo que isto esteja ativado, um Proprietário de Conta poderá sempre iniciar sessão para ajudar o Scout com a sua senha de conta.

    e. Clique em **Guardar**.

### <a name="create-help-scout-test-user"></a>Criar utilizador de teste de escuteiro de ajuda

Nesta secção, um utilizador chamado B.Simon é criado em Help Scout. O Help Scout suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Help Scout, um novo é criado após a autenticação.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo help scout no Painel de Acesso, deverá ser automaticamente inscrito no Help Scout para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)

- [Tente ajudar o escuteiro com Azure AD](https://aad.portal.azure.com/)