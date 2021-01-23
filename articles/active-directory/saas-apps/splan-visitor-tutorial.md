---
title: 'Tutorial: Integre o Azure Ative Directory single sign-on (SSO) com Splan Visitor | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Splan Visitor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/14/2020
ms.author: jeedes
ms.openlocfilehash: ed136d06f18190124abe4598d580f40e41bf8592
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704224"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-splan-visitor"></a>Tutorial: Integrar o Azure Ative Directory single sign-on (SSO) com O Visitante Splan

Neste tutorial, você vai aprender a integrar o Splan Visitor com O Azure Ative Directory (Azure AD). Quando integrar o Visitante Splan com Azure AD, pode:

* Utilize a Azure AD para controlar quem tem acesso ao Visitante Splan.
* Permitir que os utilizadores sejam automaticamente inscritos no Splan Visitor com as suas contas AD Azure.
* Gerencie as suas contas numa localização central, o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa do seguinte:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura ativada por Splan Visitor (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar Azure AD SSO em um ambiente de teste.

O Splan Visitor suporta sSO iniciado pelo IdP.

## <a name="add-splan-visitor-from-the-gallery"></a>Adicione visitante Splan da galeria

Para configurar a integração do Splan Visitor em Azure AD, adicione o Visitante Splan da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione o serviço **Azure Ative Directory.**
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **insira** o Visitante Splan na caixa de pesquisa.
1. Selecione **Splan Visitor** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-splan-visitor"></a>Configure e teste Azure AD SSO para visitante splan

Configure e teste Azure AD SSO com Splan Visitor utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Splan Visitor.

Para configurar e testar a Azure AD SSO com o Visitante Splan, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o único sinal de Azure AD com o utilizador de teste B.Simon.
    1. **[Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que a B.Simon utilize um único sinal de Ad AD.
1. **[Configure o Splan Visitor SSO](#configure-splan-visitor-sso)** para configurar as definições de inscrição única com o Visitante Splan.
    1. **[Crie um utilizador de teste Splan Visitor](#create-a-splan-visitor-test-user)** para ter uma contraparte de B.Simon no Splan Visitor que esteja ligada à representação Azure AD do utilizador.
1. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir que o Azure AD SSO no portal Azure:

1. No portal Azure, na página de integração da aplicação **do Splan Visitor,** encontre a secção **Gerir** e selecione um único sinal de **saúde.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. Na **configuração de Sign-On com** a página SAML, selecione o ícone **de edição/caneta** para **configuração SAML básica** para editar as definições.

   ![Screenshot realçando o ícone de edição/caneta para configuração SAML básica.](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** a aplicação está pré-configurada e os URLs necessários são pré-carregados com Azure. Selecione o botão **Guardar** para guardar a configuração.

1. Na **configuração de um único sinal com** a página SAML, na secção certificado de assinatura **SAML,** encontre **o Metadados da Federação XML**. Selecione **Baixar** para descarregar o certificado e guardá-lo para o seu computador.

    ![Screenshot realçando o link de descarregamento XML dos metadados da Federação.](common/metadataxml.png)

1. Na secção Configurar O **Visitante Splan,** copie os URL ou URLs apropriados com base no seu requisito.

    ![Screenshot realçando a secção URLs de configuração.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste chamado B.Simon no portal Azure.

1. No painel esquerdo do portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome,** insira **B.Simon**.  
   1. No campo **nome do utilizador,** insira o seu nome de utilizador em _username@companydomain.extension_ formato. Por exemplo, insira **B.Simon@contoso.com** .
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, permitindo o acesso ao Visitante Splan.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Splan Visitor** para abrir a visão geral da aplicação.
1. Encontre a secção **Gerir** e, em seguida, selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Adicionar Atribuição'.**
1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista **de Utilizadores** e, em seguida, clique em **Selecionar** na parte inferior do ecrã.
1. Se o utilizador lhe for atribuída uma função, selecione-a no menu **Select a role** drop-down. Se não tiver sido criada qualquer função para esta aplicação, deixe a função **de Acesso Predefinido** selecionada.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

## <a name="configure-splan-visitor-sso"></a>Configure Splan Visitante SSO

Para configurar um único sign-on com o Splan Visitor, envie o **Metadadata XML da Federação** que descarregou e adfiou URLs copiados do portal Azure para a equipa de [apoio ao Visitante Splan](mailto:support@splan.com). Isto garante que a ligação SSO SAML está corretamente definida em ambos os lados.

### <a name="create-a-splan-visitor-test-user"></a>Criar um utilizador de teste splan visitor

Crie um utilizador de teste chamado **Britta Simon** em Splan Visitor. Trabalhe com a [equipa de apoio ao Visitante Splan](mailto:support@splan.com) para adicionar o utilizador ao Visitante Splan. Tem de criar e ativar o utilizador antes de utilizar uma única sessão de sessão.

## <a name="test-sso"></a>Teste SSO

Teste a sua configuração de sinal único AD AD com uma das seguintes opções:

* **Portal Azure**: Selecione **Teste esta aplicação** para iniciar súmuferamente no Visitante Splan para o qual configura SSO.
* **Microsoft My Apps portal**: Selecione o azulejo **do Visitante Splan** para iniciar automaticamente o splan visitante para o qual configura sSO. Para obter mais informações sobre o portal My Apps, consulte [iniciar sôr-se e iniciar aplicações a partir do portal My Apps.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Próximos passos

Depois de configurar o Splan Visitor, pode [aprender a impor controlos de sessão no Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app). Os controlos de sessão ajudam a proteger a exfiltração e a infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional.
