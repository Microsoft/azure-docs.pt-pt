---
title: 'Tutorial: Integração do Azure Ative Directory com a Cisco Umbrella Admin SSO | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Cisco Umbrella Admin SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: jeedes
ms.openlocfilehash: 6073142b21a28f1242e0e6ec65ee2945a354b60b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592511"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella-admin-sso"></a>Tutorial: Integração do Azure Ative Directory com a Cisco Umbrella Admin SSO

Neste tutorial, você vai aprender a integrar Cisco Umbrella Admin SSO com Azure Ative Directory (Azure AD). Quando integrar a Cisco Umbrella Admin SSO com Azure AD, pode:

* Control em Azure AD que tem acesso à Cisco Umbrella Admin SSO.
* Permita que os seus utilizadores sejam automaticamente inscritos na Cisco Umbrella Admin SSO com as suas contas AD AZure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Cisco Umbrella Admin SSO assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Cisco Umbrella Admin SSO apoia **SP e IDP** iniciado SSO.

## <a name="add-cisco-umbrella-admin-sso-from-the-gallery"></a>Adicione Cisco Umbrella Admin SSO da galeria

Para configurar a integração da Cisco Umbrella Admin SSO em Azure AD, você precisa adicionar Cisco Umbrella Admin SSO da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **de galeria,** **digite Cisco Umbrella Admin SSO** na caixa de pesquisa.
1. Selecione **Cisco Umbrella Admin SSO** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-cisco-umbrella-admin-sso"></a>Configure e teste Azure AD SSO para Cisco Umbrella Admin SSO

Configure e teste Azure AD SSO com Cisco Umbrella Admin SSO usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Cisco Umbrella Admin SSO.

Para configurar e testar o Azure AD SSO com a Cisco Umbrella Admin SSO, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure cisco Umbrella Admin SSO SSO](#configure-cisco-umbrella-admin-sso-sso)** - para configurar as definições de sinalização única no lado da aplicação.
    1. **[Create Cisco Umbrella Admin SSO test user](#create-cisco-umbrella-admin-sso-test-user)** - para ter uma contraparte de B.Simon na Cisco Umbrella Admin SSO que está ligada à representação AD AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Cisco Umbrella Admin SSO,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure.

    a. Se desejar configurar a aplicação no modo iniciado pela **SP,** execute os seguintes passos:

    b. Clique **em Definir URLs adicionais**.

    c. Na caixa de texto **URL de entrada de sinais,** digite o URL: `https://login.umbrella.com/sso`

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configuração Cisco Umbrella Admin SSO,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Cisco Umbrella Admin SSO.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Cisco Umbrella Admin SSO**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-cisco-umbrella-admin-sso-sso"></a>Configure Cisco Umbrella Admin SSO SSO

1. Numa janela de navegador diferente, inscreva-se no site da empresa Cisco Umbrella Admin SSO como administrador.

2. A partir do lado esquerdo do menu, clique em **Administração** e navegue para **autenticação** e, em seguida, clique em **SAML**.

    ![O Administrador](./media/cisco-umbrella-tutorial/admin.png)

3. Escolha **outro e** clique em **NEXT**.

    ![O Outro](./media/cisco-umbrella-tutorial/other.png)

4. No **Cisco Umbrella Admin Metadados SSO**, página, clique em **SEGUINTE**.

    ![Os metadados](./media/cisco-umbrella-tutorial/metadata.png)

5. No **separador Upload Metadata,** se tiver configurado SAML pré-configurado, **selecione Clique aqui para alterá-los** e siga os passos abaixo.

    ![O Próximo](./media/cisco-umbrella-tutorial/next.png)

6. Na **Opção A: Carregar** o ficheiro XML , carregar o ficheiro **XML dos Metadados da Federação** que descarregou a partir do portal Azure e depois de carregar metadados os valores abaixo são automaticamente preenchidos automaticamente e depois clique em **SEGUINTE**.

    ![O ficheiro de escolha](./media/cisco-umbrella-tutorial/choose-file.png)

7. Na secção **de configuração SAML validado,** clique em **TESTAR a configuração DO SAML**.

    ![O Teste](./media/cisco-umbrella-tutorial/test.png)

8. Clique **em SAVE**.

### <a name="create-cisco-umbrella-admin-sso-test-user"></a>Criar utilizador de teste Cisco Umbrella Admin SSO

Para permitir que os utilizadores da Azure AD iniciem sessão na Cisco Umbrella Admin SSO, devem ser adavisionados na Cisco Umbrella Admin SSO.  
No caso da Cisco Umbrella Admin SSO, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Numa janela de navegador diferente, inscreva-se no site da empresa Cisco Umbrella Admin SSO como administrador.

2. Do lado esquerdo do menu, clique em **Administração** e navegue para **contas.**

    ![A Conta](./media/cisco-umbrella-tutorial/account.png)

3. Na página **'Contas',** clique em **Adicionar** no lado superior direito da página e executar os seguintes passos.

    ![O Utilizador](./media/cisco-umbrella-tutorial/create-user.png)

    a. No campo **Primeiro Nome,** insira o primeiro nome como **Britta.**

    b. No campo **Último Nome,** insira o último nome como **o Simon.**

    c. A partir do **Papel de Administração Delegado,** selecione o seu papel.

    d. No campo **'Endereço de e-mail',** insira o endereço de e-mail do utilizador como **o \@ brittasimon contoso.com**.

    e. No campo **Palavra-Passe,** insira a sua palavra-passe.

    f. No campo **Palavra-Passe Confirmar,** introduza novamente a sua palavra-passe.

    exemplo, Clique **em CREATE**.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Cisco Umbrella Admin SSO Assinar no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de assinatura SSO da Cisco Umbrella e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Cisco Umbrella Admin SSO para o qual configura o SSO. 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo Cisco Umbrella Admin SSO nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Cisco Umbrella Admin SSO para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Cisco Umbrella Admin SSO, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).