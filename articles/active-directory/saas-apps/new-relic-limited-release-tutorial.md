---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Nova Relíquia Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a New Relic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: 4cf3f9d0ae23bab4d2412b47e5841d6b8a56b65a
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327071"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Tutorial: Azure Ative Directory integração única (SSO) com Nova Relíquia

Neste tutorial, você vai aprender a integrar New Relic com Azure Ative Direy (Azure AD). Quando integrar a Nova Relíquia com AZure AD, pode:

* Controlo em Azure AD que tem acesso a Nova Relíquia.
* Permita que os seus utilizadores sejam automaticamente inscritos na Nova Relíquia com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Nova assinatura de inscrição única (SSO) ativada pela Nova Relíquia.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* A Nova Relíquia apoia **o SP e o IDP** iniciado sSO.
* Uma vez configurado New Relic, pode impor o controlo da sessão, que protege contra a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-new-relic-application-from-the-gallery"></a>Adicione nova aplicação relíquia da galeria

Para configurar a integração da Nova Relíquia no AD Azure, é necessário adicionar **New Relic (By Organization)** da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. Selecione o serviço **Azure Ative Directory.**
1. Selecione **aplicações da Enterprise**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na página **da Galeria Browse Azure AD,** escreva **Nova Relíquia (Por Organização)** na caixa de pesquisa.
1. Selecione **Nova Relíquia (Por Organização)** do painel de resultados e, em seguida, selecione **Criar**. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>Configurar e testar Azure AD SSO para nova relíquia

Configure e teste Azure AD SSO com Nova Relíquia usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Nova Relíquia.

Para configurar e testar a Azure AD SSO com nova relíquia, complete os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
   1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
   1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Novo SSO Relíquia](#configure-new-relic-sso)** - para configurar as definições de inscrição única no lado da Nova Relíquia.
   1. **[Crie um utilizador de teste de Nova Relíquia](#create-a-new-relic-test-user)** - para ter uma contrapartida para B.Simon em Nova Relíquia que esteja ligada ao utilizador Azure AD.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **New Relic by Organization,** encontre a secção **Gerir** e selecione **Single sign-on**.

1. Na página de método **de inscrição** única, selecione **SAML**.

1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração BÁSICA SAML,** preencha os valores para **O IDENTIFICADOR** e **URL de Resposta**.

   * Estes valores são recuperados usando a aplicação New Relic **My Organization.** Para utilizar esta aplicação, execute estes passos:
      1. [Faça login na](https://login.newrelic.com/) Nova Relíquia.
      1. No menu superior, selecione **Apps**.
      1. Na secção **De Aplicações,** selecione **My Organization**.
      1. Clique em **domínios de autenticação.**
      1. Escolha o domínio de autenticação ao qual deseja que o Azure AD SSO se conecte (se tiver mais de um domínio de autenticação). A maioria das empresas tem apenas um domínio de autenticação chamado **Default**. Com apenas um domínio de autenticação, não há necessidade de selecionar.
      1. Na secção **autenticação,** o **URL do consumidor de afirmação** contém o valor a utilizar para URL de **resposta**.
      1. Na secção **autenticação,** o **ID** da nossa entidade contém o valor a utilizar para **o Identificador.**

1. Na secção **"Atributos & Reclamações** do Utilizador", certifique-se de que o **identificador único** do utilizador está mapeado para um campo que contém o endereço de e-mail utilizado na Nova Relíquia.

   * O **nome de utilizador-utilizador-utilizador-utilizador padrão** funcionará para si se os seus valores forem os mesmos que os endereços de e-mail New Relic.
   * O  **utilizador de campo.o correio** eletrónico pode funcionar melhor para si se **o nome de utilizador.userprincipalname** não for o endereço de e-mail New Relic.

1. Na secção **certificado de assinatura SAML,** copie o **Url de Metadados da Federação de Aplicações** e guarde o seu valor para posterior utilização.

1. Na secção **Configurar Nova Relíquia por Organização,** copie **o URL de login** e guarde o seu valor para utilização posterior.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do portal Azure, selecione o serviço **Azure Ative Directory**
1. Selecione **Utilizadores**.
1. Para adicionar um novo utilizador, selecione **Novo utilizador** na parte superior do ecrã.
1. Na nova página do **utilizador,** siga estes passos:
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `b.simon@contoso.com`. Isto deve corresponder ao endereço de e-mail que irá utilizar no lado da Nova Relíquia.
   1. No campo **Nome**, introduza `B.Simon`.  
   1. Verifique a caixa **de verificação de passwords Show** e, em seguida, guarde o valor que é apresentado no campo **de palavra-passe inicial.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure AD um único sinal de acesso, concedendo acesso à aplicação **New Relic by Organization.**

1. A partir do portal Azure, selecione o serviço **Azure Ative Directory**
1. Selecione **aplicações da Enterprise**.
1. Na lista de candidaturas, selecione **Nova Relíquia por Organização.**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** (ou **Utilizadores** dependendo do nível do seu plano) no diálogo **'Adicionar Atribuição'.**

   ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos** (ou **Utilizadores),** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-new-relic-sso"></a>Configurar novo SSO relíquia

Siga estes passos para configurar o SSO na New Relic.

1. [Faça login na](https://login.newrelic.com/) Nova Relíquia.

1. No menu superior, selecione **Apps**.

1. Na secção **De Aplicações,** selecione **My Organization**.

1. Clique em **domínios de autenticação.**

1. Escolha o domínio de autenticação ao qual deseja que o Azure AD SSO se conecte (se tiver mais de um domínio de autenticação). A maioria das empresas tem apenas um domínio de autenticação chamado **Default**. Com apenas um domínio de autenticação, não há necessidade de selecionar.

1. Na secção **Autenticação,** clique em **Configurar**.

   1. No campo **Fonte de metadados SAML,** insira o valor que guardou anteriormente do campo de **url de metadados da Federação de Metadados** Azure.

   1. No campo **URL alvo SSO,** insira o valor que guardou anteriormente do campo URL de **login** do lado Azure.

   1. Clique **em Guardar** guardar as definições para verificar se as definições ficam bem tanto nos lados AD AD azure como em Nova Relíquia. Se ambos os lados não estiverem devidamente configurados, os seus utilizadores não poderão iniciar sessão em Nova Relíquia.

### <a name="create-a-new-relic-test-user"></a>Criar um novo utilizador de teste de relíquia

Nesta secção, cria-se um utilizador chamado B.Simon em Nova Relíquia. Siga estes passos para criar o utilizador.

1. [Faça login na](https://login.newrelic.com/) Nova Relíquia.

1. No menu superior, selecione **Apps**.

1. Na secção **de aplicações,** selecione **Gestão do Utilizador**.

1. Clique no botão **Adicionar utilizador.**

   1. No campo **Nome,** insira **B.Simon**.
   
   1. No campo **Email,** insira o valor que será enviado pela Azure AD SSO.
   
   1. Escolha um **Tipo de** utilizador e um grupo **de** utilizador para o utilizador. Para um utilizador de teste, **o Utilizador Básico** para Tipo e **Utilizador** para Grupo são escolhas razoáveis.
   
   1. Clique **em Adicionar Utilizador** para salvar o utilizador.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar na **Nova Relíquia por Organização** no Painel de Acesso, deverá ser automaticamente inscrito em Nova Relíquia. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente nova relíquia com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
