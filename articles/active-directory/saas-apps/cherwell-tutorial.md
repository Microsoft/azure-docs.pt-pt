---
title: 'Tutorial: Integração do Diretório Ativo Azure com Cherwell | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Cherwell.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/14/2021
ms.author: jeedes
ms.openlocfilehash: 5e2cf4ac43fa2a828ebe9e9a5cb5d2e1d630a59a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101649386"
---
# <a name="tutorial-azure-active-directory-integration-with-cherwell"></a>Tutorial: Integração do Diretório Ativo Azure com Cherwell

Neste tutorial, você vai aprender a integrar Cherwell com Azure Ative Directory (Azure AD). Quando integrar Cherwell com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Cherwell.
* Permita que os seus utilizadores sejam automaticamente inscritos na Cherwell com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com a Cherwell, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

* Assinatura ativada por Cherwell.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Cherwell apoia **SP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="add-cherwell-from-the-gallery"></a>Adicione Cherwell da galeria

Para configurar a integração de Cherwell em Azure AD, você precisa adicionar Cherwell da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **da galeria,** **digite Cherwell** na caixa de pesquisa.
1. Selecione **Cherwell** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-cherwell"></a>Configure e teste Azure AD SSO para Cherwell

Configure e teste Azure AD SSO com Cherwell usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Cherwell.

Para configurar e testar a Azure AD SSO com Cherwell, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    2. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
2. **[Configure Cherwell SSO](#configure-cherwell-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create Cherwell test user](#create-cherwell-test-user)** - para ter uma contraparte de B.Simon em Cherwell que está ligada à representação AZure AD do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Cherwell,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<companyname>.cherwellondemand.com/cherwellclient`

    b. Na caixa de texto **URL de resposta,** digite o URL utilizando o seguinte padrão: `https://*.cherwellondemand.com`
    
    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL e URL de resposta de assinatura real. Contacte a [equipa de apoio ao Cliente Cherwell](https://cherwellsupport.com/CherwellPortal) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar Cherwell,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste chamado B.Simon no portal Azure.

1. No painel esquerdo do portal Azure, selecione **O Diretório Ativo Azure,** selecione **Utilizadores,** e, em seguida, selecione **Todos os utilizadores**.
1. Na parte superior do ecrã, selecione **Novo utilizador**.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome,** insira **B.Simon**.  
   1. No campo **nome do utilizador,** insira `<username>@<companydomain>.<extension>` . Por exemplo: `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe Show** e, em seguida, tome nota do valor que é apresentado na caixa **palavra-passe.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Cherwell.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Cherwell.**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-cherwell-sso"></a>Configurar Cherwell Sso

Para configurar um único sign-on no lado **cherwell,** você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de [apoio cherwell](https://cherwellsupport.com/CherwellPortal). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

> [!NOTE]
> A tua equipa de apoio cherwell tem de fazer a configuração SSO real. Receberá uma notificação quando o SSO tiver sido ativado para a sua subscrição.

### <a name="create-cherwell-test-user"></a>Criar utilizador de teste cherwell

Para permitir que os utilizadores da Azure AD inscrevam-se em Cherwell, devem ser adsajados em Cherwell. No caso da Cherwell, as contas de utilizador precisam de ser criadas pela sua equipa de [apoio cherwell.](https://cherwellsupport.com/CherwellPortal)

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador cherwell ou APIs fornecidas pela Cherwell para fornecer contas de utilizador do Azure Ative Directory.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de inscrição de Cherwell, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição de Cherwell e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo cherwell nas Minhas Apps, deverá ser automaticamente inscrito no Cherwell para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Cherwell, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)