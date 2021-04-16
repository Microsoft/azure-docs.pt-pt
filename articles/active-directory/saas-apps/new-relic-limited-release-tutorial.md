---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a Nova Relíquia | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a New Relic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: jeedes
ms.openlocfilehash: 8c0ffe4affb6b30f2e2a1aa97a0f4795c130f59b
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517611"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Tutorial: Azure Ative Directory integração única (SSO) com Nova Relíquia

Neste tutorial, você vai aprender a integrar New Relic com Azure Ative Direy (Azure AD). Quando integrar a Nova Relíquia com AZure AD, pode:

* Controlo em Azure AD que tem acesso a Nova Relíquia.
* Permita que os seus utilizadores sejam automaticamente inscritos na Nova Relíquia com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa do seguinte:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma nova subscrição de Relíquia que está ativada para um único sign-on (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* A New Relic suporta sSO que é iniciado pelo prestador de serviços ou pelo fornecedor de identidade.

## <a name="add-new-relic-from-the-gallery"></a>Adicione nova relíquia da galeria

Para configurar a integração da Nova Relíquia no AD Azure, é necessário adicionar **New Relic (By Organization)** da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. Selecione o serviço **Azure Ative Directory.**
1. Selecione **aplicações da Empresa**  >  **Nova aplicação**.
1. Na página **da Galeria Browse Azure AD,** escreva **Nova Relíquia (Por Organização)** na caixa de pesquisa.
1. Selecione **Nova Relíquia (Por Organização)** a partir dos resultados e, em seguida, selecione **Criar**. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>Configurar e testar Azure AD SSO para nova relíquia

Configure e teste Azure AD SSO com Nova Relíquia utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação ligada entre um utilizador Azure AD e o utilizador relacionado em Nova Relíquia.

Para configurar e testar Azure AD SSO com Nova Relíquia:

1. [Configure Azure AD SSO](#configure-azure-ad-sso) para permitir que os seus utilizadores utilizem esta funcionalidade.
   1. [Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user) para testar o Azure AD com B.Simon.
   1. [Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que a B.Simon utilize um único sinal de Ad AD.
1. [Configure o Novo SSO relicílico](#configure-new-relic-sso) para configurar as definições de inscrição única no lado da Nova Relíquia.
   1. [Crie um novo utilizador de teste de relíquia](#create-a-new-relic-test-user) para ter uma contrapartida para B.Simon em Nova Relíquia ligada ao utilizador Azure AD.
1. [Teste SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure na página de integração de aplicações **Da Nova Relíquia pela Organização,** encontre a secção **Gerir.** Em seguida, selecione **Single sign-on**.

1. Na página de método **de inscrição** única, selecione **SAML**.

1. Na **configuração single Sign-On com** a página SAML, selecione o ícone de lápis para **configuração SAML básica** para editar as definições.

   ![Screenshot de Configurar Single Sign-On com SAML, com ícone de lápis em destaque.](common/edit-urls.png)

1. Na secção **de Configuração BÁSICA SAML,** preencha os valores para **O IDENTIFICADOR** e **URL de Resposta**.

   * Recupere estes valores utilizando a aplicação New Relic **My Organization.** Para utilizar esta aplicação:
      1. [Inscreva-se na](https://login.newrelic.com/) Nova Relíquia.
      1. No menu superior, selecione **Apps**.
      1. Na secção **de aplicações,** selecione **os**  >  **domínios de autenticação da** Minha Organização .
      1. Escolha o domínio de autenticação ao qual pretende que o Azure AD SSO se conecte (se tiver mais de um domínio de autenticação). A maioria das empresas tem apenas um domínio de autenticação chamado **Default**. Se houver apenas um domínio de autenticação, não precisa de selecionar nada.
      1. Na secção **autenticação,** o **URL do consumidor de afirmação** contém o valor a utilizar para URL de **resposta**.
      1. Na secção **autenticação,** o **ID** da nossa entidade contém o valor a utilizar para **o Identificador.**

1. Na secção **"Atributos & Reclamações** do Utilizador", certifique-se de que o **identificador único** do utilizador está mapeado para um campo que contém o endereço de e-mail utilizado na Nova Relíquia.

   * O **nome de utilizador-utilizador padrão** funcionará para si se os seus valores forem os mesmos que os endereços de e-mail New Relic.
   * O  **site de correio eletrónico do** campo pode funcionar melhor para si se o nome de **utilizador.userprincipalname** não for o endereço de e-mail New Relic.

1. Na secção **certificado de assinatura SAML,** copie o **Url de Metadados da Federação de Aplicações** e guarde o seu valor para posterior utilização.

1. Na secção **Configurar Nova Relíquia por Organização,** copie **o URL de login** e guarde o seu valor para posterior utilização.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a New Relic.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Nova Relíquia.**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-new-relic-sso"></a>Configurar novo SSO relíquia

Siga estes passos para configurar o SSO na New Relic.

1. [Inscreva-se na](https://login.newrelic.com/) Nova Relíquia.

1. No menu superior, selecione **Apps**.

1. Na secção **de aplicações,** selecione **os**  >  **domínios de autenticação da** Minha Organização .

1. Escolha o domínio de autenticação ao qual pretende que o Azure AD SSO se conecte (se tiver mais de um domínio de autenticação). A maioria das empresas tem apenas um domínio de autenticação chamado **Default**. Se houver apenas um domínio de autenticação, não precisa de selecionar nada.

1. Na secção **De Autenticação,** selecione **Configure**.

   1. Para **a Fonte de metadados SAML,** insira o valor que guardou anteriormente no campo url de **metadados da Federação de Metadados** Azure AD.

   1. Para **URL alvo SSO,** insira o valor que guardou anteriormente do campo **URL de login** Azure.

   1. Depois de verificar se as definições ficam bem tanto nos lados AD AD e New Relic, **selecione Save**. Se ambos os lados não estiverem devidamente configurados, os seus utilizadores não poderão iniciar seducação na Nova Relíquia.

### <a name="create-a-new-relic-test-user"></a>Criar um novo utilizador de teste de relíquia

Nesta secção, cria-se um utilizador chamado B.Simon em Nova Relíquia.

1. [Inscreva-se na](https://login.newrelic.com/) Nova Relíquia.

1. No menu superior, selecione **Apps**.

1. Na secção **de aplicações,** selecione **Gestão do Utilizador**.

1. Selecione **Adicionar utilizador**.

   1. Para **nome**, insira **B.Simon**.
   
   1. Para **e-mail,** insira o valor que será enviado pela Azure AD SSO.
   
   1. Escolha um **Tipo de** utilizador e um grupo **de** utilizador para o utilizador. Para um utilizador de teste, **o Utilizador Básico** para Tipo e **Utilizador** para Grupo são escolhas razoáveis.
   
   1. Para salvar o utilizador, **selecione Adicionar Utilizador**.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Novo Sinal de Relíquia no URL onde pode iniciar o fluxo de login.  

* Vá diretamente para o URL de inscrição de nova relíquia e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na Nova Relíquia para a qual configura o SSO. 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo new relicílico nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na Nova Relíquia para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado New Relic, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
