---
title: Ligue para uma API web ASP.NET protegida pela plataforma de identidade Microsoft
description: Neste quickstart, aprenda a chamar uma API web ASP.NET protegida pela plataforma de identidade microsoft a partir de uma aplicação do Windows Desktop (WPF). O cliente WPF autentica um utilizador, solicita um token de acesso e liga para a API web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 62cebb4e774e2f86ed6a4a17edd6da71f7c7cd9f
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141334"
---
# <a name="quickstart-call-an-aspnet-web-api-protected-by-microsoft-identity-platform"></a>Quickstart: Ligue para uma API web ASP.NET protegida pela plataforma de identidade microsoft

Neste arranque rápido, expõe uma API web e protege-a para que apenas o utilizador autenticado possa aceder à sua utilização. Esta amostra mostra como expor uma API web ASP.NET para que possa aceitar fichas emitidas por contas pessoais (incluindo outlook.com, live.com e outros) bem como contas de trabalho e escola de qualquer empresa ou organização que tenha integrado com a plataforma de identidade da Microsoft.

A amostra também inclui um cliente de aplicação do Windows Desktop (WPF) que demonstra como pode solicitar um token de acesso para aceder a uma API web.

## <a name="prerequisites"></a>Pré-requisitos

Para executar esta amostra, você precisará do seguinte:

