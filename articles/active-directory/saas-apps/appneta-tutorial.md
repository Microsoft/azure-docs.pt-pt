---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com AppNeta Performance Manager | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o AppNeta Performance Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 57ac41e56a2a418b7f261bfd0435a4726a64469b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309497"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-appneta-performance-manager"></a>Tutorial: Azure Ative Directory integração única (SSO) com AppNeta Performance Manager

Neste tutorial, você vai aprender a integrar AppNeta Performance Manager com Azure Ative Directory (Azure AD). Quando integrar o AppNeta Performance Manager com AZure AD, pode:

* Control em Azure AD que tem acesso ao AppNeta Performance Manager.
* Capacitar os seus utilizadores a serem automaticamente inscritos no AppNeta Performance Manager com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* AppNeta Performance Manager única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* AppNeta Performance Manager suporta **SSO** iniciado SP

* AppNeta Performance Manager suporta **provisão de** utilizadores just in time

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.


## <a name="adding-appneta-performance-manager-from-the-gallery"></a>Adicionar AppNeta Performance Manager da galeria

Para configurar a integração do AppNeta Performance Manager em AZure AD, é necessário adicionar o AppNeta Performance Manager da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite AppNeta Performance Manager** na caixa de pesquisa.
1. Selecione **AppNeta Performance Manager** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-appneta-performance-manager"></a>Configure e teste Azure AD SSO para AppNeta Performance Manager

Configure e teste Azure AD SSO com AppNeta Performance Manager usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no AppNeta Performance Manager.

Para configurar e testar o Azure AD SSO com o AppNeta Performance Manager, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o AppNeta Performance Manager SSO](#configure-appneta-performance-manager-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create AppNeta Performance Manager test user](#create-appneta-performance-manager-test-user)** - para ter uma contraparte de B.Simon no AppNeta Performance Manager que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **AppNeta Performance Manager,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<subdomain>.pm.appneta.com`

    > [!NOTE]
    > O valor url de inscrição não é real. Atualize este valor com o URL Sign-On real. Contacte [a equipa de suporte do Cliente AppNeta Performance Manager](mailto:support@appneta.com) para obter este valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação AppNeta Performance Manager espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação AppNeta Performance Manager espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com o seu requisito.

    | Name | Atributo de origem|
    | --------| ----------------|
    | nomePróprio| user.givenname|
    | apelido| utilizador.sobrenome|
    | e-mail| user.userprincipalname|
    | name| user.userprincipalname|
    | grupos  | user.assignedroles |
    | telefone| user.telephonenumber |
    | título| user.jobtitle|
    | | |

    > [!NOTE]
    > **grupos** referem-se ao grupo de segurança em Appneta que é mapeado para um **papel** em Azure AD. Consulte [este](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) doc que explica como criar papéis personalizados no Azure AD.

    1. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    1. Na caixa de texto **'Nome',** digite o nome do atributo indicado para esta linha.

    1. Deixe o **Espaço Namespace** em branco.

    1. Selecione Fonte como **Atributo**.

    1. A partir da lista **de atributos Source,** digite o valor de atributo mostrado para esta linha.

    1. Clique **em Ok**

    1. Clique em **Guardar**.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar appNeta Performance Manager,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao AppNeta Performance Manager.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, **selecione AppNeta Performance Manager**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se tiver configurado as funções como explicado no acima, pode selecioná-lo a partir do Dropdown de **função Select.**
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**
## <a name="configure-appneta-performance-manager-sso"></a>Configurar appNeta Performance Manager SSO

Para configurar um único sign-on no lado **appNeta Performance Manager,** você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipa de [suporte AppNeta Performance Manager](mailto:support@appneta.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-appneta-performance-manager-test-user"></a>Criar utilizador de teste appNeta Performance Manager

Nesta secção, um utilizador chamado Britta Simon é criado no AppNeta Performance Manager. O AppNeta Performance Manager suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no AppNeta Performance Manager, um novo é criado após a autenticação.

> [!Note]
> Se precisar de criar um utilizador manualmente, contacte a equipa de [suporte do AppNeta Performance Manager](mailto:support@appneta.com).

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de entrada do Gestor de Desempenho appNeta, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL do Gestor de Desempenho appNeta e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo do AppNeta Performance Manager nas Minhas Apps, este será redirecionado para o URL de apresentação do Gestor de Desempenho appNeta. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Assim que configurar o AppNeta Performance Manager, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
