---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com AwareGo Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o AwareGo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/07/2020
ms.author: jeedes
ms.openlocfilehash: 6599abd6282d0d1eb7cb81002c34ddd5158dab6b
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511064"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-awarego"></a>Tutorial: Azure Ative Directy integração única com AwareGo

Neste tutorial, você vai aprender a integrar AwareGo com Azure Ative Directory (Azure AD). Quando integra o AwareGo com a AD Azure, pode:

* Controle em Azure AD que tem acesso ao AwareGo.
* Permita que os seus utilizadores sejam automaticamente inscritos no AwareGo com as suas contas AD Azure.
* Gerencie as suas contas numa localização central, o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura ativada por Um único sinal de acesso awareGo (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. O AwareGo suporta um SSO iniciado por um prestador de serviços (SP).


## <a name="adding-awarego-from-the-gallery"></a>Adicionar AwareGo da galeria

Para configurar a integração do AwareGo no AD Azure, é necessário adicionar o AwareGo da galeria à sua lista de aplicações geridas como um serviço (SaaS).

1. Inscreva-se no portal Azure utilizando uma conta de trabalho, uma conta escolar ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione o serviço **Azure Ative Directory.**
1. Selecione **Aplicações empresariais**  >  **todas as aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **escreva AwareGo** na caixa de pesquisa.
1. No painel de resultados, selecione **AwareGo** e, em seguida, adicione a aplicação. Em poucos segundos, a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-awarego"></a>Configure e teste Azure AD SSO para AwareGo

Configure e teste Azure AD SSO com AwareGo utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no AwareGo.

Para configurar e testar a Azure AD SSO com AwareGo, faça o seguinte:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.  

    a. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o único sinal de Azure AD com o utilizador B.Simon.  
    b. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir ao utilizador B.Simon utilizar um único sinal de Azure.  

1. **[Configure o AwareGo SSO](#configure-awarego-sso)** para configurar as definições de inscrição única no lado da aplicação.

    a. **[Crie um utilizador de teste AwareGo](#create-an-awarego-test-user)** para ter uma contraparte de B.Simon in AwareGo que está ligada à representação AD Azure do utilizador.  
    b. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Para ativar o Azure AD SSO no portal Azure, faça o seguinte:

1. No portal Azure, na página de integração da aplicação **AwareGo,** em **Gestão**, selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. Para editar as definições, no Conjunto Sign-On com painel **SAML,** selecione o botão **Editar.**

   ![Screenshot do botão Editar para Configuração SAML Básica.](common/edit-urls.png)

1. No painel de edição, em **Configuração BÁSICA SAML,** faça o seguinte:

    a. Na **placa de sinalização na** caixa URL, insira qualquer um dos seguintes URLs:

    * `https://lms.awarego.com/auth/signin/` 
    * `https://my.awarego.com/auth/signin/`

    b. Na caixa **identifier (Entity ID),** introduza um URL no seguinte formato: `https://<SUBDOMAIN>.awarego.com`

    c. Na caixa **URL de resposta,** introduza um URL no seguinte formato: `https://<SUBDOMAIN>.awarego.com/auth/sso/callback`

    > [!NOTE]
    > Os valores anteriores não são reais. Atualize-os com o identificador real e responda urLs. Para obter os valores, contacte a [equipa de apoio ao cliente AwareGo.](mailto:support@awarego.com) Também pode consultar os exemplos na secção **Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** ao lado **do Certificado (Base64),** selecione **Descarregamento** para descarregar o certificado e guardá-lo para o seu computador.

    ![Screenshot do link "Download" do certificado no painel de certificado de assinatura SAML.](common/certificatebase64.png)

1. Na secção **Configurar AwareGo,** copie um ou mais URLs, dependendo dos seus requisitos.

    ![Screenshot do painel "Configurar AwareGo" para copiar URLs de configuração.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, cria-se um utilizador de teste no portal Azure chamado B.Simon.

1. No painel esquerdo do portal Azure, selecione **O Diretório Ativo Azure** e, em seguida, selecione **Utilizadores**  >  **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. No painel de propriedades do **Utilizador,** faça o seguinte:

   a. Na caixa **Nome,** insira **B.Simon**.  
   b. Na caixa **do nome do utilizador,** introduza o nome de utilizador no seguinte formato: `<username>@<companydomain>.<extension>` (por exemplo, B.Simon@contoso.com ).  
   c. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, note o valor que é apresentado na caixa **palavra-passe** para posterior utilização.  
   d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, permite ao utilizador B.Simon utilizar o Azure SSO, concedendo acesso ao AwareGo.

1. No portal Azure, selecione **Aplicações empresariais**  >  **Todas as aplicações**.
1. Na lista **de Aplicações,** selecione **AwareGo**.
1. Na página geral da aplicação, na secção **Gerir,** selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador** e, em seguida, no painel de atribuição de **adicionar,** selecione **Utilizadores e grupos**.
1. No painel **de Utilizadores e grupos,** na lista **de Utilizadores,** selecione **B.Simon** e, em seguida, selecione o botão **Select.**
1. Se estiver à espera de atribuir uma função aos utilizadores, pode selecioná-la na lista de abandono de **funções.** Se não tiver sido criada qualquer função para esta aplicação, a função *De Acesso Predefinido* é selecionada.
1. No painel **'Adicionar Atribuição',** selecione o botão **Atribuir.**

## <a name="configure-awarego-sso"></a>Configurar AwareGo SSO

Para configurar um único sinal no lado **AwareGo,** envie o certificado **certificado (Base64)** que descarregou anteriormente e os URLs que copiou anteriormente do portal Azure para a equipa de [suporte awareGo](mailto:support@awarego.com). A equipa de apoio cria esta definição para estabelecer corretamente a ligação SSO SAML de ambos os lados.

### <a name="create-an-awarego-test-user"></a>Criar um utilizador de teste AwareGo

Nesta secção, cria-se um utilizador chamado Britta Simon in AwareGo. Trabalhe com a [equipa de suporte AwareGo](mailto:support@awarego.com) para adicionar os utilizadores na plataforma AwareGo. Tem de criar e ativar os utilizadores antes de poder utilizar uma única sessão de sessão.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, pode testar a sua configuração de inscrição única AD AD, fazendo qualquer uma das seguintes: 

* No portal Azure, selecione **Testar esta aplicação**. Isto redireciona-o para a página de início de sposição Do AwareGo, onde pode iniciar o fluxo de entrada. 

* Vá diretamente à página de entrada do AwareGo e inicie o fluxo de entrada a partir daí.

* Vá ao Microsoft My Apps. Quando seleciona o azulejo **AwareGo** nas Minhas Apps, é redirecionado para a página de sinse-in AwareGo. Para obter mais informações, consulte [iniciar sômis e inicie aplicações a partir do portal My Apps.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Passos seguintes

Depois de configurar o AwareGo, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Controlo de Aplicações de Acesso Condicional. Para obter mais informações, consulte [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)


