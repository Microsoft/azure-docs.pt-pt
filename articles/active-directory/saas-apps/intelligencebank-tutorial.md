---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com o IntelligenceBank | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o IntelligenceBank.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2020
ms.author: jeedes
ms.openlocfilehash: 9887c435c2aa8dc7ba8cab9481cf30195c4b334e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92459939"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-intelligencebank"></a>Tutorial: Azure Ative Directory integração única (SSO) com o IntelligenceBank

Neste tutorial, você vai aprender a integrar o IntelligenceBank com o Azure Ative Directory (Azure AD). Quando integrar o IntelligenceBank com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao IntelligenceBank.
* Capacitar os seus utilizadores a serem automaticamente inscritos no IntelligenceBank com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada pelo IntelligenceBank (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* IntelligenceBank suporta SSO iniciado **pela SP**

* Assim que configurar o IntelligenceBank, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-intelligencebank-from-the-gallery"></a>Adicionar IntelligenceBank da galeria

Para configurar a integração do IntelligenceBank no AD Azure, precisa adicionar o IntelligenceBank da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **de galeria,** **digite IntelligenceBank** na caixa de pesquisa.
1. Selecione **IntelligenceBank** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-intelligencebank"></a>Configurar e testar Azure AD único sign-on para IntelligenceBank

Configure e teste Azure AD SSO com o IntelligenceBank usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no IntelligenceBank.

Para configurar e testar o Azure AD SSO com o IntelligenceBank, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure IntelligenceBank SSO](#configure-intelligencebank-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create IntelligenceBank test user](#create-intelligencebank-test-user)** - para ter uma contraparte de B.Simon no IntelligenceBank que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações do **IntelligenceBank,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.intelligencebank.com`

    b. Na caixa de texto **identifier (Entity ID),** utilize qualquer pessoa do seguinte:

    - `IB`
    - `IntelligenceBank`
    - `https://<SUBDOMAIN>.intelligencebank.com`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.intelligencebank.com/auth`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real em URL, Identifier e URL de resposta. Contacte [a equipa de suporte do Cliente IntelligenceBank](mailto:helpdesk@intelligencebank.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção Configurar o **IntelligenceBank,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao IntelligenceBank.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **IntelligenceBank**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-intelligencebank-sso"></a>Configurar IntelligenceBank SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa IntelligenceBank como administrador.

1. Clique no **Autenticador** e clique em **Adicionar Novo**

    ![A screenshot mostra o separador Administrador selecionado e o ícone Add New.](./media/intelligencebank-tutorial/authenticator.PNG)

1. Efetue os seguintes passos:

    ![A screenshot mostra os campos onde introduz a informação neste passo.](./media/intelligencebank-tutorial/urls.PNG)

    a. Na caixa de texto **'Nome',** insira o nome por exemplo como `azureadsso` .

    b. Na caixa de texto **Descrição,** introduza a descrição válida.

    c. Selecione **SAML** a partir do dropdown como **o Tipo**.

    d. Na caixa de texto **do Url Remoto,** cole o valor URL do **Login,** que copiou a partir do portal Azure.

    e. Na caixa de texto **do Anfitrião,** cole o valor **de ID** da Entidade, que copiou a partir do portal Azure.

    f. Abra o Certificado descarregado **(Base64)** do portal Azure para o Bloco de Notas e cole o conteúdo na caixa de texto **CertData**

    exemplo, Na caixa de texto **SingleLogoutService,** cole o valor **URL de Log out,** que copiou a partir do portal Azure.

    h. Clique no botão **Guardar.**

### <a name="create-intelligencebank-test-user"></a>Criar utilizador de teste IntelligenceBank

1. Numa janela diferente do navegador web, inscreva-se no site da empresa IntelligenceBank como administrador.

1. Vá aos **Utilizadores de**  ->  **Administração** e selecione **Adicionar Novo Ícone do Utilizador** para adicionar o **Utilizador**.

    ![A screenshot mostra o ícone do Utilizadores selecionado no separador Utilizadores.](./media/intelligencebank-tutorial/creating-user.PNG)

1. Preencha os campos necessários de acordo com os requisitos da sua organização e clique em **Guardar**.

    ![A screenshot mostra a página 'Adicionar Novo Utilizador' onde introduz as informações do utilizador.](./media/intelligencebank-tutorial/creating-user-1.PNG)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do IntelligenceBank no Painel de Acesso, deverá ser automaticamente inscrito no IntelligenceBank para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o IntelligenceBank com a Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o IntelligenceBank com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)