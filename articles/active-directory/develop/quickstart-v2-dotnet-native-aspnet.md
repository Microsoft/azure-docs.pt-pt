---
title: Ligue para um ASP.NET Web API protegido pela plataforma de identidade da Microsoft
description: Neste arranque rápido, aprenda a chamar um ASP.NET Web API protegido pela plataforma de identidade da Microsoft a partir de uma aplicação do Windows Desktop (WPF). O cliente WPF autentica um utilizador, solicita um sinal de acesso e chama a Web API.
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
ms.openlocfilehash: 084083a704a007e6675234883c62350d1d9a0849
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536153"
---
# <a name="quickstart-call-an-aspnet-web-api-protected-by-microsoft-identity-platform"></a>Quickstart: Ligue para um ASP.NET Web API protegido pela plataforma de identidade da Microsoft

Neste arranque rápido, expõe uma API web e protege-a para que apenas o utilizador autenticado possa aceder a ela. Esta amostra mostra como expor uma ASP.NET Web API para que possa aceitar fichas emitidas por contas pessoais (incluindo outlook.com, live.com e outras) bem como contas de trabalho e escolas de qualquer empresa ou organização que tenha integrado com a plataforma de identidade da Microsoft.

A amostra também inclui um cliente da aplicação Windows Desktop (WPF) que demonstra como pode solicitar um sinal de acesso para aceder a uma API web.

## <a name="prerequisites"></a>Pré-requisitos

Para executar esta amostra, necessitará do seguinte:

