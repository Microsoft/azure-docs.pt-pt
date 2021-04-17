---
title: 'Tutorial: Integração do Diretório Ativo Azure com Clarizen One | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Clarizen One.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: f7e90ff4c69e03482a1608185bc947ccb8604187
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516946"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen-one"></a>Tutorial: Integração do Diretório Ativo Azure com Clarizen One

Neste tutorial, você vai aprender a integrar Clarizen One com Azure Ative Directory (Azure AD). Quando integrar clarizen One com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Clarizen One.
* Ative os seus utilizadores a serem automaticamente inscritos no Clarizen One com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Clarizen Uma única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Clarizen One suporta **IDP** iniciado SSO.

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="add-clarizen-one-from-the-gallery"></a>Adicione Clarizen One da galeria

Para configurar a integração do Clarizen One em AD Azure, precisa adicionar Clarizen One da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **escreva Clarizen One** na caixa de pesquisa.
1. Selecione **Clarizen One** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-clarizen-one"></a>Configure e teste Azure AD SSO para Clarizen One

Configure e teste Azure AD SSO com Clarizen One usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Clarizen One.

Para configurar e testar Azure AD SSO com Clarizen One, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Clarizen One SSO](#configure-clarizen-one-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Clarizen test user](#create-clarizen-one-test-user)** - para ter uma contraparte de B.Simon em Clarizen One que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Clarizen One,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na **configuração single Sign-On com página SAML,** execute os seguintes passos:

    a. Na caixa de texto **identifier,** digite o valor: `Clarizen`

    b. Na caixa de texto **URL de resposta,** digite o URL: `https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar Clarizen One,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Criar um utilizador de teste AZure AD 

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Clarizen One.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Clarizen One**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função &quot;Acesso Predefinido&quot; selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name=&quot;configure-clarizen-one-sso&quot;></a>Configure Clarizen One SSO

1. Numa janela diferente do navegador web, inscreva-se no seu site da empresa Clarizen One como administrador.

1. Clique no seu nome de utilizador e, em seguida, clique em **Definições**.

    ![Clicar em &quot;Definições&quot; no seu nome de utilizador](./media/clarizen-tutorial/setting.png &quot;Definições")

1. Clique no separador **Definições Globais.** Em seguida, ao lado da **Autenticação Federada,** clique **em editar**.

    ![Separador "Configurações Globais"](./media/clarizen-tutorial/authentication.png "Definições Globais")

1. Na caixa de diálogo de **autenticação federada,** execute os seguintes passos:

    ![Caixa de diálogo "Autenticação Federada"](./media/clarizen-tutorial/federated-authentication.png "Autenticação Federada")

    a. Selecione **Autenticação Federada Ativada**.

    b. Clique **em Upload** para fazer upload do certificado descarregado.

    c. Na caixa **URL de entrada de inscrição,** introduza o valor do URL de **login** a partir da janela de configuração da aplicação Azure.

    d. Na caixa **URL de inscrição,** introduza o valor do **URL logout** a partir da janela de configuração da aplicação AZure AD.

    e. Selecione **Use POST**.

    f. Clique em **Guardar**.

### <a name="create-clarizen-one-test-user"></a>Criar Clarizen Um utilizador de teste

O objetivo desta secção é criar um utilizador chamado Britta Simon em Clarizen One.

**Se precisar de criar o utilizador manualmente, execute os seguintes passos:**

Para permitir que os utilizadores de Azure AD inscrevam-se no Clarizen One, é necessário que provisões para contas de utilizador. No caso do Clarizen One, o provisionamento é uma tarefa manual.

1. Inscreva-se no seu site da empresa Clarizen One como administrador.

2. Clique **em Pessoas.**

    ![Clicando em "Pessoas"](./media/clarizen-tutorial/people.png "People")

3. Clique **em Convidar Utilizador.**

    ![Botão "Convidar utilizador"](./media/clarizen-tutorial/user.png "Convidar utilizadores")

1. Na caixa de diálogo **Pessoas Convidadas,** execute os seguintes passos:

    ![Caixa de diálogo "Convidar pessoas"](./media/clarizen-tutorial/invite-people.png "Convidar pessoas")

    a. Na caixa **de e-mail,** digite o endereço de e-mail da conta Britta Simon.

    b. Clique **em Convidar.**

    > [!NOTE]
    > O titular da conta Azure Ative Directory receberá um e-mail e seguirá um link para confirmar a sua conta antes de ficar ativa.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no Clarizen One para o qual configura o SSO.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Clarizen One nas Minhas Apps, deverá ser automaticamente inscrito no Clarizen One para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Clarizen One, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).