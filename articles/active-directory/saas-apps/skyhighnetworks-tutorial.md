---
title: 'Tutorial: Integração do Diretório Ativo Azure com a configuração SSO AD SSO da MVISION Cloud Ad | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e a Configuração SSO AD SSO da MVISION Cloud Azure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: jeedes
ms.openlocfilehash: eaf6b2526125b13eec67680c292ed1dbae6fcfee
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284435"
---
# <a name="tutorial-integrate-mvision-cloud-azure-ad-sso-configuration-with-azure-active-directory"></a>Tutorial: Integrar a configuração SSO AD AD AD da MVISION com diretório ativo Azure

Neste tutorial, você vai aprender a integrar a configuração SSO AD AD da MVISION Cloud com O Diretório Ativo Azure (Azure AD). Quando integrar a configuração SSO AD AD AD da Cloud Cloud com AD Azure, pode:

* Control em Azure AD que tem acesso à Configuração SSO AD AD Azure da Cloud Cloud.
* Ativar os seus utilizadores a serem automaticamente inscritos na Configuração SSO AD AD AZure da Cloud Cloud com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* MVISION Cloud Azure AD SSO Configuração única de sso ativada subscrição .

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* A configuração SSO AD SSO do MVISION Cloud Azure suporta sSO iniciado **SP e IDP.**

## <a name="add-mvision-cloud-azure-ad-sso-configuration-from-the-gallery"></a>Adicione a configuração SSO AD da galeria

Para configurar a integração da Configuração SSO AD AD AD da MVISION Cloud Azure em Ad AD, é necessário adicionar a configuração SSO AD AD AD da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite MVISION Cloud Azure AD SSO Configuração** na caixa de pesquisa.
1. Selecione **MVISION Cloud Ad SSO Configuração do** painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-mvision-cloud-azure-ad-sso-configuration"></a>Configure e teste Azure AD SSO para a configuração SSO AD SSO da MVISION Cloud Azure

Configure e teste Azure AD SSO com AXURE AD SSO Configuração MVISION Cloud AD usando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Configuração SSO AD AD Azure da MVISION Cloud.

Para configurar e testar O Azure AD SSO com a configuração SSO AD AD AdSO da MVISION Cloud, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
1. **[Configuração SSO de configuração MVISION Cloud Azure AD SSO](#configure-mvision-cloud-azure-ad-sso-configuration-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. Crie o utilizador de **[teste de configuração SSO MVISION Cloud Azure AD](#create-mvision-cloud-azure-ad-sso-configuration-test-user)** - para ter uma contraparte de Britta Simon na Configuração SSO AD AD Azure da MVISION Que esteja ligada à representação AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Datadog,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<ENV>.myshn.net/shndash/response/saml-postlogin`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte a equipa de [suporte do cliente de configuração MVISION Cloud Azure AD SSO](mailto:support@skyhighnetworks.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

7. Na secção **de Configuração MVISION Cloud Azure AD SSO,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon utilize o Azure single sign-on, concedendo acesso à Configuração SSO AD AD AZure da MVISION Cloud.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **MVISION Cloud Azure AD SSO Configuração**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-mvision-cloud-azure-ad-sso-configuration-sso"></a>Configuração SSO de configuração de SSO de nuvem MVISION

Para configurar um único sign-on no lado de **configuração MVISION Cloud Azure AD SSO,** é necessário enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de suporte de [configuração MVISION Cloud Azure AD SSO](mailto:support@skyhighnetworks.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-mvision-cloud-azure-ad-sso-configuration-test-user"></a>Criar utilizador de teste de configuração SSO AD SSO do MVISION Cloud Azure

Nesta secção, cria um utilizador chamado B.Simon na Configuração SSO AD AD Ad da MVISION Cloud. Trabalhe com a [equipa de suporte de configuração MVISION Cloud Ad SSO](mailto:support@skyhighnetworks.com) para adicionar os utilizadores na plataforma de configuração SSO AD AD Adure da MVISION Cloud. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o Sinal de Configuração SSO do MVISION Cloud Azure AD no URL, onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de configuração do SSO do MVISION Cloud Ad SSO e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na Configuração SSO AD AD Ad Ad para a qual configura o SSO. 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo de configuração MVISION Cloud Ad SSO nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na Configuração SSO AZURE Azure para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Assim que configurar a configuração MVISION Cloud Azure AD SSO pode impor o controlo de sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
