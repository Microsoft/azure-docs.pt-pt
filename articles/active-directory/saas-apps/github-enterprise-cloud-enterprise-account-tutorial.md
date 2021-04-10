---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com GitHub Enterprise Cloud - Enterprise Account | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o GitHub Enterprise Cloud - Conta Empresarial.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2021
ms.author: jeedes
ms.openlocfilehash: 7360ad5abc7342043152c2da11038b624b0bfadc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649995"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-cloud---enterprise-account"></a>Tutorial: Azure Ative Directy integração única (SSO) com GitHub Enterprise Cloud - Conta Empresarial

Neste tutorial, você vai aprender como integrar GitHub Enterprise Cloud - Conta Empresarial com Azure Ative Directory (Azure AD). Quando integrar o GitHub Enterprise Cloud - Conta Empresarial com AZure AD, pode:

* Controle em Azure AD que tem acesso a uma Conta Empresarial GitHub e a quaisquer organizações dentro da Conta Empresarial.


## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma [conta gitHub enterprise.](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/about-enterprise-accounts)
* Uma conta de utilizador GitHub que é proprietária de uma Conta Empresarial. 

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* GitHub Enterprise Cloud - Enterprise Account suporta **SP** e **IDP** iniciado SSO.
* GitHub Enterprise Cloud - Enterprise Account suporta o fornecimento do utilizador **Just In Time.**

## <a name="adding-github-enterprise-cloud---enterprise-account-from-the-gallery"></a>Adicionar GitHub Enterprise Cloud - Conta Empresarial da galeria

Para configurar a integração da GitHub Enterprise Cloud - Conta Empresarial em Azure AD, é necessário adicionar a GitHub Enterprise Cloud - Conta Empresarial da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva GitHub Enterprise Cloud - Conta Empresarial** na caixa de pesquisa.
1. Selecione **GitHub Enterprise Cloud - Conta Empresarial** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-cloud---enterprise-account"></a>Configure e teste Azure AD SSO para GitHub Enterprise Cloud - Conta Empresarial

Configure e teste Azure AD SSO com GitHub Enterprise Cloud - Conta Empresarial usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na GitHub Enterprise Cloud - Conta Empresarial.

