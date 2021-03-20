---
title: 'Tutorial: Integração do Diretório Ativo Azure com | do RunMyProcess Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o RunMyProcess.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.openlocfilehash: 011bbbda07806f1493ae27fbeef8509f0d12c44b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92518456"
---
# <a name="tutorial-integrate-runmyprocess-with-azure-active-directory"></a>Tutorial: Integrar RunMyProcess com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar RunMyProcess com Azure Ative Directory (Azure AD). Quando integrar o RunMyProcess com AD Azure, pode:

* Control em Azure AD que tem acesso ao RunMyProcess.
* Ativar os seus utilizadores a serem automaticamente inscritos no RunMyProcess com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada por RunMyProcess (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* RunMyProcess suporta **SSO** iniciado SP

## <a name="adding-runmyprocess-from-the-gallery"></a>Adicionar RunMyProcess da galeria

Para configurar a integração do RunMyProcess em AD Azure, é necessário adicionar o RunMyProcess da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** digite **RunMyProcess** na caixa de pesquisa.
1. Selecione **RunMyProcess** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com RunMyProcess usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no RunMyProcess.

Para configurar e testar o Azure AD SSO com o RunMyProcess, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o RunMyProcess SSO](#configure-runmyprocess-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
5. **[Create RunMyProcess test user](#create-runmyprocess-test-user)** - para ter uma contraparte de B.Simon no RunMyProcess que está ligada à representação AD AZure do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **RunMyProcess,** encontre a secção **Gerir** e selecione **'Único sinal de sismo'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte [a equipa de suporte do Cliente RunMyProcess](mailto:support@runmyprocess.com) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar RunMyProcess,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-runmyprocess-sso"></a>Configure RunMyProcess SSO

1. Numa janela diferente do navegador web, inscreva-se no seu inquilino RunMyProcess como administrador.

1. No painel de navegação à esquerda, clique em **'Conta'** e selecione **Configuração.**

    ![A screenshot mostra a configuração selecionada a partir da Conta.](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. Aceda à secção **de método de autenticação** e execute abaixo dos passos:

    ![A screenshot mostra o separador método de autenticação onde pode introduzir os valores descritos.](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Como **Método**, selecione **SSO com Samlv2**.

    b. Na caixa de texto **de redirecionamento SSO,** cole o valor do URL de **Login,** que copiou do portal Azure.

    c. Na caixa de texto **de redirecionamento logout,** cole o valor do **URL logout,** que copiou do portal Azure.

    d. Na caixa de texto **do formato ID name,** digite o valor do **formato identificador** de nome como **urna:oasis:names:tc:SAML:1.1:nameid-formato:emailAddress**.

    e. Abra o ficheiro de certificado descarregado do portal Azure no bloco de notas, copie o conteúdo do ficheiro de certificado e, em seguida, cole-o na caixa de texto **Certificate.**

    f. Clique **em Guardar** o ícone.

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

Nesta secção, você permitirá que B.Simon utilize o Azure single sign-on, concedendo acesso ao RunMyProcess.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **RunMyProcess**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-runmyprocess-test-user"></a>Criar utilizador de teste RunMyProcess

Para permitir que os utilizadores de Azure AD inscrevam-se no RunMyProcess, devem ser a provisionados no RunMyProcess. No caso do RunMyProcess, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa RunMyProcess como administrador.

1. Clique em **'Conta'** e selecione **utilizadores** no painel de navegação esquerdo e, em seguida, clique em **Novo Utilizador**.

    ![Novo Utilizador](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "Novo Utilizador")

1. Na secção Definições do **Utilizador,** execute os seguintes passos:

    ![Perfil](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "Perfil")
  
    a. Digite o **Nome** e **e-mail** de uma conta Azure AD válida que pretende obter nas caixas de texto relacionadas.

    b. Selecione um **idioma IDE,** **idioma** e **perfil.**

    c. Selecione **Enviar-me um e-mail de criação de conta.**

    d. Clique em **Guardar**.

    > [!NOTE]
    > Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador do RunMyProcess ou APIs fornecidas pelo RunMyProcess para fornecer contas de utilizador do Azure Ative Directory.

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo RunMyProcess no Painel de Acesso, deverá ser automaticamente inscrito no RunMyProcess para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)