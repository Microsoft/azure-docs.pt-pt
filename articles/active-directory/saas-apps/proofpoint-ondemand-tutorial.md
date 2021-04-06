---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Proofpoint on Demand | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Proofpoint on Demand.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/29/2021
ms.author: jeedes
ms.openlocfilehash: e9beb2c9e265982a9e10f3e6abee6bc3cba4a4a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648463"
---
# <a name="tutorial-azure-active-directory-integration-with-proofpoint-on-demand"></a>Tutorial: Integração do Diretório Ativo Azure com o Proofpoint on Demand

Neste tutorial, você aprenderá a integrar o Proofpoint on Demand com o Azure Ative Directory (Azure AD). Quando integrar o Proofpoint on Demand com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Proofpoint on Demand.
* Permitir que os seus utilizadores sejam automaticamente inscritos no Proofpoint on Demand com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Proofpoint on Demand single sign-on (SSO) ativada subscrição.

> [!NOTE]
> Se estiver a utilizar a autenticação MFA ou sem palavra-passe com Azure AD, desligue o valor AuthnContext no Pedido SAML. Caso contrário, a Azure AD lançará o erro no desfasamento do AuthnContext e não enviará o símbolo de volta para a aplicação.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Proofpoint on Demand suporta **SSO** iniciado sp

## <a name="add-proofpoint-on-demand-from-the-gallery"></a>Adicione o Ponto de Prova a pedido da galeria

Para configurar a integração do Proofpoint on Demand em Azure AD, é necessário adicionar o Proofpoint on Demand da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** escreva **Ponto de Prova sobre Procuração** na caixa de pesquisa.
1. Selecione **Proofpoint on Demand** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-proofpoint-on-demand"></a>Configure e teste Azure AD SSO para ponto de prova a pedido

Configure e teste Azure AD SSO com Proofpoint on Demand usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Proofpoint on Demand.

Para configurar e testar a Azure AD SSO com o Proofpoint on Demand, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o ponto de prova no Demand SSO](#configure-proofpoint-on-demand-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Proofpoint on Demand test user](#create-proofpoint-on-demand-test-user)** - para ter uma contraparte de B.Simon in Proofpoint on Demand que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página **proofpoint on Demand** application integration, encontre a secção **Gerir** e selecione um único sinal **de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Ponto de prova no domínio da procura e nos URLs informações únicas de assinatura](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<hostname>.pphosted.com/ppssamlsp_hostname`

    b. Na caixa **identifier,** digite um URL utilizando o seguinte padrão: `https://<hostname>.pphosted.com/ppssamlsp`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<hostname>.pphosted.com:portnumber/v1/samlauth/samlconsumer`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Sign-On real, identifier e responder URL. Contacte [a equipa de suporte](https://www.proofpoint.com/us/support-services) do Cliente à Procura para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Desaprovação de Configuração na Secção Demanda,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Proofpoint on Demand.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Proofpoint on Demand**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="configure-proofpoint-on-demand-sso"></a>Configure ponto de prova sobre sSO de procura

Para configurar um único sinal de sinal no lado **do Proofpoint no** lado da Demand, é necessário enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de [suporte Proofpoint on Demand](https://www.proofpoint.com/us/support-services). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-proofpoint-on-demand-test-user"></a>Criar ponto de prova no utilizador de teste de procura

Nesta secção, cria-se um utilizador chamado Britta Simon in Proofpoint on Demand. Trabalhe com a equipa de suporte do [Cliente Proofpoint on Demand](https://www.proofpoint.com/us/support-services) para adicionar utilizadores na plataforma Proofpoint on Demand.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Proofpoint no URL de inscrição de pedido, onde pode iniciar o fluxo de login. 

* Vá diretamente ao Proofpoint on Demand'on URL e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no telha Proofpoint on Demand nas Minhas Apps, deverá ser automaticamente inscrito no Ponto de Observação a Pedido para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Proofpoint on Demand, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).