* Estúdio Visual 2017 ou 2019.  Baixe [o Estúdio Visual gratuitamente.](https://www.visualstudio.com/downloads/)

* Ou uma [conta Microsoft](https://www.outlook.com) ou programa de [desenvolvedores do Office 365](/office/developer-program/office-365-developer-program)

## <a name="download-or-clone-this-sample"></a>Descarregue ou clone esta amostra

Pode clonar esta amostra a partir da sua concha ou linha de comando:

  ```console
  git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
  ```

Ou, pode [descarregar a amostra como ficheiro ZIP.](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip)

## <a name="register-your-web-api-in-the-application-registration-portal"></a>Registe a sua API web no portal de registo de candidaturas

### <a name="choose-the-azure-ad-tenant-where-you-want-to-create-your-applications"></a>Escolha o inquilino da AD Azure onde pretende criar as suas aplicações

Se quiser registar as suas aplicações manualmente, como primeiro passo terá de:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se a sua conta estiver presente em mais de um inquilino DaD Azure, selecione o seu perfil no canto superior direito do menu em cima da página e, em seguida, **mude de diretório**.
   Mude a sua sessão do portal para o desejado inquilino da AD Azure.

### <a name="register-the-service-app-todolistservice"></a>Registe a aplicação de serviço (TodoListService)

1. Navegue na plataforma de identidade da Microsoft para programadores da página de registos de [aplicações.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Selecione **Novo registo**.
1. Quando aparecer uma página de **inscrição,** insira as informações de registo da sua candidatura:
   - Na secção **Nome,** introduza um nome de aplicação significativo que `AppModelv2-NativeClient-DotNet-TodoListService`será apresentado aos utilizadores da aplicação, por exemplo.
   - Alterar os tipos de **conta suportados** para **Contas em qualquer diretório organizacional**.
   - Selecione **Registar** para criar a aplicação.

1. Na página **'Overview'** da aplicação, encontre o valor de **ID da Aplicação (cliente)** e grave-o para mais tarde. Você precisará dele para configurar o arquivo de`ClientId` configuração do Estúdio Visual para este projeto ( em `TodoListService\Web.config`).
1. Selecione a secção **Expor uma API** e:
   - **Selecione Adicionar um âmbito**
   - aceitar o ID de aplicação proposto URI (api://{clientId}) selecionando **Guardar e Continuar**
   - Introduza os seguintes parâmetros:
     - para a utilização do **nome Scope**`access_as_user`
     - Certifique-se de que a opção **de Administradores e utilizadores** é selecionada para Quem pode **consentir**
     - no tipo de nome de **exibição de consentimento do Administrador**`Access TodoListService as a user`
     - no tipo de descrição de **consentimento da Admin**`Accesses the TodoListService web API as a user`
     - no tipo de nome de **exibição** de consentimento do utilizador`Access TodoListService as a user`
     - no tipo de **descrição** do consentimento do utilizador`Accesses the TodoListService web API as a user`
     - Manter **o Estado** como **habilitado**
     - **Selecione adicionar âmbito**

### <a name="configure-the-service-project-to-match-the-registered-web-api"></a>Configure o projeto de serviço para corresponder à API web registada

1. Abra a solução no Estúdio Visual e abra o ficheiro **Web.config** sob a raiz do projeto **TodoListService.**
1. Substitua o `ida:ClientId` valor do parâmetro pelo ID do Cliente (ID de **aplicação)** da aplicação que acabou de registar no Portal de Registo de Aplicações.

### <a name="add-the-new-scope-to-the-todolistclients-appconfig"></a>Adicione a nova margem à app *TodoListClient.config*

1. Abra o ficheiro **app.config** localizado na pasta raiz do projeto **TodoListClient** e, em seguida, `TodoListServiceScope` colhe o ID `{Enter the Application ID of your TodoListService from the app registration portal}`de **aplicação** da aplicação que acabou de registar para o seu *TodoListService* sob parâmetro, substituindo a cadeia .

   > Nota: Certifique-se de que utiliza o seguinte formato:
   >
   > `api://{TodoListService-Application-ID}/access_as_user`
   >
   >(onde {TodoListService-Application-ID} é o GUID que representa o ID de aplicação para o seu TodoListService).

## <a name="register-the-client-app-todolistclient"></a>Registe a aplicação do cliente (TodoListClient)

Neste passo, configura o seu projeto *TodoListClient* registando uma nova aplicação no portal de registo de aplicações. Nos casos em que o cliente e o servidor são considerados *a mesma aplicação,* pode também reutilizar a mesma aplicação registada no 'Passo 2.». A utilização da mesma aplicação é necessária se quiser que os utilizadores assinem com contas pessoais da Microsoft

### <a name="register-the-todolistclient-application-in-the-application-registration-portal"></a>Registe a aplicação *TodoListClient* no portal de registo de *aplicações*

1. Navegue na plataforma de identidade da Microsoft para programadores da página de registos de [aplicações.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Selecione **Novo registo**.
1. Quando aparecer uma página de **inscrição,** insira as informações de registo da sua candidatura:
   - Na secção **Nome,** introduza um nome de aplicação significativo que `NativeClient-DotNet-TodoListClient`será apresentado aos utilizadores da aplicação, por exemplo.
   - Alterar os tipos de **conta suportados** para **Contas em qualquer diretório organizacional**.
   - Selecione **Registar** para criar a aplicação.
1. Na página Descrição geral da aplicação, selecione a secção **Autenticação**.
   - Na secção **Redirecionamento** | **de URIs sugerido para clientes públicos (mobile, desktop),** verifique**https://login.microsoftonline.com/common/oauth2/nativeclient**
   - Selecione **Guardar**.
1. Selecione a secção de **permissões DaPI**
   - Clique no botão adicionar um botão **de permissão** e, em seguida,
   - Selecione o separador **My APIs.**
   - Na lista de APIs, `AppModelv2-NativeClient-DotNet-TodoListService API`selecione o , ou o nome que inseriu para a Web API.
   - Verifique **a** access_as_user permissão se ainda não foi verificada. Utilize a caixa de pesquisa se necessário.
   - Selecione o botão **adicionar permissões**

### <a name="configure-your-todolistclient-project"></a>Configure o seu projeto *TodoListClient*

1. No *portal*de registo de candidaturas, na página **'Overview'** copiam o valor do ID da **Aplicação (cliente)**
1. Abra o ficheiro **app.config** localizado na pasta raiz do projeto **TodoListClient** e, em seguida, colá o valor no valor do `ida:ClientId` parâmetro

## <a name="run-your-project"></a>Executar o seu projeto

1. Pressione `<F5>` para executar o seu projeto. O seu *TodoListClient* deve abrir.
1. Selecione **Iniciar sessão** no direito superior e iniciar sessão com o mesmo utilizador que utilizou para registar a sua aplicação ou um utilizador no mesmo diretório.
1. Neste momento, se estiver a iniciar sessão pela primeira vez, poderá ser solicitado o consentimento para *a TodoListService* Web Api.
1. O insessão também solicita o sinal de acesso ao âmbito *access_as_user* de acesso ao *TodoListService* Web Api e manipular a lista *de A-Fazer.*

## <a name="pre-authorize-your-client-application"></a>Pré-autorizar o seu pedido de cliente

Uma das formas de permitir que os utilizadores de outras diretórios acedam à sua Web API é *autorizando as* aplicações do cliente a aceder à sua Web API, adicionando os IDs de aplicação a partir de aplicações de clientes na lista de aplicações *pré-autorizadas* para a sua Web API. Ao adicionar um cliente pré-autorizado, não exigirá que o utilizador consinta em utilizar a sua API web. Siga os passos abaixo para pré-autorizar a sua Aplicação Web::

1. Volte ao portal de registo de *aplicações* e abra as propriedades do seu **TodoListService.**
1. Na secção **Expor uma API,** clique em **Adicionar uma aplicação de cliente** ao abrigo da secção de *aplicações autorizadas* do cliente.
1. No campo id do *cliente,* cola `TodoListClient` a identificação da aplicação da aplicação.
1. Na secção *de âmbitos autorizados,* selecione `api://<Application ID>/access_as_user`o âmbito para esta Web API .
1. Prima o botão **de aplicação Adicionar** na parte inferior da página.

## <a name="run-your-project"></a>Executar o seu projeto

1. Pressione `<F5>` para executar o seu projeto. O seu *TodoListClient* deve abrir.
1. Selecione **Iniciar sessão** no direito superior (ou Clear Cache/Sign-in) e, em seguida, iniciar sessão utilizando uma conta pessoal da Microsoft (live.com ou hotmail.com) ou trabalho ou conta escolar.

## <a name="optional-restrict-sign-in-access-to-your-application"></a>Opcional: Restringir o acesso ao acesso à sua aplicação

Por padrão, ao descarregar esta amostra de código e configurar a aplicação para utilizar o ponto final do Azure Ative Directory v2 seguindo os passos anteriores, ambas as contas pessoais - como outlook.com, live.com e outras - bem como contas de trabalho ou escola de quaisquer organizações integradas com a Azure AD podem solicitar fichas e aceder à sua Web API.

Para restringir quem pode iniciar sessão na sua candidatura, utilize uma das opções:

### <a name="option-1-restrict-access-to-a-single-organization-single-tenant"></a>Opção 1: Restringir o acesso a uma única organização (inquilino único)

Pode restringir o acesso de entrada para a sua aplicação apenas às contas de utilizador que se encontram num único inquilino da AD Azure - incluindo contas de *hóspedes* desse inquilino. Este cenário é comum para *aplicações de linha de negócio:*

1. Abra o ficheiro **App_Start\Startup.Auth** e altere o valor do ponto `OpenIdConnectSecurityTokenProvider` final `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` dos metadados que passou para `contoso.onmicrosoft.com`o (também pode usar o Nome do Inquilino, como).
2. No mesmo ficheiro, `ValidIssuer` coloque a `TokenValidationParameters` `"https://sts.windows.net/{Tenant ID}/"` propriedade `ValidateIssuer` no `true`ato e o argumento para .

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Opção 2: Utilize um método personalizado para validar os emitentes

Pode implementar um método personalizado para validar os emitentes utilizando o parâmetro **EmitenteValidator.** Para mais informações sobre como utilizar este parâmetro, leia sobre a [classe TokenValidaÇãoParâmetros](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o cenário de API da Web protegido que a plataforma de identidade da Microsoft suporta:
> [!div class="nextstepaction"]
> [Cenário de API da web protegido](scenario-protected-web-api-overview.md)
