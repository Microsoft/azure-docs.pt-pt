---
title: 'Tutorial: Integração do Diretório Ativo Azure com Ivanti Service Manager (ISM) | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Ivanti Service Manager (ISM).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 16aceeddcba574953ac03ccf04d099762b0000c3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648888"
---
# <a name="tutorial-azure-active-directory-integration-with-ivanti-service-manager-ism"></a>Tutorial: Integração do Diretório Ativo Azure com o Ivanti Service Manager (ISM)

Neste tutorial, você vai aprender a integrar Ivanti Service Manager (ISM) com Azure Ative Directory (Azure AD). Quando integrar o Ivanti Service Manager (ISM) com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Ivanti Service Manager (ISM).
* Ative os seus utilizadores a serem automaticamente inscritos no Ivanti Service Manager (ISM) com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Ivanti Service Manager (ISM) assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Ivanti Service Manager (ISM) apoia **SP e IDP** iniciado SSO
* Ivanti Service Manager (ISM) suporta o fornecimento do utilizador **Just In Time**

## <a name="add-ivanti-service-manager-ism-from-the-gallery"></a>Adicionar Ivanti Service Manager (ISM) da galeria

Para configurar a integração do Ivanti Service Manager (ISM) no Azure AD, é necessário adicionar o Ivanti Service Manager (ISM) da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Ivanti Service Manager (ISM)** na caixa de pesquisa.
1. Selecione **Ivanti Service Manager (ISM)** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-ivanti-service-manager-ism"></a>Configure e teste Azure AD SSO para Ivanti Service Manager (ISM)

Configure e teste Azure AD SSO com Ivanti Service Manager (ISM) usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Ivanti Service Manager (ISM).

Para configurar e testar a Azure AD SSO com o Ivanti Service Manager (ISM), execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Ivanti Service Manager (ISM) SSO](#configure-ivanti-service-manager-ism-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Ivanti Service Manager (ISM) test user](#create-ivanti-service-manager-ism-test-user)** - para ter uma contraparte de B.Simon in Ivanti Service Manager (ISM) que está ligada à representação AD Ad Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **do Ivanti Service Manager (ISM),** encontre a secção **Gerir** e selecione **um único sinal de sação.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando um dos seguintes padrões:
    
    ```http
    https://<customer>.saasit.com/
    https://<customer>.saasiteu.com/
    https://<customer>.saasitau.com/
    ```

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<customer>/handlers/sso/SamlAssertionConsumerHandler.ashx`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<customer>.saasit.com/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte a [equipa de suporte ao cliente ivanti Service Manager (ISM)](https://www.ivanti.com/support/contact) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Raw)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificateraw.png)

7. Na secção **Configurar Ivanti Service Manager (ISM),** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Ivanti Service Manager (ISM).

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Ivanti Service Manager (ISM)**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-ivanti-service-manager-ism-sso"></a>Configure Ivanti Service Manager (ISM) SSO

Para configurar um único sign-on no lado **do Ivanti Service Manager (ISM),** é necessário enviar o Certificado descarregado **(Raw)** e URLs copiados apropriados do portal Azure para a equipa de [suporte do Ivanti Service Manager (ISM).](https://www.ivanti.com/support/contact) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-ivanti-service-manager-ism-test-user"></a>Criar o utilizador de teste Ivanti Service Manager (ISM)

Nesta secção, um utilizador chamado Britta Simon é criado no Ivanti Service Manager (ISM). O Ivanti Service Manager (ISM) suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Ivanti Service Manager (ISM), um novo é criado após a autenticação.

> [!Note]
> Se precisar de criar um utilizador manualmente, contacte a [equipa de suporte do Ivanti Service Manager (ISM).](https://www.ivanti.com/support/contact)

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Ivanti Service Manager (ISM) Assinar no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de entrada de serviços da Ivanti (ISM) e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Ivanti Service Manager (ISM) para o qual configura o SSO. 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo do Ivanti Service Manager (ISM) nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de candidatura na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Ivanti Service Manager (ISM) para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Ivanti Service Manager (ISM) pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).