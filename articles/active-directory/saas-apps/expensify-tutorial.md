---
title: 'Tutorial: Integração do Diretório Ativo Azure com Expensify | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Expensify.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: jeedes
ms.openlocfilehash: 5c68453f7f7d638877683920a17022cf3d29bdc5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597815"
---
# <a name="tutorial-integrate-expensify-with-azure-active-directory"></a>Tutorial: Integrar Expensify com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar Expensify com Azure Ative Diretório (Azure AD). Quando integrar Expensify com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Expensify.
* Permitir que os seus utilizadores sejam automaticamente inscritos para Expensificar com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Expensifique a subscrição ativada por um único sinal (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Expensificar suporta sSO iniciado **SP.**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="add-expensify-from-the-gallery"></a>Adicione Expensify da galeria

Para configurar a integração do Expensify no AD AZure, é necessário adicionar Expensify da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Expensify** na caixa de pesquisa.
1. Selecione **Expensify** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-expensify"></a>Configure e teste Azure AD SSO para expensificar

Configure e teste Azure AD SSO com Expensify usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Expensify.

Para configurar e testar a Azure AD SSO com expensificar, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
2. **[Configure Expensify SSO](#configure-expensify-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Crie Expensify utilizador](#create-expensify-test-user)** de teste - para ter uma contraparte de B.Simon em Expensify que está ligada à representação AD Azure do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **Expensify,** encontre a secção **Gerir** e selecione **'Único sinal de súplica'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com página SAML,** clique no ícone de lápis para **configuração SAML básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** o URL: `https://www.expensify.com/authentication/saml/login`

    b. Na caixa de texto **identifier (Entity ID),** digite o URL: `https://www.expensify.com`

    c. b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://www.expensify.com/authentication/saml/loginCallback?domain=<yourdomain>`

    > [!NOTE]
    > O valor URL de resposta não é real. Atualize este valor com o URL de resposta real. Contacte a equipa de [apoio ao Cliente](mailto:help@expensify.com) para obter este valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **metadata XML** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configuração Expensify,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Expensify.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Expensify**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-expensify-sso"></a>Configure Expensify SSO

Para ativar o SSO em Expensify, primeiro tem de ativar o **Controlo de Domínio** na aplicação. Pode ativar o Controlo de Domínio na aplicação através dos passos listados [aqui.](https://help.expensify.com/domain-control) Para apoio adicional, trabalhe com [a equipa de apoio ao Cliente Expensify.](mailto:help@expensify.com) Assim que tiver o Controlo de Domínio ativo, siga estes passos:

![Configurar Sign-On Individuais](./media/expensify-tutorial/domain-control.png)

1. Inscreva-se na sua aplicação Expensify.

2. No painel esquerdo, clique em **Definições** e navegue para **SAML**.

3. Alterne a opção **de Login SAML** conforme **Ativado**.

4. Abra os metadados da Federação descarregados a partir do Azure AD no bloco de notas, copie o conteúdo e, em seguida, cole-os na caixa de texto **do Fornecedor de Identidade Metadados.**

### <a name="create-expensify-test-user"></a>Criar Expensify utilizador de teste

Nesta secção, cria-se um utilizador chamado B.Simon in Expensify. Trabalhe com [a equipa de suporte do Cliente Expensify](mailto:help@expensify.com) para adicionar os utilizadores na plataforma Expensify.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Expensify Sign-on URL onde pode iniciar o fluxo de login. 

* Vá para Expensificar o URL de inscrição diretamente e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Expensify nas Minhas Aplicações, este será redirecionado para Expensify Sign-on URL. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado, o Expensify pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).