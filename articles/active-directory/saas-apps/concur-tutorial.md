---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com | Microsoft Docs'
description: Saiba como configurar o SSO entre o Azure Ative Directory e o Concur.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/26/2020
ms.author: jeedes
ms.openlocfilehash: 006d30839e16ea303b684fb0ee1be7f40dcbdfb7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736982"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur"></a>Tutorial: Azure Ative Directory integração única (SSO) com Concur

Neste tutorial, você vai aprender a integrar Concur com Azure Ative Direy (Azure AD). Quando integrar o Concur com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Concur.
* Permitir que os seus utilizadores sejam automaticamente inscritos para concur com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Concur única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Concur suporta **SP** iniciado SSO
* Concorda suporta **o fornecimento do** utilizador Just In Time

## <a name="adding-concur-from-the-gallery"></a>Adicionar Concur da galeria

Para configurar a integração do Concur no Azure AD, precisa adicionar Concur da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **escreva Concur** na caixa de pesquisa.
1. Selecione **Concur** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-concur"></a>Configure e teste Azure AD SSO para Concur

Configure e teste Azure AD SSO com Concur usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Concur.

Para configurar e testar a Azure AD SSO com a Concur, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
2. **[Configure Concur SSO](#configure-concur-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create Concur test user](#create-concur-test-user)** - para ter uma contraparte de B.Simon em Concur que está ligada à representação AD Ad Azure do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Concur,** encontre a secção **Gerir** e selecione **'Único sinal de s-on'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com página SAML,** clique no ícone de lápis para **configuração SAML básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://www.concursolutions.com/UI/SSO/<OrganizationId>`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<customer-domain>.concursolutions.com`

    c. Para **URL de resposta**, insira um dos seguintes padrões de URL:

    | URL de Resposta|
    |----------|
    | `https://www.concursolutions.com/SAMLRedirector/SAMLReceiver.ashx` |
    | `https://<customer-domain>.concursolutions.com/<OrganizationId>` |
    | `https://<customer-domain>.concur.com` |
    | `https://<customer-domain>.concursolutions.com` | 

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de inscrição real, o identificador e o URL de resposta. Contacte [a equipa de suporte do Cliente Concur](https://www.concur.co.in/contact) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configurar Concur,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Concur.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Concur**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-concur-sso"></a>Configurar Concur SsO

Para configurar um único sign-on no lado **Concur,** você precisa enviar os **metadados XML da Federação** descarregados e URLs copiados apropriados do portal Azure para a equipa de [suporte Concur](https://www.concur.co.in/contact). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

  > [!NOTE]
  > A configuração da sua subscrição Concur para SSO federada via SAML é uma tarefa separada, que deve contactar a [equipa de suporte do Cliente Concur](https://www.concur.co.in/contact) para realizar.

### <a name="create-concur-test-user"></a>Criar utilizador de teste Concur

Nesta secção, um utilizador chamado B.Simon é criado em Concur. A Concur suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Concur, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de inscrição do Concur, onde pode iniciar o fluxo de login. 

* Vá diretamente para o URL de inscrição do Concur e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Concur nas Minhas Apps, este será redirecionado para URL de assinatura. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Concur pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)