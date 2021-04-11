---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com o Painel de Instrumento GlobalLink da TransPerfect | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o TransPerfect GlobalLink Dashboard.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: jeedes
ms.openlocfilehash: ad720f5e625d2054a5c79b47c9659d4d10d251e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955113"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-transperfect-globallink-dashboard"></a>Tutorial: Azure Ative Directory integração única (SSO) com o Painel Deso GlobalLink da TransPerfect

Neste tutorial, você vai aprender a integrar transPerfect GlobalLink Dashboard com Azure Ative Directory (Azure AD). Quando integrar o Painel GlobalLink da TransPerfect com Azure AD, pode:

* Control em Azure AD que tem acesso ao Painel GlobalLink da TransPerfect.
* Permita que os seus utilizadores sejam automaticamente inscritos no TransPerfect GlobalLink Dashboard com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* TransPerfect GlobalLink Dashboard única assinatura ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* O Painel Desemfins GlobalLink suporta sSO iniciado **sp e IDP.**
* O Painel Desafect GlobalLink suporta o fornecimento do utilizador **Just In Time.**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-transperfect-globallink-dashboard-from-the-gallery"></a>Adicionar o Painel TransPerfect GlobalLink da galeria

Para configurar a integração do Painel GlobalLink da TransPerfect em AD Azure, é necessário adicionar o Painel Desafiado GlobalLink da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** digite **TransPerfect GlobalLink Dashboard** na caixa de pesquisa.
1. Selecione **TransPerfect GlobalLink Dashboard** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-transperfect-globallink-dashboard"></a>Configure e teste Azure AD SSO para o Painel GlobalLink da TransPerfect

Configure e teste Azure AD SSO com TransPerfect GlobalLink Dashboard usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Painel GlobalLink da TransPerfect.

Para configurar e testar o Azure AD SSO com o Painel GlobalLink da TransPerfect, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o TransPerfect GlobalLink Dashboard SSO](#configure-transperfect-globallink-dashboard-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. Crie o utilizador de **[teste TransPerfect GlobalLink Dashboard](#create-transperfect-globallink-dashboard-test-user)** - para ter uma contraparte de B.Simon no TransPerfect GlobalLink Dashboard que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **TransPerfect GlobalLink Dashboard,** encontre a secção **Gerir** e selecione **um único sinal de sposição**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure.

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite o URL:  `https://sso.transperfect.com`

1. Clique em **Guardar**.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** clique no botão de cópia para copiar o Url de **metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)
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

Nesta secção, você permitirá que B.Simon utilize o Azure single sign-on, concedendo acesso ao Painel GlobalLink da TransPerfect.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **TransPerfect GlobalLink Dashboard**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-transperfect-globallink-dashboard-sso"></a>Configurar transPerfect GlobalLink Dashboard SSO

Para configurar um único sinal no lado **do painel de instrumentos TransPerfect GlobalLink,** é necessário enviar o **url de metadados da Federação de Aplicações** para a [equipa de suporte do Painel GlobalLink da TransPerfect](mailto:TechOps_Consulting@transperfect.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-transperfect-globallink-dashboard-test-user"></a>Criar utilizador de teste transPerfect GlobalLink Dashboard

Nesta secção, um utilizador chamado B.Simon é criado no Painel GlobalLink da TransPerfect. O TransPerfect GlobalLink Dashboard suporta o provisionamento just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no TransPerfect GlobalLink Dashboard, é criado um novo quando tenta aceder ao TransPerfect GlobalLink Dashboard.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o Sinal do Painel de Instrumento GlobalLink transPerfect no URL, onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de sinal de sign-on do Painel De Sinais do TransPerfect GlobalLink e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Painel Desatado GlobalLink para o qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo do Painel Desperfect GlobalLink nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Painel Desafinado GlobalLink para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o TransPerfect GlobalLink Dashboard, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).