Para configurar e testar o Azure AD SSO com a GitHub Enterprise Cloud - Conta Empresarial, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador Azure AD e a conta de utilizador de teste à aplicação GitHub](#assign-your-azure-ad-user-and-the-test-user-account-to-the-github-app)** - para permitir que a sua conta de utilizador e utilizador teste `B.Simon` utilizem um único sinal de Ad AD.
1. **[Ative e Teste SAML para a Conta Empresarial e suas organizações](#enable-and-test-saml-for-the-enterprise-account-and-its-organizations)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Teste SSO com outra conta de empresa ou conta de membro da organização](#test-sso-with-another-enterprise-account-owner-or-organization-member-account)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página **GitHub Enterprise Cloud - Enterprise Account** integration, encontre a secção **Gerir** e selecione um único sinal **de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://github.com/enterprises/<ENTERPRISE-SLUG>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://github.com/enterprises/<ENTERPRISE-SLUG>/saml/consume`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

     Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão:  `https://github.com/enterprises/<ENTERPRISE-SLUG>/sso`

    > [!NOTE]
    > `<ENTERPRISE-SLUG>`Substitua-o pelo nome real da sua Conta Empresarial GitHub.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificateBase64.png)

1. Na secção **Configurar GitHub Enterprise Cloud - Enterprise Account,** copie os URL(s) apropriados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado `B.Simon` .

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

<a name="assign-the-azure-ad-test-user"></a>

### <a name="assign-your-azure-ad-user-and-the-test-user-account-to-the-github-app"></a>Atribua o seu utilizador AZure AD e a conta de utilizador de teste à aplicação GitHub

Nesta secção, você irá ativar `B.Simon` e sua conta de utilizador para usar Azure single sign-on, concedendo acesso a GitHub Enterprise Cloud - Conta Empresarial.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **GitHub Enterprise Cloud - Conta Empresarial**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** e a sua conta de utilizador na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="enable-and-test-saml-for-the-enterprise-account-and-its-organizations"></a>Ativar e Testar SAML para a Conta Empresarial e suas organizações

Para configurar um único sign-on no lado **GitHub Enterprise Cloud - Enterprise Account,** siga os passos listados [nesta documentação do GitHub](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/enforcing-security-settings-in-your-enterprise-account#enabling-saml-single-sign-on-for-organizations-in-your-enterprise-account). 
1. Inscreva-se na GitHub.com com uma conta de utilizador que seja proprietária de uma [conta empresarial.](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/roles-in-an-enterprise#enterprise-owner) 
1. Copie o valor do `Login URL` campo na aplicação a partir do portal Azure e cole-o no `Sign on URL` campo nas definições de GITHub Enterprise Account SAML. 
1. Copie o valor do `Azure AD Identifier` campo na aplicação a partir do portal Azure e cole-o no `Issuer` campo nas definições de GITHub Enterprise Account SAML. 
1. Copie o conteúdo do ficheiro **Certificado (Base64)** que descarregou nos degraus acima do portal Azure e cole-os no campo apropriado nas definições SAML da Conta Empresarial GitHub. 
1. Clique no `Test SAML configuration` e confirme que é capaz de autenticar a partir da Conta Empresarial GitHub para Azure AD com sucesso.
1. Uma vez que o teste seja bem sucedido, guarde as definições. 
1. Após a autenticação via SAML pela primeira vez a partir da conta empresarial GitHub, será criada uma _identidade externa ligada_ na conta empresarial GitHub que associa a conta de utilizador assinada no GitHub à conta de utilizador Azure AD.  
 
Depois de ativar o SSO SAML para a sua Conta Empresarial GitHub, o SAML SSO é ativado por padrão para todas as organizações detidas pela sua Conta Empresarial. Todos os membros serão obrigados a autenticar usando o SSO SAML para ter acesso às organizações onde são membros, e os proprietários de empresas serão obrigados a autenticar usando a SAML SSO ao aceder a uma Conta Empresarial.

<a name="test-sso"></a>

## <a name="test-sso-with-another-enterprise-account-owner-or-organization-member-account"></a>Testar SSO com outra conta de empresa ou conta de membro da organização

Após a integração do SAML ser criada para a conta empresarial GitHub (que também se aplica às organizações GitHub na conta da empresa), outros proprietários de contas empresariais que são atribuídos à app em Azure AD devem poder navegar para o URL da conta empresarial GitHub , `https://github.com/enterprises/<enterprise account>` autenticar via SAML, e aceder às políticas e configurações sob a conta da empresa GitHub. 

Um proprietário de uma organização para uma organização numa conta de empresa deve ser capaz de [convidar um utilizador a aderir à sua organização GitHub.](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-organizations-and-teams/inviting-users-to-join-your-organization) Inscreva-se na GitHub.com com uma conta do proprietário da organização e siga os passos do artigo para convidar `B.Simon` para a organização. Uma conta de utilizador do GitHub terá de ser criada para `B.Simon` se não existir já. 

Para testar o acesso da organização GitHub na Conta Enterprise com a conta de utilizador do `B.Simon` teste:
1. Convidar `B.Simon` para uma organização ao abrigo da Conta Empresarial como proprietária da organização. 
1. Faça o sômis para GitHub.com utilizando a conta de utilizador que pretende ligar à `B.Simon` conta de utilizador Azure AD.
1. Inscreva-se no Azure AD utilizando a `B.Simon` conta de utilizador.
1. Vá à organização GitHub. O utilizador deve ser solicitado a autenticar via SAML. Após a autenticação bem sucedida da SAML, `B.Simon` deve ser capaz de aceder aos recursos da organização. 

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado GitHub Enterprise Cloud - Enterprise Account pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
