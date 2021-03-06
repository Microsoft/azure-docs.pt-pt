---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Fluxx Labs | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Fluxx Labs.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2020
ms.author: jeedes
ms.openlocfilehash: d201f80aea2c22e1bee06c776d2a84fb69f2bd7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92452605"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fluxx-labs"></a>Tutorial: Azure Ative Directory integração única (SSO) com Fluxx Labs

Neste tutorial, você vai aprender a integrar fluxx Labs com Azure Ative Directory (Azure AD). Quando integrar o Fluxx Labs com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Fluxx Labs.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Fluxx Labs com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada pela Fluxx Labs (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Fluxx Labs apoia **IDP** iniciado SSO
* Uma vez configurado o Fluxx Labs, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-fluxx-labs-from-the-gallery"></a>Adicionar Fluxx Labs da galeria

Para configurar a integração do Fluxx Labs no Azure AD, é necessário adicionar o Fluxx Labs da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **de galeria,** **digite Fluxx Labs** na caixa de pesquisa.
1. Selecione **Fluxx Labs** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-fluxx-labs"></a>Configurar e testar Azure AD único sign-on para Fluxx Labs

Configure e teste Azure AD SSO com Fluxx Labs usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Fluxx Labs.

Para configurar e testar o Azure AD SSO com fluxx Labs, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Fluxx Labs SSO](#configure-fluxx-labs-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Fluxx Labs test user](#create-fluxx-labs-test-user)** - para ter uma contraparte de B.Simon in Fluxx Labs que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **fluxx Labs,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na **configuração single Sign-On com página SAML,** execute os seguintes passos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:

    | Ambiente | Padrão url|
    |-------------|------------|
    | Produção | `https://<subdomain>.fluxx.io` |
    | Pré-produção | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    | Ambiente | Padrão url|
    |-------------|------------|
    | Produção | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Pré-produção | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte [a equipa de suporte do cliente da Fluxx Labs](https://fluxx.zendesk.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção Configurar o **Fluxx Labs,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Fluxx Labs.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Fluxx Labs**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-fluxx-labs-sso"></a>Configure Fluxx Labs SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Fluxx Labs como administrador.

2. Selecione **Administração** abaixo da secção **Definições.**

    ![Screenshot que mostra a secção "Definições" com "Administrador" selecionado.](./media/fluxxlabs-tutorial/config1.png)

3. No Painel De Administração, selecione **integrações de plug-ins** e, em  >   seguida, selecione **SSO-(Desativado)**

    ![Screenshot que mostra o separador "Integrações" com "S A M L S S S O- (Desativado) selecionado.](./media/fluxxlabs-tutorial/config2.png)

4. Na secção de atributos, execute os seguintes passos:

    ![Screenshot que mostra a secção "Atributos" com "S A M L S S O" verificado, valores introduzidos nos campos e o botão "Guardar" selecionado.](./media/fluxxlabs-tutorial/config3.png)

    a. Selecione a caixa de verificação **SAML SSO.**

    b. Na caixa de texto **do Caminho de Pedido,** tipo **/auth/saml**.

    c. Na caixa de texto **Callback Path,** tipo **/auth/saml/callback**.

    d. Na caixa de texto **"Url de serviço ao consumidor de afirmação" (URL de Sign-On único),** insira o valor **URL de resposta,** que inseriu no portal Azure.

    e. Na caixa de texto **(Sp Entity ID)** insira o valor **identificador,** que inseriu no portal Azure.

    f. Na caixa de texto **sSO Target URL do Fornecedor de Identidade,** cole o valor URL de **login,** que copiou a partir do portal Azure.

    exemplo, Abra o certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **do Certificado de Fornecedor de Identidade.**

    h. Na caixa de texto **do formato do identificador** de nome, introduza o valor `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` .

    i. Clique em **Guardar**.

    > [!NOTE]
    > Uma vez guardado o conteúdo, o campo aparecerá em branco para segurança, mas o valor foi guardado na configuração.

### <a name="create-fluxx-labs-test-user"></a>Criar utilizador de teste fluxx Labs

Para permitir que os utilizadores de Azure AD inscrevam-se no Fluxx Labs, devem ser a provisionados em Fluxx Labs. No caso da Fluxx Labs, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa Fluxx Labs como administrador.

2. Clique no **ícone** apresentado abaixo.

    ![Screenshot que mostra opções de administrador com o ícone "Plus" selecionado em "Your Dashboard is Empty".](./media/fluxxlabs-tutorial/config6.png)

3. No painel de instrumentos, clique no ícone apresentado abaixo para abrir o cartão **New PEOPLE.**

    ![Screenshot que mostra o menu "Contact Management" com o ícone "Plus" ao lado de "People" selecionado.](./media/fluxxlabs-tutorial/config4.png)

4. Na secção **PESSOA NOVA,** execute os seguintes passos:

    ![Configuração fluxx Labs](./media/fluxxlabs-tutorial/config5.png)

    a. A Fluxx Labs usa o e-mail como o identificador único para logins SSO. Povoar o campo **SSO UID** com o endereço de e-mail do utilizador, que corresponde ao endereço de e-mail, que estão a usar como login com sSO.

    b. Clique em **Guardar**.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Fluxx Labs no Painel de Acesso, deverá ser automaticamente inscrito nos Laboratórios Fluxx para os quais configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Fluxx Labs com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger a Fluxx Labs com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)