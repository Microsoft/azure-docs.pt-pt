---
title: 'Tutorial: Integração do Diretório Ativo Azure com asana | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Asana.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: a06c94eed6c90d7b38f28d37f3c8145d4ac1151d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651013"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Tutorial: Integração do Diretório Ativo Azure com a Asana

Neste tutorial, você vai aprender a integrar Asana com Azure Ative Direy (Azure AD). Quando integrar a Asana com a Ad Azure, pode:

* Controlo em Azure AD que tem acesso a Asana.
* Ative os seus utilizadores a serem automaticamente inscritos na Asana com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Asana assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Asana apoia **SP** iniciado SSO

* Asana suporta fornecimento [ **automatizado** de utilizadores](asana-provisioning-tutorial.md)

## <a name="add-asana-from-the-gallery"></a>Adicione Asana da galeria

Para configurar a integração da Asana no Azure AD, precisa adicionar Asana da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **digite Asana** na caixa de pesquisa.
1. Selecione **Asana** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-asana"></a>Configure e teste Azure AD SSO para Asana

Configure e teste Azure AD SSO com Asana usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Asana.

Para configurar e testar a Azure AD SSO com a Asana, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Asana SSO](#configure-asana-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Asana test user](#create-asana-test-user)** - para ter uma contraparte de B.Simon em Asana que está ligada à representação AD AZure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Asana,** encontre a secção **Gerir** e selecione **'Único sinal de s-on'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com página SAML,** clique no ícone de lápis para **configuração SAML básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Asana Domain e URLs informações únicas de súmis](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** o URL: `https://app.asana.com/`

    b. Na caixa de texto **identifier (Entity ID),** digite o URL: `https://app.asana.com/`

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configuração Asana,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Asana.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Asana.**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="configure-asana-sso"></a>Configurar Asana SSO

1. Numa janela de navegador diferente, inscreva-se na sua aplicação Asana. Para configurar o SSO em Asana, aceda às definições do espaço de trabalho clicando no nome do espaço de trabalho no canto superior direito do ecrã. Em seguida, clique em **\<your workspace name\> Definições**.

    ![Asana sso configurações](./media/asana-tutorial/settings.png)

2. Na janela de definições da **Organização,** clique em **Administração**. Em seguida, clique **em Membros devem iniciar sessão via SAML** para ativar a configuração SSO. Executar os seguintes passos:

    ![Configurar configurar configurações da Organização de Sign-On Única](./media/asana-tutorial/save.png)  

    a. Na caixa de texto **URL da página de início de sessão,** cole o URL de início de **sessão**.

    b. Clique no certificado descarregado do portal Azure e, em seguida, abra o ficheiro de certificado usando o Notepad ou o seu editor de texto preferido. Copie o conteúdo entre o título do certificado de início e de fim e cole-o na caixa de texto **do Certificado X.509.**

3. Clique em **Guardar**. Vá ao [guia da Asana para configurar o SSO](https://asana.com/guide/help/premium/authentication#gl-saml) se precisar de mais assistência.

### <a name="create-asana-test-user"></a>Criar utilizador de teste Asana

O objetivo desta secção é criar um utilizador chamado Britta Simon em Asana. A Asana suporta o fornecimento automático do utilizador, que está por defeito. Pode encontrar mais detalhes [aqui](asana-provisioning-tutorial.md) sobre como configurar o fornecimento automático do utilizador.

**Se precisar de criar o utilizador manualmente, execute os seguintes passos:**

Nesta secção, cria-se um utilizador chamado Britta Simon em Asana.

1. Em **Asana,** vá à secção **de Equipas** no painel esquerdo. Clique no botão de sinal de mais.

    ![Criar um utilizador de teste AZure AD](./media/asana-tutorial/teams.png)

2. Digite o e-mail do utilizador como **britta.simon \@ contoso.com** na caixa de texto e, em seguida, selecione **Convidar**.

3. Clique **em Enviar Convite.** O novo utilizador receberá um e-mail na sua conta de e-mail. o utilizador terá de criar e validar a conta.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de inscrição de Asana, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição de Asana e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Asana nas Minhas Apps, este será redirecionado para o URL de assinatura de Asana. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Asana, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).