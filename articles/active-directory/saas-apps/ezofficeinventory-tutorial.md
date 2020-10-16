---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com EZOfficeInventory Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o EZOfficeInventory.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.openlocfilehash: ff6ba71f4606b3caf51007a9d75f3ca36bc99cda
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826480"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ezofficeinventory"></a>Tutorial: Azure Ative Directory integração única (SSO) com EZOfficeInventory

Neste tutorial, você vai aprender a integrar eZOfficeInventory com Azure Ative Directory (Azure AD). Quando integrar o EZOfficeInventory com Azure AD, pode:

* Control em Azure AD que tem acesso ao EZOfficeInventory.
* Capacitar os seus utilizadores a serem automaticamente inscritos no EZOfficeInventory com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* EZOfficeInventory única assinatura ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* EZOfficeInventory suporta **SP** iniciado SSO
* EZOfficeInventory suporta provisão de utilizadores **just in time**
* Uma vez configurado EZOfficeInventory pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-ezofficeinventory-from-the-gallery"></a>Adicionar EZOfficeInventory da galeria

Para configurar a integração do EZOfficeInventory em AZure AD, é necessário adicionar o EZOfficeInventory da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite EZOfficeInventory** na caixa de pesquisa.
1. Selecione **EZOfficeInventory** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ezofficeinventory"></a>Configurar e testar Azure AD único sinal para ezOfficeInventory

Configure e teste Azure AD SSO com EZOfficeInventory usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no EZOfficeInventory.

Para configurar e testar o Azure AD SSO com ezOfficeInventory, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure EZOfficeInventory SSO](#configure-ezofficeinventory-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create EZOfficeInventory test user](#create-ezofficeinventory-test-user)** - para ter uma contraparte de B.Simon em EZOfficeInventory que está ligada à representação AD Ad Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **EZOfficeInventory,** encontre a secção **Gerir** e selecione **um único sinal de sing**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<SUBDOMAIN>.ezofficeinventory.com/users/sign_in`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte [a equipa de suporte ao cliente da EZOfficeInventory](mailto:support@ezofficeinventory.com) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação EZOfficeInventory espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação EZOfficeInventory espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com o seu requisito.

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | first_name | user.givenname |
    | last_name | utilizador.sobrenome |
    | e-mail | user.mail |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configuração EZOfficeInventory,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao EZOfficeInventory.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **EZOfficeInventory**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-ezofficeinventory-sso"></a>Configurar EzOfficeInventory SSO

1. Para automatizar a configuração dentro do EZOfficeInventory, é necessário instalar a **extensão do navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar O EZOfficeInventory** irá direcioná-lo para a aplicação EZOfficeInventory. A partir daí, forneça as credenciais de administração para assinar na EZOfficeInventory. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-5.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar manualmente o EZOfficeInventory, abra uma nova janela do navegador web e inscreva-se no site da empresa EZOfficeInventory como administrador e execute os seguintes passos:

1. No canto superior direito da página, clique no **Perfil** e, em seguida, navegue para **Configurações**  >  **Adicionar Ons**.

    ![Screenshot que mostra a página "Definições" com a ação "Adicionar Ons" selecionada.](./media/ezofficeinventory-tutorial/configure01.png)

1. Desloque-se até à secção **de Integração SAML,** execute os seguintes passos:

    ![Configuração EZOfficeInventory](./media/ezofficeinventory-tutorial/configure02.png)

    a. Verifique a opção **Ativada.**

    b. Na caixa de texto **URL do Fornecedor de Identidade,** cole o valor URL de **login,** que copiou a partir do portal Azure.

    c. Abra o certificado codificado Base64 no bloco de notas, copie o seu conteúdo e cole-o na caixa de texto **do Certificado de Fornecedor de Identidade.**

    d. Na caixa **de texto do botão de início** de sessão, introduza o texto do botão de login.

    e. Na caixa de texto **do primeiro nome,** **introduza first_name**.

    f. Na caixa de texto **do último nome,** **introduza last_name**.

    exemplo, Na caixa de texto **de e-mail,** insira **o e-mail.**

    h. Selecione a sua função de acordo com o seu requisito a partir da opção **EZOfficeInventory Por padrão.**

    i. Clique em **Atualizar**.

### <a name="create-ezofficeinventory-test-user"></a>Criar utilizador de teste EZOfficeInventory

Nesta secção, um utilizador chamado Britta Simon é criado no EZOfficeInventory. O EZOfficeInventory suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no EZOfficeInventory, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo EZOfficeInventory no Painel de Acesso, deverá ser automaticamente inscrito no EZOfficeInventory para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Experimente ezOfficeInventory com Azure AD](https://aad.portal.azure.com/)