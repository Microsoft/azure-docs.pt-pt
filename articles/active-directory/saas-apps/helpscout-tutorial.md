---
title: 'Tutorial: Integração do Diretório Ativo Azure com Help Scout [ Scout] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Help Scout.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b71ccbc6cfdb9d3d37fc46b0e932fa98eee2fb43
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159101"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Tutorial: Integração do Diretório Ativo Azure com Escuteiro de Ajuda

Neste tutorial, aprende-se a integrar o Help Scout com o Azure Ative Directory (Azure AD).
Integrar o Scout de Ajuda com a AD Azure proporciona-lhe os seguintes benefícios:

* Podes controlar em Azure AD quem tem acesso a Help Scout.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos para ajudar o Scout (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Help Scout, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Ajuda a escotar única subscrição ativada por sinal

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Ajuda Scout apoia **SP e IDP** iniciado SSO
* Ajuda Scout suporta aprovisionamento de utilizadores **justo no tempo**

## <a name="adding-help-scout-from-the-gallery"></a>Adicionar ajuda scout da galeria

Para configurar a integração do Help Scout no Azure AD, você precisa adicionar Help Scout da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite **Help Scout** na caixa de pesquisa.
1. Selecione **Help Scout** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure com o Help Scout com base num utilizador de teste chamado **B.Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Help Scout.

Para configurar e testar o único sinal de Azure AD com o Help Scout, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o SSO](#configure-help-scout-sso)** de Ajuda - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Create Help Scout test user](#create-help-scout-test-user)** - para ter uma contrapartida de B.Simon em Help Scout que está ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure com o Help Scout, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **help scout,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

1. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

1. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** Se desejar configurar a aplicação no modo iniciado **idp,** execute os seguintes passos:

    ![Ajude o Scout Domain e urLs informações únicas de inscrição](common/idp-intiated.png)

    a. **Identificador** é o PÚBLICO URI (Id da Entidade fornecedora de **serviços)** da Help Scout, começa com`urn:`

    b. **O URL de resposta** é o **URL pós-back (URL** do Serviço de Consumidor de Afirmação) do Help Scout, começa com`https://` 

    > [!NOTE]
    > Os valores nestes URLs são apenas para demonstração. É necessário atualizar estes valores a partir de URL e Identificador de Resposta real. Obtém estes valores a partir do separador **Sign-On único** na secção autenticação, o que é explicado mais tarde no tutorial.

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    ![Ajude o Scout Domain e urLs informações únicas de inscrição](common/metadata-upload-additional-signon.png)

    Na caixa de texto **salmôl Sign-on URL,** escreva um URL como:`https://secure.helpscout.net/members/login/`

1. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **De Seleção de Ajuda, copie** os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado B.Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **B.Simon**.
  
    b. No **campo** de nome utilizador tipo **B.Simon\@yourcompanydomain.extension**  
    Por exemplo, B.Simon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que b.Simon use o único sign-on Azure, concedendo acesso ao Help Scout.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Help Scout**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Help Scout**.

    ![O link help scout na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **B.Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

## <a name="configure-help-scout-sso"></a>Configure Ajude a Escuteiro SSO

1. Para automatizar a configuração dentro do Help Scout, precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar O Scout de Ajuda** irá direcioná-lo para a aplicação Help Scout. A partir daí, forneça as credenciais de administração para assinar no Help Scout. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração de configuração](common/setup-sso.png)

1. Se quiser configurar o Help Scout manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Help Scout como administrador e execute os seguintes passos:

1. Clique em **Gerir** a partir do menu superior e, em seguida, selecione **Empresa** a partir do menu dropdown.

    ![Configurar um único sinal](./media/helpscout-tutorial/settings1.png)

1. **Selecione Autenticação** a partir do painel de navegação esquerdo.

    ![Configurar um único sinal](./media/helpscout-tutorial/settings2.png)

1. Isto leva-o à secção de definições SAML e executa os seguintes passos:

    ![Configurar um único sinal](./media/helpscout-tutorial/settings3.png)

    a. Copie o valor do **URL pós-back (Url do Serviço de Consumidor de Afirmação)** e cole o valor na caixa de texto URL de **resposta** na secção **de configuração Básica sAML** no portal Azure.

    b. Copie o valor do Público URI (Id da **Entidade fornecedora de serviços)** e cole o valor na caixa de texto **identificador** na secção **de Configuração Básica SAML** no portal Azure.

1. **Toggle Enable SAML** e execute os seguintes passos:

    ![Configurar um único sinal](./media/helpscout-tutorial/settings4.png)

    a. Na caixa de texto **single Sign-On URL,** colá o valor do URL de **Login,** que copiou do portal Azure.

    b. Clique no **Certificado de Upload** para fazer o upload do Certificado **(Base64)** descarregado do portal Azure.

    c. Insira o domínio de e-mail da `contoso.com` sua organização e.x.- na caixa de texto de Domínios de **e-mail.** Pode separar vários domínios com uma vírina. Sempre que um Utilizador ou Administrador de Escuteiros de Ajuda que entre nesse domínio específico na [página de login](https://secure.helpscout.net/members/login/) do Help Scout será encaminhado para o Fornecedor de Identidade para autenticar com as suas credenciais.

    d. Por último, pode alternar o **Sinal SAML da Força** se pretender que os Utilizadores apenas entrem para ajudar a Scout através deste método. Se ainda quiser deixar a opção para eles assinarem com as suas credenciais de escuteiro, pode deixá-la toggled fora. Mesmo que isso esteja ativado, um Proprietário de Conta será sempre capaz de iniciar sessão para ajudar a Scout com a sua senha de conta.

    e. Clique em **Guardar**.

### <a name="create-help-scout-test-user"></a>Criar o utilizador de teste de ajuda scout

Nesta secção, um utilizador chamado B.Simon é criado em Help Scout. Ajuda o Scout suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Help Scout, um novo é criado após a autenticação.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo help scout no Painel de Acesso, deve ser automaticamente inscrito no Help Scout para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Tente ajudar o scout com a AD Azure](https://aad.portal.azure.com/)