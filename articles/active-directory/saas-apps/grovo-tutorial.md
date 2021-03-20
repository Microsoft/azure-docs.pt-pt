---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com Grovo | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Grovo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.openlocfilehash: 84bbec783630aadc68a9632c90ee90f4a8cc98d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92446706"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-grovo"></a>Tutorial: Azure Ative Directory integração única (SSO) com Grovo

Neste tutorial, você vai aprender a integrar Grovo com Azure Ative Directory (Azure AD). Quando integrar o Grovo com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Grovo.
* Permita que os seus utilizadores sejam automaticamente inscritos na Grovo com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* SSO ativava a assinatura de um único sign-on (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Grovo apoia **SP e IDP** iniciado SSO
* Grovo suporta **provisão de** utilizadores just in time

## <a name="adding-grovo-from-the-gallery"></a>Adicionando Grovo da galeria

Para configurar a integração da Grovo no Azure AD, precisa adicionar o Grovo da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **da galeria,** escreva **Grovo** na caixa de pesquisa.
1. Selecione **Grovo** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-grovo"></a>Configurar e testar Azure AD único sinal para Grovo

Configure e teste Azure AD SSO com Grovo usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Grovo.

Para configurar e testar a Azure AD SSO com a Grovo, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Grovo SSO](#configure-grovo-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Grovo test user](#create-grovo-test-user)** - para ter uma contraparte de B.Simon em Grovo que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Grovo,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.grovo.com/sso/saml2/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

    c. Clique **em Definir URLs adicionais**.

    d. Na caixa de texto **do Estado de retransmissão,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.grovo.com`

1. Clique **em Definir URLs adicionais** e execute os seguintes passos se desejar configurar a aplicação no modo iniciado **sp:**

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta, URL de inscrição e Estado de retransmissão. Contacte a [equipa de suporte do Cliente Grovo](https://www.grovo.com/contact-us) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Grovo,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Grovo.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Grovo**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-grovo-sso"></a>Configure Grovo SSO

1. Numa janela diferente do navegador web, inscreva-se no Grovo como Administrador.

2. Vá **para**  >  **integrações de administração.**
 
    ![Screenshot que mostra o menu "Administrador" com "Integrações" selecionados.](./media/grovo-tutorial/tutorial_grovo_admin.png) 

3. Clique **em CONFIGURAÇÃO** na secção **SP Iniciado SAML 2.0.**

    ![Screenshot que mostra a secção "S P iniciado S A M L 2.0" com o botão "Configurar" selecionado.](./media/grovo-tutorial/tutorial_grovo_setup.png)

4. Na janela popup **SP Initiated SAML 2.0** execute os seguintes passos:

    ![Configuração Grovo](./media/grovo-tutorial/tutorial_grovo_saml.png)

    a. Na **caixa de** texto ID da Entidade, cole o valor do **Identificador AZURE AD,** que copiou do portal Azure.

    b. Na caixa de texto **de serviço de referência única,** cole o valor do URL de **login,** que copiou do portal Azure.

    c. Selecione **uma única ligação de ponto de serviço de serviço** como `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect` .
    
    d. Abra o **certificado codificado Base64** descarregado do portal Azure em bloco de notas, cole-o na caixa de texto **de chave pública.**

    e. Clique em **Seguinte**.

### <a name="create-grovo-test-user"></a>Criar utilizador de teste Grovo

Nesta secção, um utilizador chamado B.Simon é criado em Grovo. A Grovo suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Grovo, um novo é criado após a autenticação.

> [!Note]
> Se necessitar de criar um utilizador manualmente, contacte a [equipa de suporte da Grovo](https://www.grovo.com/contact-us).

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Grovo no Painel de Acesso, deverá ser automaticamente inscrito no Grovo para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente Grovo com Azure AD](https://aad.portal.azure.com/)