* Visual Studio 2017 ou 2019.  Baixe [gratuitamente o Visual Studio](https://www.visualstudio.com/downloads/).

* Ou uma [conta Microsoft](https://www.outlook.com) ou o [Microsoft 365 Developer Program](/office/developer-program/office-365-developer-program)

## <a name="download-or-clone-this-sample"></a>Descarregue ou clone esta amostra

Pode clonar esta amostra da sua concha ou linha de comando:

  ```console
  git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
  ```

Ou, pode [descarregar a amostra como um ficheiro ZIP](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

## <a name="register-your-web-api-in-the-application-registration-portal"></a>Registe a sua API web no portal de registo de candidaturas

### <a name="choose-the-azure-ad-tenant-where-you-want-to-create-your-applications"></a>Escolha o inquilino Azure AD onde pretende criar as suas aplicações

Se quiser registar as suas aplicações manualmente, como um primeiro passo, terá de ser:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se a sua conta estiver presente em mais de um inquilino AD Azure, selecione o seu perfil no canto superior direito no menu em cima da página e, em seguida, altere o **diretório**.
   Mude a sua sessão de portal para o pretendido inquilino AZure AD.

### <a name="register-the-service-app-todolistservice"></a>Registar a aplicação de serviço (TodoListService)

1. Navegue para a plataforma de identidade da Microsoft para programadores [Página de registos de aplicações.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Selecione **Novo registo**.
1. Quando aparecer a **página de inscrição,** insira as informações de registo do seu pedido:
   - Na secção **Nome,** introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por `AppModelv2-NativeClient-DotNet-TodoListService` exemplo.
   - Alterar **tipos de conta suportadas** para contas em qualquer **diretório organizacional**.
   - Selecione **Registar** para criar a aplicação.

1. Na página **de Visão Geral** da aplicação, encontre o valor de **ID da Aplicação (cliente)** e grave-o para mais tarde. Você precisará dele para configurar o ficheiro de configuração do Estúdio Visual para este projeto ( `ClientId` em `TodoListService\Web.config` ).
1. Selecione a secção **Expor uma API** e:
   - **Selecione Adicionar um âmbito**
   - aceitar a proposta de ID URI (api://{clientId}) selecionando **Save and Continue**
   - Introduza os seguintes parâmetros:
     - para **uso de nome scope**`access_as_user`
     - Certifique-se de que a opção **Dedmins e utilizadores** está selecionada para **Quem pode consentir**
     - no tipo **de nome de exibição de consentimento de administração**`Access TodoListService as a user`
     - no tipo **de descrição do consentimento de administração**`Accesses the TodoListService web API as a user`
     - no **tipo de nome de exibição de consentimento do utilizador**`Access TodoListService as a user`
     - no tipo **de descrição do consentimento do utilizador**`Accesses the TodoListService web API as a user`
     - Manter **o Estado** como **Ativado**
     - Selecione **adicionar âmbito**

### <a name="configure-the-service-project-to-match-the-registered-web-api"></a>Configurar o projeto de serviço para corresponder à API web registada

1. Abra a solução no Visual Studio e, em seguida, abra o ficheiro **Web.config** sob a raiz do projeto **TodoListService.**
1. Substitua o valor do `ida:ClientId` parâmetro pelo ID do **Cliente (ID de aplicação)** da aplicação que acabou de registar no Portal de Registo de Candidaturas.

### <a name="add-the-new-scope-to-the-todolistclients-appconfig"></a>Adicione o novo âmbito à app.config do *TodoListClient*

* Abra o ficheiro **app.config** localizado na pasta raiz do projeto **TodoListClient** e, em seguida, cole o ID da **aplicação** que acabou de registar para o seu *TodoListService* em `TodoListServiceScope` parâmetro, substituindo a cadeia `{Enter the Application ID of your TodoListService from the app registration portal}` .

  > [!NOTE]
  > Certifique-se de que utiliza o seguinte formato:
  >
  > `api://{TodoListService-Application-ID}/access_as_user`
  >
  >(onde {TodoListService-Application-ID} é o GUID que representa o ID de aplicação para o seu TodoListService).

## <a name="register-the-client-app-todolistclient"></a>Registe a aplicação do cliente (TodoListClient)

Neste passo, configura o seu projeto *TodoListClient* registando uma nova aplicação no portal de registo de Aplicações. Nos casos em que o cliente e o servidor são considerados *a mesma aplicação,* pode também reutilizar a mesma aplicação registada no 'Passo 2.'. A utilização da mesma aplicação é necessária se quiser que os utilizadores entrem com contas pessoais da Microsoft

### <a name="register-the-todolistclient-application-in-the-application-registration-portal"></a>Registe a aplicação *TodoListClient* no portal de *registo de candidaturas*

1. Navegue para a plataforma de identidade da Microsoft para programadores [Página de registos de aplicações.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Selecione **Novo registo**.
1. Quando aparecer a **página de inscrição,** insira as informações de registo do seu pedido:
   - Na secção **Nome,** introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por `NativeClient-DotNet-TodoListClient` exemplo.
   - Alterar **tipos de conta suportadas** para contas em qualquer **diretório organizacional**.
   - Selecione **Registar** para criar a aplicação.
   
   > [!NOTE]
   > Nas **app.config**do projeto *TodoListClient,* o valor padrão `ida:Tenant` de . `common`
   >
   > `common`significa que pode iniciar sôms, utilizando uma conta work ou school ou uma conta pessoal da Microsoft (porque selecionou **contas em qualquer diretório organizacional).**
   >
   > `organizations`significa que pode entrar usando uma conta de Trabalho ou Escola.
   >
   > `consumers`significa que só pode iniciar scontabilidade utilizando uma conta pessoal da Microsoft.
   >
   
1. Na página 'Visão Geral' da aplicação, selecione a secção **autenticação.**
   1. Nas **configurações da Plataforma,** selecione o botão **Adicionar uma plataforma.**
   1. Para **aplicações móveis e desktop,** selecione as **aplicações Mobile e desktop**.
   1. Para **redirecionar URIs,** selecione a **https://login.microsoftonline.com/common/oauth2/nativeclient** caixa de verificação.
   1. Selecione **Configurar**.   
1. Selecione a secção **de permissões API**
   1. Selecione o botão **Adicionar uma permissão**.
   1. Selecione o **separador As Minhas APIs.**
   1. Na lista de APIs, selecione o `AppModelv2-NativeClient-DotNet-TodoListService API` , ou o nome que inseriu para a API web.
   1. Verifique a permissão **access_as_user** se ainda não foi verificada. Utilize a caixa de pesquisa, se necessário.
   1. Selecione o botão **'Adicionar permissões'.**

### <a name="configure-your-todolistclient-project"></a>Configure o seu projeto *TodoListClient*

1. No portal de registo de *aplicações,* na página **geral copiam** o valor do **ID da Aplicação (cliente)**
1. Abra o ficheiro **app.config** localizado na pasta raiz do projeto **TodoListClient** e, em seguida, cole o valor no valor do `ida:ClientId` parâmetro

## <a name="run-your-project"></a>Executar o seu projeto

1. Pressione `<F5>` para executar o seu projeto. O seu *TodoListClient* deve abrir.
1. Selecione **Iniciar sôms no** direito superior e faça sôs-lhe o mesmo utilizador que utilizou para registar a sua aplicação, ou um utilizador no mesmo diretório.
1. Neste momento, se estiver a iniciar sessão pela primeira vez, poderá ser solicitado que consinta na *TodoListService* Web Api.
1. O sign-in também solicita o token de acesso ao *access_as_user* âmbito para aceder *ao TodoListService* Web Api e manipular a lista *de A-Fazer.*

## <a name="pre-authorize-your-client-application"></a>Pré-autorizar a sua candidatura ao cliente

Uma das formas de permitir que os utilizadores de outros diretórios acedam à sua API web é *autorizando as* aplicações do cliente a aceder à sua API web adicionando os IDs de aplicações de clientes na lista de aplicações *pré-autorizadas* para a sua API web. Ao adicionar um cliente pré-autorizado, não exigirá que o utilizador consinta em utilizar a sua API web. Siga os passos abaixo para pré-autorizar a sua Aplicação Web::

1. Volte ao portal de *registo de aplicações* e abra as propriedades do seu **Serviço TodoList.**
1. Na secção **Expor uma API,** clique em **Adicionar um pedido de cliente** na secção de *aplicações de cliente autorizado.*
1. No campo *de Identificação do Cliente,* cole o ID da aplicação. `TodoListClient`
1. Na secção *de âmbitos autorizados,* selecione o âmbito para esta API web `api://<Application ID>/access_as_user` .
1. Prima o botão **de aplicação Adicionar** na parte inferior da página.

## <a name="run-your-project"></a>Executar o seu projeto

1. Pressione `<F5>` para executar o seu projeto. O seu *TodoListClient* deve abrir.
1. Selecione **Iniciar sôms no** direito superior (ou Limpar Cache/Iniciar sôs)e, em seguida, iniciar sôm-in utilizando uma conta pessoal da Microsoft (live.com ou hotmail.com) ou trabalho ou conta escolar.

## <a name="optional-restrict-sign-in-access-to-your-application"></a>Opcional: Restringir o acesso de inscrição à sua aplicação

Por padrão, quando descarrega esta amostra de código e configura a aplicação para utilizar o ponto final do Azure Ative Directory v2 seguindo os passos anteriores, ambas as contas pessoais - como outlook.com, live.com e outras - bem como contas de trabalho ou escola de quaisquer organizações que estejam integradas com a AZure AD podem solicitar fichas e aceder à sua API web.

Para restringir quem pode iniciar sôm na sua aplicação, utilize uma das opções:

### <a name="option-1-restrict-access-to-a-single-organization-single-tenant"></a>Opção 1: Restringir o acesso a uma única organização (inquilino único)

Você pode restringir o acesso de entrada para a sua aplicação apenas contas de usuário que estão em um único inquilino AZure AD - incluindo contas de *hóspedes* desse inquilino. Este cenário é comum para *aplicações de linha de negócio:*

1. Abra o ficheiro **App_Start\Startup.Auth** e altere o valor do ponto final de metadados que é transmitido `OpenIdConnectSecurityTokenProvider` para `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` (também pode utilizar o Nome do Inquilino, `contoso.onmicrosoft.com` como).
2. No mesmo ficheiro, coloque a `ValidIssuer` propriedade no de e o argumento para `TokenValidationParameters` `"https://sts.windows.net/{Tenant ID}/"` `ValidateIssuer` `true` .

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Opção 2: Utilize um método personalizado para validar emitentes

Pode implementar um método personalizado para validar emitentes utilizando o parâmetro **EmiterValidator.** Para obter mais informações sobre como utilizar este parâmetro, leia sobre a [classe TokenValidationParameters](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre o cenário de API web protegido que a plataforma de identidade da Microsoft suporta:
> [!div class="nextstepaction"]
> [Cenário de API web protegido](scenario-protected-web-api-overview.md)
