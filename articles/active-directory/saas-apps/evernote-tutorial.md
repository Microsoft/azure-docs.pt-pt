---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com evernote | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Evernote.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.openlocfilehash: 86a314cd5255c06a70d0f9b28d06e3ac4156fdb6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92453839"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evernote"></a>Tutorial: Azure Ative Directy integração única (SSO) com Evernote

Neste tutorial, você vai aprender a integrar o Evernote com o Azure Ative Directory (Azure AD). Quando integrar o Evernote com AZure AD, pode:

* Controlo em Azure AD que tem acesso ao Evernote.
* Permita que os seus utilizadores sejam automaticamente inscritos no Evernote com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por Evernote single sign-on (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Evernote apoia **SP e IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-evernote-from-the-gallery"></a>Adicionar Evernote da galeria

Para configurar a integração do Evernote no Azure AD, é necessário adicionar o Evernote da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **escreva Evernote** na caixa de pesquisa.
1. Selecione **Evernote** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-evernote"></a>Configurar e testar Azure AD único sinal para Evernote

Configure e teste Azure AD SSO com o Evernote utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Evernote.

Para configurar e testar o Azure AD SSO com o Evernote, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Evernote SSO](#configure-evernote-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Evernote test user](#create-evernote-test-user)** - para ter uma contraparte de B.Simon no Evernote que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Evernote,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    Na caixa de texto **identifier,** digite um URL:  `https://www.evernote.com/saml2`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL:  `https://www.evernote.com/Login.action`

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

7. Para modificar as opções **de Assinatura,** clique no botão **Editar** para abrir o diálogo do Certificado de **Assinatura SAML.**

    ![Screenshot que mostra o diálogo "S A M L Signing Certificate" com o botão "Editar" selecionado.](common/edit-certificate.png) 

    ![image](./media/evernote-tutorial/samlassertion.png)

    a. Selecione a opção **de resposta e afirmação de Signe SAML** para **a opção de assinatura**.

    b. Clique em **Guardar**

1. Na secção Configurar o **Evernote,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Evernote.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Evernote**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-evernote-sso"></a>Configurar Evernote SSO

1. Para automatizar a configuração dentro do Evernote, é necessário instalar a extensão do **navegador 'As aplicações' Secure's,** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique na **configuração O Evernote** irá direcioná-lo para a aplicação Evernote. A partir daí, forneça as credenciais de administração para assinar no Evernote. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o Evernote manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Evernote como administrador e execute os seguintes passos:

4. Ir para **'Admin Console'**

    ![Admin-Console](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

5. A partir da **'Consola de Administração',** vá a **'Segurança'** e selecione **'Single Sign-On'**

    ![SSO-Setting](./media/evernote-tutorial/tutorial_evernote_sso.png)

6. Configure os seguintes valores:

    ![Certificate-Setting](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Ativar SSO:** SSO é ativado por padrão (Clique **em Desativar o Único Sinal** para remover o requisito SSO)

    b. Cole o valor URL do **Login,** que copiou do portal Azure para a caixa de texto **URL DE PEDIDO DE SAML HTTP.**

    c. Abra o certificado descarregado a partir de Azure AD num bloco de notas e copie o conteúdo, incluindo "BEGIN CERTIFICATE" e "END CERTIFICATE" e cole-o na caixa de texto **do Certificado X.509.** 

    d.Clique **Em Guardar Alterações**

### <a name="create-evernote-test-user"></a>Criar utilizador de teste Evernote

Para permitir que os utilizadores da Azure AD assinem no Evernote, devem ser adsajados no Evernote.  
No caso do Evernote, o provisionamento é uma tarefa manual.

**Para a disponibilização de uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu site da empresa Evernote como administrador.

2. Clique na **'Consola de Administração'.**

    ![Admin-Console](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. A partir da **'Consola de Administração',** aceda a **'Adicionar utilizadores'.**

    ![Screenshot que mostra o menu "Utilizadores" com "Adicionar utilizadores" selecionado.](./media/evernote-tutorial/create_aaduser_0001.png)

4. **Adicione os membros** da equipa na caixa de texto **do Email,** digite o endereço de e-mail da conta do utilizador e clique em **Convidar.**

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. Após o envio do convite, o titular da conta Azure Ative Directory receberá um e-mail para aceitar o convite.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo evernote no Painel de Acesso, deverá ser automaticamente inscrito no Evernote para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Evernote com a Azure AD](https://aad.portal.azure.com/)