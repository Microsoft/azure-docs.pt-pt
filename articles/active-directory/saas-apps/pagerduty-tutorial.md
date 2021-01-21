---
title: 'Tutorial: Integração do Diretório Ativo Azure com PagerDuty | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o PagerDuty.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 57c6057e2515a497faa5731a20ce84e6fc9d1185
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624223"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pagerduty"></a>Tutorial: Azure Ative Directory integração única (SSO) com PagerDuty

Neste tutorial, você vai aprender a integrar PagerDuty com Azure Ative Directory (Azure AD). Quando integrar pagerDuty com Azure AD, pode:

* Controle em Azure AD que tem acesso ao PagerDuty.
* Ativar os seus utilizadores a serem automaticamente inscritos no PagerDuty com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por PagerDuty (SSO).

> [!NOTE]
> Se estiver a utilizar a autenticação MFA ou sem palavra-passe com Azure AD, desligue o valor AuthnContext no Pedido SAML. Caso contrário, a Azure AD lançará o erro no desfasamento do AuthnContext e não enviará o símbolo de volta para a aplicação.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* PagerDuty suporta **SSO** iniciado SP

## <a name="add-pagerduty-from-the-gallery"></a>Adicione PagerDuty da galeria

Para configurar a integração do PagerDuty no AD Azure, é necessário adicionar PagerDuty da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite PagerDuty** na caixa de pesquisa.
1. Selecione **PagerDuty** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-pagerduty"></a>Configurar e testar Azure AD único sinal de inscrição para PagerDuty

Configure e teste Azure AD SSO com PagerDuty usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no PagerDuty.

Para configurar e testar o Azure AD SSO com pagerDuty, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o PagerDuty SSO](#configure-pagerduty-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create PagerDuty test user](#create-pagerduty-test-user)** - para ter uma contraparte de B.Simon em PagerDuty que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **PagerDuty,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<tenant-name>.pagerduty.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<tenant-name>.pagerduty.com`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real em URL, Identifier e URL de resposta. Contacte [a equipa de suporte do Cliente PagerDuty](https://www.pagerduty.com/support/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar PagerDuty,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao PagerDuty.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **PagerDuty**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-pagerduty-sso"></a>Configurar PagerDuty SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Pagerduty como administrador.

2. No menu em cima, clique em **Definições de Conta**.

    ![Definições de conta](./media/pagerduty-tutorial/ic778535.png "Definições de conta")

3. Clique **em 'S sign-on' único**.

    ![Início de sessão único](./media/pagerduty-tutorial/ic778536.png "Início de sessão único")

4. Na página **Enable Single Sign-on (SSO),** execute os seguintes passos:

    ![Ativar um único sinal](./media/pagerduty-tutorial/ic778537.png "Ativar um único sinal")

    a. Abra o certificado codificado base-64 descarregado do portal Azure em bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o para a caixa de texto **do Certificado X.509**
  
    b. Na caixa de texto **do URL de login,** cole URL de **login** que copiou do portal Azure.
  
    c. Na caixa **de textos logout URL,** cole **URL logout** que copiou do portal Azure.

    d. Selecione **Permita o início do nome de utilizador/palavra-passe.**

    e. Selecione Requer uma caixa de **verificação de comparação de contexto de autenticação EXACT.**

    f. Clique em **Guardar Alterações**.

### <a name="create-pagerduty-test-user"></a>Criar utilizador de teste PagerDuty

Para permitir que os utilizadores de Azure AD assinem no PagerDuty, devem ser ateados no PagerDuty. No caso do PagerDuty, o provisionamento é uma tarefa manual.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador pagerduty ou APIs fornecidas pela Pagerduty para fornecer contas de utilizador do Azure Ative Directory.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu inquilino **pagerduty.**

2. No menu em cima, clique em **Utilizadores.**

3. Clique **em Adicionar Utilizadores**.
   
    ![Add Users](./media/pagerduty-tutorial/ic778539.png "Adicionar utilizadores")

4.  No diálogo convidar a **sua equipa,** execute os seguintes passos:
   
    ![Convide a sua equipa](./media/pagerduty-tutorial/ic778540.png "Convide a sua equipa")

    a. Digite o **Primeiro e Último Nome** do utilizador como **B.Simon**. 
   
    b. Insira o endereço de **e-mail** do utilizador como **b.simon \@ contoso.com**.
   
    c. Clique **em Adicionar** e, em seguida, clique em Enviar **Convites.**
   
    > [!NOTE]
    > Todos os utilizadores adicionados receberão um convite para criar uma conta PagerDuty.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de assinatura pagerDuty, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de login do PagerDuty e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo PagerDuty nas Minhas Apps, este irá redirecionar para o URL de assinatura pagerDuty. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado PagerDuty pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
