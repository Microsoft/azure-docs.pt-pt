---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com TextMagic Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o TextMagic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: f1db4f1ae40c72203cdd5d07c49b2f9c202ad525
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491364"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-textmagic"></a>Tutorial: Azure Ative Directory integração única (SSO) com TextMagic

Neste tutorial, você vai aprender a integrar TextMagic com Azure Ative Directory (Azure AD). Quando integrar o TextMagic com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao TextMagic.
* Ative os seus utilizadores a serem automaticamente inscritos no TextMagic com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por TextMagic (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* TextMagic suporta **IDP** iniciado SSO

* TextMagic suporta o fornecimento do utilizador **Just In Time**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-textmagic-from-the-gallery"></a>Adicionar TextMagic da galeria

Para configurar a integração do TextMagic no AD Azure, é necessário adicionar o TextMagic da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** digite **TextMagic** na caixa de pesquisa.
1. Selecione **TextMagic** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-textmagic"></a>Configurar e testar Azure AD único sinal para TextMagic

Configure e teste Azure AD SSO com TextMagic usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no TextMagic.

Para configurar e testar o Azure AD SSO com textMagic, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure textMagic SSO](#configure-textmagic-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create TextMagic test user](#create-textmagic-test-user)** - para ter uma contraparte de B.Simon em TextMagic que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **TextMagic,** encontre a secção **Gerir** e selecione **um único sinal de sing.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **identifier,** digite um URL:  `https://my.textmagic.com/saml/metadata`

5. A aplicação TextMagic espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem seguinte mostra a lista de atributos predefinidos, onde como **identificador** de nome é mapeado com **user.userprincipalname**. A aplicação TextMagic espera que **o identificador** de nomes seja mapeado com **o user.mail**, pelo que é necessário editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação TextMagic espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com o seu requisito.

    | Nome |   Atributo de origem| Espaço de Nomes  |
    | --------------- | --------------- | --------------- |
    | empresa | user.companyname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | nomePróprio               | user.givenname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | apelido            | utilizador.sobrenome |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | telefone               | user.telephonenumber |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **set up TextMagic,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao TextMagic.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **TextMagic**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="configure-textmagic-sso"></a>Configurar textoMagic SSO

1. Para automatizar a configuração dentro do TextMagic, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Setup TextMagic** irá direcioná-lo para a aplicação TextMagic. A partir daí, forneça as credenciais de administração para assinar no TextMagic. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-5.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o TextMagic manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa TextMagic como administrador e execute os seguintes passos:

4. Selecione **as definições de Conta** sob o nome de utilizador.

    ![A screenshot mostra as definições de conta selecionadas pelo utilizador.](./media/textmagic-tutorial/config1.png)

5. Clique no separador **Single Sign-On (SSO)** e preencha os seguintes campos:  

    ![A screenshot mostra o separador Single Sign-On onde pode introduzir os valores descritos.](./media/textmagic-tutorial/config2.png)

    a. No **ID da Entidade fornecedora de identidade:** caixa de texto, cole o valor do **Identificador AD Azure,** que copiou do portal Azure.

    b. No **Url SSO do fornecedor de identidade:** caixa de texto, cole o valor do URL de **Login,** que copiou do portal Azure.

    c. No **URL SLO provider de identidade:** caixa de texto, cole o valor do **URL logout,** que copiou do portal Azure.

    d. Abra o **certificado codificado base-64** no bloco de notas descarregado do portal Azure, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o para o **certificado Público x509:** caixa de texto.

    e. Clique em **Guardar**.

### <a name="create-textmagic-test-user"></a>Criar utilizador de teste TextMagic

A aplicação suporta **o fornecimento do utilizador a tempo** e após a autenticação os utilizadores serão criados automaticamente na aplicação. É necessário preencher as informações uma vez no primeiro login para ativar a sub-conta no sistema.
Não há nenhum item de ação para si nesta secção.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo TextMagic no Painel de Acesso, deverá ser automaticamente inscrito no TextMagic para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente TextMagic com Azure AD](https://aad.portal.azure.com/)

