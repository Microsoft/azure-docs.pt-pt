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
ms.openlocfilehash: 29e19eea51b5ee55831bf1d694a9a6473a62d471
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504054"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Tutorial: Azure Ative Directory integração única (SSO) com Nova Relíquia

Neste tutorial, você vai aprender a integrar New Relic com Azure Ative Direy (Azure AD). Quando integrar a Nova Relíquia com AZure AD, pode:

* Controlo em Azure AD que tem acesso a Nova Relíquia.
* Permita que os seus utilizadores sejam automaticamente inscritos na Nova Relíquia com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre software como integração de aplicações de serviço (SaaS) com Azure AD, consulte O que é o acesso à [aplicação e um único acesso com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa do seguinte:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma nova subscrição de Relíquia que está ativada para um único sign-on (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* A New Relic suporta sSO que é iniciado pelo prestador de serviços ou pelo fornecedor de identidade.
* Depois de configurar a Nova Relíquia, pode impor o controlo da sessão, que protege contra a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-new-relic-from-the-gallery"></a>Adicione nova relíquia da galeria

Para configurar a integração da Nova Relíquia no AD Azure, é necessário adicionar **New Relic (By Organization)** da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
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

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **New Relic by Organization,** encontre a secção **Gerir.** Em seguida, selecione **Single sign-on**.

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

Eis como criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do portal Azure, selecione **Azure Ative Directory**.
1. Selecione **Utilizadores**  >  **Novo utilizador**.
1. Na página do **novo utilizador:**
   1. No campo **nome do utilizador,** insira o `username@companydomain.extension` . Por exemplo, `b.simon@contoso.com`. Isto deve corresponder ao endereço de e-mail que usará no lado da Nova Relíquia.
   1. No campo **Nome**, introduza `B.Simon`.  
   1. Selecione **Mostrar a palavra-passe** e, em seguida, guarde o valor que é mostrado.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Eis como permitir que b.Simon use a Azure AD um único sinal de acesso, concedendo acesso à aplicação New Relic by Organization.

1. A partir do portal Azure, selecione **Azure Ative Directory**.
1. Selecione **aplicações**  >  **Da Empresa Nova Relíquia por Organização**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![Screenshot da secção Manage, com Utilizadores e grupos em destaque.](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Em **Add Assignment**, selecione **Utilizadores e grupos** (ou **Utilizadores**, dependendo do nível do seu plano).

   ![Screenshot da opção de utilizador Adicionar.](common/add-assign-user.png)

1. Em **Utilizadores e grupos** (ou **Utilizadores),** selecione **B.Simon** da lista **de Utilizadores** e, em seguida, escolha **Selecione** na parte inferior do ecrã.
1. In **Add Assignment**, selecione **Atribua**.

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

Aqui está como testar a sua configuração de sinal de Ad AD única Azure usando o Painel de Acesso.

Quando selecionar **Nova Relíquia por Organização** no Painel de Acesso, deverá ser automaticamente inscrito em Nova Relíquia. Para obter mais informações sobre o Painel de Acesso, consulte [iniciar sôr-se e iniciar aplicações a partir do portal My Apps.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](./tutorial-list.md) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente nova relíquia com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
