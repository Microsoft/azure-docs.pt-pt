---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com HowNow WebApp SSO | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o HowNow WebApp SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/06/2020
ms.author: jeedes
ms.openlocfilehash: 8d5881d838c4fe952206afb827fd60ed98dbba86
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96178335"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hownow-webapp-sso"></a>Tutorial: Azure Ative Directory integração única (SSO) com HowNow WebApp SSO

Neste tutorial, você vai aprender a integrar HowNow WebApp SSO com Azure Ative Directory (Azure AD). Quando integrar o HowNow WebApp SSO com AZure AD, pode:

* Control em Azure AD que tem acesso ao HowNow WebApp SSO.
* Capacitar os seus utilizadores a serem automaticamente inscritos no HowNow WebApp SSO com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Como O WebApp SSO único sign-on (SSO) ativou a subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* HowNow WebApp SSO suporta **SSO** iniciado SP

* HowNow WebApp SSO suporta **provisão de** utilizadores just in time

## <a name="adding-hownow-webapp-sso-from-the-gallery"></a>Adicionar HowNow WebApp SSO da galeria

Para configurar a integração do HowNow WebApp SSO em AD Azure, é necessário adicionar o HowNow WebApp SSO da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite HowNow WebApp SSO** na caixa de pesquisa.
1. Selecione **HowNow WebApp SSO** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-hownow-webapp-sso"></a>Configure e teste Azure AD SSO para HowNow WebApp SSO

Configure e teste Azure AD SSO com HowNow WebApp SSO usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no HowNow WebApp SSO.

Para configurar e testar O Azure AD SSO com HowNow WebApp SSO, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure HowNow WebApp SSO SSO](#configure-hownow-webapp-sso-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. Crie o utilizador de **[teste HowNow WebApp SSO](#create-hownow-webapp-sso-test-user)** - para ter uma contraparte de B.Simon no HowNow WebApp SSO que está ligada à representação AD Ad do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **HowNow WebApp SSO,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<CUSTOMER_NAME>.hownow.app/users/saml/sign_in`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<CUSTOMER_NAME>.hownow.app/users/saml/metadata`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<CUSTOMER_NAME>.hownow.app/users/saml/auth`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real em URL, Identifier e URL de resposta. Contacte [a equipa de suporte do Cliente HowNow WebApp SSO](mailto:support@gethownow.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na secção **Certificado de Assinatura SAML,** clique em Editar o botão **Editar** para abrir o diálogo **do Certificado de Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

1. Na secção **Certificado de Assinatura SAML,** copie o **Valor de Impressão Digital** e guarde-o no seu computador.

    ![Valor da impressão digital do polegar da cópia](common/copy-thumbprint.png)

1. Na secção **set up HowNow WebApp SSO,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao HowNow WebApp SSO.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **HowNow WebApp SSO**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-hownow-webapp-sso-sso"></a>Configurar howNow webApp SSO SSO

Para configurar um único sinal de s-on no lado **SSO do HowNow WebApp,** é necessário enviar o **Valor de Impressão Polegar** e URLs copiados apropriados do portal Azure para a equipa de suporte [howNow WebApp SSO](mailto:support@gethownow.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-hownow-webapp-sso-test-user"></a>Criar utilizador de teste SSO do HowNow WebApp

Nesta secção, um utilizador chamado Britta Simon é criado no HowNow WebApp SSO. O HowNow WebApp SSO suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no HowNow WebApp SSO, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de entrada de SSO do HowNow WebApp SSO, onde pode iniciar o fluxo de login. 

* Aceda diretamente ao URL de login do HowNow WebApp SSO e inicie o fluxo de login a partir daí.

* Pode utilizar o Microsoft Access Panel. Quando clicar no azulejo HowNow WebApp SSO no Painel de Acesso, este irá redirecionar para o URL de assinatura SSO do HowNow WebApp. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado HowNow WebApp SSO, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).