---
title: 'Tutorial: Integração do Azure Ative Directory com a Veritas Enterprise Vault.cloud SSO | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Veritas Enterprise Vault.cloud SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 2796928316197bd48723253dbc97dfcd34ac95e8
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222287"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Tutorial: Integração do Azure Ative Directory com a Veritas Enterprise Vault.cloud SSO

Neste tutorial, você vai aprender a integrar a Veritas Enterprise Vault.cloud SSO com Azure Ative Directory (Azure AD). Quando integrar a Veritas Enterprise Vault.cloud SSO com Azure AD, pode:

* Control em Azure AD que tem acesso à Veritas Enterprise Vault.cloud SSO.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Veritas Enterprise Vault.cloud SSO com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Veritas Enterprise Vault.cloud SSO, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A Veritas Enterprise Vault.cloud subscrição única ativada por SSO.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* A Veritas Enterprise Vault.cloud SSO suporta a **SP** iniciada SSO.

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="add-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Adicione a Veritas Enterprise Vault.cloud SSO da galeria

Para configurar a integração da Veritas Enterprise Vault.cloud SSO em AD Azure, precisa adicionar a Veritas Enterprise Vault.cloud SSO da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **de galeria,** **escreva Veritas Enterprise Vault.cloud SSO** na caixa de pesquisa.
1. Selecione **Veritas Enterprise Vault.cloud SSO** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-veritas-enterprise-vaultcloud-sso"></a>Configure e teste Azure AD SSO para a Veritas Enterprise Vault.cloud SSO

Configure e teste Azure AD SSO com a Veritas Enterprise Vault.cloud SSO utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AD Azure e o utilizador relacionado na Veritas Enterprise Vault.cloud SSO.

Para configurar e testar a Azure AD SSO com a Veritas Enterprise Vault.cloud SSO, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Veritas Enterprise Vault.cloud SSO SSO](#configure-veritas-enterprise-vaultcloud-sso-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Veritas Enterprise Vault.cloud utilizador de teste SSO](#create-veritas-enterprise-vaultcloud-sso-test-user)** - para ter uma contrapartida de B.Simon na Veritas Enterprise Vault.cloud SSO que está ligada à representação AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração **de aplicações SSO Vault.cloud Veritas Enterprise,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. Na caixa **identifier,** digite um dos URLs de acordo com o Datacenter:

    | Datacenter| URL |
    |----------|----|
    | América do Norte| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Ásia-Pacífico| `https://auth.syd.archivecloud.net`|

    c. Na caixa de texto **URL de resposta,** digite um dos URLs de acordo com o Datacenter:

    | Datacenter| URL |
    |----------|----|
    | América do Norte| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Ásia-Pacífico| `https://auth.syd.archivecloud.net`|

    > [!NOTE]
    > Este valor não é real. Atualize este valor com o URL Sign-On real. Contacte [a Veritas Enterprise Vault.cloud equipa de suporte ao cliente SSO](https://www.veritas.com/support/.html) para obter este valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar a Veritas Enterprise Vault.cloud SSO,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Veritas Enterprise Vault.cloud SSO.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Veritas Enterprise Vault.cloud SSO**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-veritas-enterprise-vaultcloud-sso-sso"></a>Configure Veritas Enterprise Vault.cloud SSO SSO

Para configurar um único sign-on na Veritas Enterprise Vault.cloud lado **SSO,** você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para [Veritas Enterprise Vault.cloud equipa de apoio SSO](https://www.veritas.com/support/.html). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-veritas-enterprise-vaultcloud-sso-test-user"></a>Criar Vault.cloud utilizador de teste SSO da Veritas Enterprise

Nesta secção, cria-se um utilizador chamado Britta Simon na Veritas Enterprise Vault.cloud SSO. Trabalhe com [a Equipa de Suporte SSO da Veritas Enterprise Vault.cloud](https://www.veritas.com/support/.html) para adicionar os utilizadores na plataforma SSO Vault.cloud Da Veritas Enterprise. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para a Veritas Enterprise Vault.cloud URL de inscrição SSO onde pode iniciar o fluxo de login. 

* Vá diretamente à Veritas Enterprise Vault.cloud URL de inscrição SSO e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo SSO Vault.cloud Veritas Enterprise nas Minhas Apps, este será redirecionado para a Veritas Enterprise Vault.cloud URL de assinatura SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Assim que configurar a Veritas Enterprise Vault.cloud SSO, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
