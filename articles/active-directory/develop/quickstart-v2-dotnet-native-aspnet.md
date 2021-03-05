---
title: 'Quickstart: Ligue para uma API web ASP.NET que esteja protegida pela plataforma de identidade microsoft | Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste quickstart, aprenda a chamar uma API web ASP.NET protegida pela plataforma de identidade da Microsoft a partir de uma aplicação do Windows Desktop (WPF).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 0e8565615cdbac63b0526b5cd87dd9d92d5aa4f1
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216688"
---
# <a name="quickstart-call-an-aspnet-web-api-thats-protected-by-microsoft-identity-platform"></a>Quickstart: Ligue para uma API web ASP.NET protegida pela plataforma de identidade da Microsoft

Neste quickstart, você descarrega e execute uma amostra de código que demonstra como proteger uma API web ASP.NET, limitando o acesso aos seus recursos apenas a contas autorizadas. A amostra suporta a autorização de contas e contas pessoais da Microsoft em qualquer organização do Azure Ative Directory (Azure AD).

O artigo também usa uma aplicação da Windows Presentation Foundation (WPF) para demonstrar como pode solicitar um token de acesso para aceder a uma API web.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Visual Studio 2017 ou 2019. Baixe [gratuitamente o Visual Studio](https://www.visualstudio.com/downloads/).

## <a name="clone-or-download-the-sample"></a>Clone ou descarregue a amostra

Pode obter a amostra de duas formas:

* Clone-o a partir da sua concha ou linha de comando:
   ```console
   git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
   ```
* [Descarregue-o como um ficheiro ZIP.](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip)

## <a name="register-your-web-api"></a>Registe a sua API web

Nesta secção, registe a sua API web nas **inscrições** da App no portal Azure.

### <a name="choose-your-azure-ad-tenant"></a>Escolha o seu inquilino Azure AD

Para registar as suas aplicações manualmente, escolha o inquilino Azure Ative Directory (Azure AD) onde pretende criar as suas apps.

1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se tiver acesso a vários inquilinos, use o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino que pretende utilizar.

### <a name="register-the-todolistservice-app"></a>Registe a aplicação TodoListService

1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
1. Procure e selecione **Azure Active Directory**.
1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
1. Introduza um **Nome** para a sua aplicação, por `AppModelv2-NativeClient-DotNet-TodoListService` exemplo. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde.
1. Para **tipos de conta suportada**, selecione Contas em qualquer **diretório organizacional**.
1. Selecione **Registar** para criar a aplicação.
1. Na página **de Visão Geral** da aplicação, procure o valor de **ID da Aplicação (cliente)** e, em seguida, grave-o para utilização posterior. Você precisará dele para configurar o ficheiro de configuração do Estúdio Visual para este projeto (isto é, `ClientId` no ficheiro *TodoListService\Web.config).*
1. Em **Gestão**, **selecione Expor uma API** Adicione um  >  **âmbito**. Aceite a proposta de ID URI de aplicação ( `api://{clientId}` ) selecionando **Save and continue**, e, em seguida, insira as seguintes informações:

    1. Para **o nome Scope,** insira `access_as_user` .
    1. Para **Quem pode consentir**, certifique-se de que a opção **Dedmins e utilizadores** está selecionada.
    1. Na caixa de **nome do exposição de consentimento** do Administrador, insira `Access TodoListService as a user` .
    1. Na caixa de descrição do consentimento do **Administrador,** insira `Accesses the TodoListService web API as a user` .
    1. Na caixa de nome de visualização do **consentimento do utilizador,** introduza `Access TodoListService as a user` .
    1. Na caixa **de descrição** do consentimento do Utilizador, insira `Accesses the TodoListService web API as a user` .
    1. Para **o Estado**, mantenha **ativado**.
1. Selecione **Adicionar âmbito**.

### <a name="configure-the-service-project"></a>Configure o projeto de serviço

Configure o projeto de serviço para corresponder à API web registada, fazendo o seguinte:

1. Abra a solução no Visual Studio e, em seguida, abra o ficheiro *Web.config* sob a raiz do projeto TodoListService.

1. Substitua o valor do `ida:ClientId` parâmetro pelo valor ID do Cliente (ID da aplicação que acabou de registar no portal de **registos** da App.

### <a name="add-the-new-scope-to-the-appconfig-file"></a>Adicione o novo âmbito ao ficheiro app.config

Para adicionar o novo âmbito ao ficheiro todoListClient *app.config,* faça o seguinte:

1. Na pasta raiz do projeto TodoListClient, abra o ficheiro *app.config.*

1. Cole o ID da aplicação que acabou de registar para o seu projeto TodoListService no `TodoListServiceScope` parâmetro, substituindo a `{Enter the Application ID of your TodoListService from the app registration portal}` cadeia.

  > [!NOTE]
  > Certifique-se de que o ID da aplicação utiliza o seguinte formato: `api://{TodoListService-Application-ID}/access_as_user` (onde `{TodoListService-Application-ID}` está o GUID que representa o ID da aplicação para a sua aplicação TodoListService).

## <a name="register-the-todolistclient-client-app"></a>Registe a app de clientes TodoListClient

Nesta secção, regista a sua app TodoListClient nas **inscrições** da App no portal Azure e, em seguida, configura o código no projeto TodoListClient. Se o cliente e o servidor forem considerados *a mesma aplicação,* pode reutilizar a aplicação registada na fase 2. Utilize a mesma aplicação se quiser que os utilizadores entrem com uma conta pessoal da Microsoft.

### <a name="register-the-app"></a>Registar a aplicação

Para registar a aplicação TodoListClient, faça o seguinte:

1. Aceda à plataforma de identidade da Microsoft para programadores O portal [de registos de aplicações.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Selecione **Novo registo**.
1. Quando a página do Registo abrir uma página de **inscrição,** insira as informações de registo do seu pedido:

    1. Na secção **Nome,** introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação (por exemplo, **NativeClient-DotNet-TodoListClient).**
    1. Para **tipos de conta suportada**, selecione Contas em qualquer **diretório organizacional**.
    1. Selecione **Registar** para criar a aplicação.

   > [!NOTE]
   > No projeto TodoListClient *app.config* ficheiro, o valor padrão `ida:Tenant` de . `common` Os valores possíveis são:
   > - `common`: Pode iniciar sômência utilizando uma conta pessoal de trabalho ou escola ou uma conta pessoal da Microsoft (porque selecionou **Contas em qualquer diretório organizacional** no passo 3b).
   > - `organizations`: Pode iniciar scontabilidade utilizando uma conta de trabalho ou escola.
   > - `consumers`: Só pode iniciar scontabilidade utilizando uma conta pessoal da Microsoft.

1. Na página **'Visão Geral'** da aplicação, selecione **Autenticação** e, em seguida, faça o seguinte:

    1. Nas **configurações da Plataforma,** selecione o botão **Adicionar uma plataforma.**
    1. Para **aplicações móveis e desktop**, selecione **aplicações móveis e desktop**.
    1. Para **redirecionar URIs,** selecione a **https://login.microsoftonline.com/common/oauth2/nativeclient** caixa de verificação.
    1. Selecione **Configurar**.

1. Selecione **permissões API**, e, em seguida, faça o seguinte:

    1. Selecione o botão **Adicionar uma permissão**.
    1. Selecione o **separador As Minhas APIs.**
    1. Na lista de APIs, selecione **AppModelv2-NativeClient-DotNet-TodoListService API** ou o nome que inseriu para a API web.
    1. Selecione a caixa de verificação de permissão **access_as_user** se ainda não estiver selecionada. Se necessário, utilize a caixa de busca.
    1. Selecione o botão **'Adicionar permissões'.**

### <a name="configure-your-project"></a>Configure o seu projeto

Para configurar o seu projeto TodoListClient, faça o seguinte:

1. No portal de **registos** da App, na página **'Visão Geral', copie** o valor do **ID da Aplicação (cliente).**

1. A partir da pasta raiz do projeto TodoListClient, abra o ficheiro *app.config* e, em seguida, cole o valor de ID da aplicação no `ida:ClientId` parâmetro.

## <a name="run-your-todolistclient-project"></a>Executar o seu projeto TodoListClient

Para executar o seu projeto TodoListClient, faça o seguinte:

1. Prima F5 para abrir o seu projeto TodoListClient. A página do projeto deve abrir.

1. No canto superior direito, **selecione Iniciar sôms,** e, em seguida, faça o sômis com as mesmas credenciais que usou para registar a sua aplicação, ou inscreva-se como utilizador no mesmo diretório.

   Se estiver a iniciar sessão pela primeira vez, poderá ser solicitado que consinta na API web Do TodoListService.

   Para ajudá-lo a aceder à API web TodoListService e manipular a lista *de A-Fazer,* o insiná-lo também solicita um sinal de acesso ao âmbito *access_as_user.*

## <a name="pre-authorize-your-client-application"></a>Pré-autorizar a sua candidatura ao cliente

Uma das formas de permitir que utilizadores de outros diretórios acedam à sua API web é pré-autorizar a aplicação do cliente a aceder à sua API web. Fá-lo adicionando o ID da aplicação do cliente à lista de aplicações pré-autorizadas para a sua API web. Ao adicionar um cliente pré-autorizado, está a permitir que os utilizadores acedam à sua API web sem ter de fornecer o seu consentimento. Para pré-autorizar a aplicação do seu cliente, faça o seguinte:

1. No portal de registos da **App,** abra as propriedades da sua aplicação TodoListService.
1. Na secção **Expor uma API,** em **aplicações de clientes autorizados,** selecione **Adicionar uma aplicação ao cliente.**
1. Na caixa **de identificação** do Cliente, cole o ID de aplicação da aplicação TodoListClient.
1. Na secção **de âmbitos autorizados,** selecione o âmbito para a `api://<Application ID>/access_as_user` API web.
1. Selecione **Adicionar a aplicação**.

### <a name="run-your-project"></a>Executar o seu projeto

1. Prima F5 para executar o seu projeto. A sua aplicação TodoListClient deve ser aberta.
1. No canto superior direito, selecione **Iniciar sôms e,** hotmail.com live.com em seguida, faça o sôm.

## <a name="optional-limit-sign-in-access-to-certain-users"></a>Opcional: Limitar o acesso ao acesso a determinados utilizadores

Por padrão, quando tiver seguido os passos anteriores, quaisquer contas pessoais, tais como outlook.com ou live.com, ou contas de trabalho ou escola de organizações que estão integradas com AZure AD podem solicitar fichas e aceder à sua API web.

Para especificar quem pode iniciar sôm na sua aplicação, utilize uma das seguintes opções:

### <a name="option-1-limit-access-to-a-single-organization-single-tenant"></a>Opção 1: Limitar o acesso a uma única organização (inquilino único)

Você pode limitar o acesso de inscrição à sua aplicação a contas de utilizador que estão em um único inquilino AZure AD, incluindo contas de *hóspedes* desse inquilino. Este cenário é comum para *aplicações de linha de negócio.*

1. Abra o ficheiro *App_Start\Startup.Auth* e, em seguida, altere o valor do ponto final de metadados que é passado `OpenIdConnectSecurityTokenProvider` para `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` . Você também pode usar o nome do inquilino, `contoso.onmicrosoft.com` como.
2. No mesmo ficheiro, coloque a `ValidIssuer` propriedade no `TokenValidationParameters` `"https://sts.windows.net/{Tenant ID}/"` to, e desemote o `ValidateIssuer` argumento para `true` .

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Opção 2: Utilize um método personalizado para validar emitentes

Pode implementar um método personalizado para validar emitentes utilizando o `IssuerValidator` parâmetro. Para obter mais informações sobre este parâmetro, consulte [a classe TokenValidationParameters](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o cenário de API web protegido que a plataforma de identidade da Microsoft suporta:
> [!div class="nextstepaction"]
> [Cenário de API web protegido](scenario-protected-web-api-overview.md)
