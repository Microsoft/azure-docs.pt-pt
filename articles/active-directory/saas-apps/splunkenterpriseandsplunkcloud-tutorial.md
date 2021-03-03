---
title: 'Tutorial: Integração do Azure Ative Directory com a Splunk Enterprise e a Splunk Cloud | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Splunk Enterprise e a Splunk Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: 81107b3655ae86d51e36445ce46661d553ab3b5a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101649864"
---
# <a name="tutorial-azure-active-directory-integration-with-splunk-enterprise-and-splunk-cloud"></a>Tutorial: Integração do Azure Ative Directory com a Splunk Enterprise e a Splunk Cloud

Neste tutorial, você vai aprender a integrar a Splunk Enterprise e a Splunk Cloud com o Azure Ative Directory (Azure AD). Quando integrar a Splunk Enterprise e a Splunk Cloud com a Azure AD, pode:

* Controle em Azure AD que tem acesso à Splunk Enterprise e à Splunk Cloud.
* Permita que os seus utilizadores sejam automaticamente inscritos na Splunk Enterprise e splunk Cloud com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Splunk Enterprise e Splunk Cloud única assinatura ativada (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Splunk Enterprise e Splunk Cloud suportam **SSO** iniciado SP

## <a name="add-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>Adicione Splunk Enterprise e Splunk Cloud da galeria

Para configurar a integração da Splunk Enterprise e splunk Cloud em Azure AD, você precisa adicionar Splunk Enterprise e Splunk Cloud da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **de galeria,** **escreva Splunk Enterprise e Splunk Cloud** na caixa de pesquisa.
1. Selecione **Splunk Enterprise e Splunk Cloud** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-splunk-enterprise-and-splunk-cloud"></a>Configure e teste Azure AD SSO para Splunk Enterprise e Splunk Cloud

Configure e teste Azure AD SSO com Splunk Enterprise e Splunk Cloud usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Splunk Enterprise e Splunk Cloud.

Para configurar e testar Azure AD SSO com Splunk Enterprise e Splunk Cloud, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Splunk Enterprise e a Splunk Cloud SSO](#configure-splunk-enterprise-and-splunk-cloud-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Splunk Enterprise e Splunk Cloud test user](#create-splunk-enterprise-and-splunk-cloud-test-user)** - para ter uma contraparte de B.Simon em Splunk Enterprise e Splunk Cloud que está ligada à representação AD AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Splunk Enterprise e Splunk Cloud,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)
4. Na secção **de Configuração Básica SAML,** execute o seguinte padrão:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<splunkserverUrl>/app/launcher/home`

    b. Na caixa **identifier,** digite um URL utilizando o seguinte padrão: `<splunkserverUrl>`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<splunkserver>/saml/acs`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Sign-On real, identifier e responder URL. Contacte [a Splunk Enterprise e a splunk Cloud Client support team](https://www.splunk.com/en_us/about-splunk/contact-us.html) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Splunk Enterprise e Splunk Cloud.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Splunk Enterprise e Splunk Cloud**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-splunk-enterprise-and-splunk-cloud-sso"></a>Configure Splunk Enterprise e Splunk Cloud SSO

  Para configurar um único sign-on no lado **da Splunk Enterprise e splunk Cloud,** você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a [splunk Enterprise e splunk cloud equipe de suporte](https://www.splunk.com/en_us/about-splunk/contact-us.html). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-splunk-enterprise-and-splunk-cloud-test-user"></a>Criar utilizador de teste Splunk Enterprise e Splunk Cloud

Nesta secção, cria-se um utilizador chamado Britta Simon em Splunk Enterprise e Splunk Cloud. Trabalhe com [a equipa de suporte splunk Enterprise e Splunk Cloud](https://www.splunk.com/en_us/about-splunk/contact-us.html) para adicionar os utilizadores na plataforma Splunk Enterprise e Splunk Cloud. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Splunk Enterprise e SPLunk Cloud Sign-on URL onde você pode iniciar o fluxo de login. 

* Vá diretamente à Splunk Enterprise e splunk Cloud Sign-on URL e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Splunk Enterprise e Splunk Cloud nas My Apps, este será redirecionado para Splunk Enterprise e SPLunk Cloud Sign-on URL. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado a Splunk Enterprise e a Splunk Cloud, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app)