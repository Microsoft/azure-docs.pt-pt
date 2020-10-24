---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com o WEDO Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o WEDO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2020
ms.author: jeedes
ms.openlocfilehash: 529c4e6433d16f9d70530ba516b5ec1426806984
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519236"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wedo"></a>Tutorial: Azure Ative Directory integração única (SSO) com o WEDO

Neste tutorial, você vai aprender a integrar o WEDO com o Azure Ative Directory (Azure AD). Quando integra o WEDO com AZure AD, pode:

* Controlo em Azure AD que tem acesso ao WEDO.
* Permita que os seus utilizadores sejam automaticamente inscritos no WEDO com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura única ativada wedo (SSO). Entre em contato com [a equipa de suporte do cliente WEDO](mailto:info@wedo.swiss) para obter uma subscrição SSO.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* WEDO apoia **SP e IDP** iniciado SSO

* [Uma vez configurado o WEDO, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-wedo-from-the-gallery"></a>Adicionando WEDO da galeria

Para configurar a integração do WEDO no AZure AD, é necessário adicionar o WEDO da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva **WEDO** na caixa de pesquisa.
1. Selecione **o WEDO** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-wedo"></a>Configurar e testar Azure AD único sinal para WEDO

Configure e teste Azure AD SSO com WEDO usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no WEDO.

Para configurar e testar o Azure AD SSO com o WEDO, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o WEDO SSO](#configure-wedo-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create WEDO test user](#create-wedo-test-user)** - para ter uma contraparte de B.Simon no WEDO que está ligada à representação AD AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **WEDO,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.wedo.swiss/sp/acs`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.wedo.swiss/sp/acs`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<SUBDOMAIN>.wedo.swiss/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de suporte do cliente WEDO](mailto:info@wedo.swiss) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação WEDO espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    | Nome | Atributo de origem|
    | ------------ | --------- |
    | e-mail | user.email |
    | nomePróprio | user.firstName |
    | apelido | user.lasttName |
    | userName | user.userName |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configuração WEDO,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao WEDO.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **WEDO**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-wedo-sso"></a>Configurar WEDO SSO

Siga estes passos para ativar o Azure AD SSO no WEDO.

1. Faça login NO [WEDO](https://login.wedo.swiss/). É preciso ter funções de **administrador.**
1. Nas definições de Perfil, selecione a **autenticação** do menu nas **definições de rede**de secção .
1. Na página **de autenticação SAML,** execute os seguintes passos:

   ![Ligação de autenticação SAML](media/wedo-tutorial/network-security-authentification.png)

   a. Ativar **a autenticação SAML**.

   b. Selecione o **separador Metadados do Fornecedor de Identidade (XML).**

   c. Abra o **metdata XML** da Federação descarregada do portal Azure para o Bloco de Notas e copie o conteúdo dos metadados XML e cole-o na caixa de texto **do Certificado X.509.**

   d. Clique em **Guardar**

### <a name="create-wedo-test-user"></a>Criar utilizador de teste WEDO

Nesta secção, irá criar um utilizador de teste no WEDO chamado Bob Simon. As informações devem corresponder a criar um utilizador de *teste AD Azure*.

1. A partir da definição de perfil no WEDO, **selecione Utilizadores** a partir da secção *de definições* de Rede.
1. Clique **em Adicionar utilizador**.
1. No popup do utilizador Adicionar, preencha as informações do utilizador

    a. Primeiro `B` nome.

    b. Sobrenome. `Simon`

    c. Insira o `username@companydomain.extension` e-mail. Por exemplo, `B.Simon@contoso.com`. É obrigatório ter e-mail com o mesmo domínio que o nome curto da sua empresa.

    d. Tipo de utilizador `User` .

    e. Clique **em Criar utilizador.**

    f. Na página *'Selecionar equipas',* clique em **Guardar**.

    exemplo,  Na página do *utilizador convidar,* clique em **Sim**.

1. Validar o utilizador utilizando o link que recebeu por e-mail

> [!NOTE]
> Se pretender criar um utilizador falso (o e-mail acima não existe na sua rede), contacte o [nosso suporte](mailto:info@wedo.swiss) para validar o utilizador*.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo WEDO no Painel de Acesso, deverá ser automaticamente inscrito no WEDO para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o WEDO com a Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o WEDO com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)