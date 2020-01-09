---
title: Chamar um ASP.NET Web API protegido pela plataforma de identidade da Microsoft
description: Neste guia de início rápido, saiba como chamar uma API Web ASP.NET protegida pela plataforma de identidade da Microsoft de um aplicativo de área de trabalho do Windows (WPF). O cliente do WPF autentica um usuário, solicita um token de acesso e chama a API da Web.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c6c51b0a7ae7255391fd35d234b5ee47b7a9525
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424042"
---
# <a name="quickstart-call-an-aspnet-web-api-protected-by-microsoft-identity-platform"></a>Início rápido: chamar um ASP.NET Web API protegido pela plataforma de identidade da Microsoft

Neste guia de início rápido, você expõe uma API da Web e a protege para que somente o usuário autenticado possa acessá-la. Este exemplo mostra como expor um ASP.NET Web API para que ele possa aceitar tokens emitidos por contas pessoais (incluindo outlook.com, live.com e outros), bem como contas corporativas e de estudante de qualquer empresa ou organização integrada ao Microsoft Identity plataforma.

O exemplo também inclui um cliente do WPF (aplicativo de área de trabalho do Windows) que demonstra como você pode solicitar um token de acesso para acessar uma API da Web.

## <a name="prerequisites"></a>Pré-requisitos

Para executar este exemplo, você precisará do seguinte:

* Visual Studio 2017 ou 2019.  Baixe o [Visual Studio gratuitamente](https://www.visualstudio.com/downloads/).

* Um [conta Microsoft](https://www.outlook.com) ou um [programa para desenvolvedores do Office 365](/office/developer-program/office-365-developer-program)

## <a name="download-or-clone-this-sample"></a>Baixar ou clonar este exemplo

Você pode clonar este exemplo do Shell ou da linha de comando:

  ```console
  git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
  ```

Ou você pode [baixar o exemplo como um arquivo zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

## <a name="register-your-web-api-in-the-application-registration-portal"></a>Registrar sua API Web no portal de registro de aplicativos

### <a name="choose-the-azure-ad-tenant-where-you-want-to-create-your-applications"></a>Escolha o locatário do Azure AD no qual você deseja criar seus aplicativos

Se você quiser registrar seus aplicativos manualmente, como uma primeira etapa, você precisará:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se sua conta estiver presente em mais de um locatário do Azure AD, selecione seu perfil no canto superior direito no menu na parte superior da página e, em seguida, **Alterne o diretório**.
   Altere a sessão do portal para o locatário do Azure AD desejado.

### <a name="register-the-service-app-todolistservice"></a>Registrar o aplicativo de serviço (TodoListService)

1. Navegue até a página da plataforma Microsoft Identity para desenvolvedores [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Selecione **novo registro**.
1. Quando a **página Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
   - Na secção **Nome**, introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `AppModelv2-NativeClient-DotNet-TodoListService`.
   - Altere os **tipos de conta com suporte** para **contas em qualquer diretório organizacional**.
   - Selecione **Registar** para criar a aplicação.

1. Na página **visão geral** do aplicativo, localize o valor da **ID do aplicativo (cliente)** e registre-o para mais tarde. Você precisará dela para configurar o arquivo de configuração do Visual Studio para este projeto (`ClientId` em `TodoListService\Web.config`).
1. Selecione a seção **expor uma API** e:
   - Selecione **Adicionar um escopo**
   - aceite o URI da ID do aplicativo proposto (API://{clientId}) selecionando **salvar e continuar**
   - Insira os seguintes parâmetros:
     - para o **nome do escopo** , use `access_as_user`
     - Verifique se a opção **Administradores e usuários** está selecionada para **quem pode consentir**
     - em **nome de exibição do consentimento do administrador** , digite `Access TodoListService as a user`
     - em tipo de **Descrição de consentimento do administrador** `Accesses the TodoListService Web API as a user`
     - em **nome de exibição do consentimento do usuário** , digite `Access TodoListService as a user`
     - em tipo de **Descrição de consentimento do usuário** `Accesses the TodoListService Web API as a user`
     - Manter **estado** como **habilitado**
     - Selecione **Adicionar escopo**

### <a name="configure-the-service-project-to-match-the-registered-web-api"></a>Configurar o projeto de serviço para corresponder à API Web registrada 

1. Abra a solução no Visual Studio e, em seguida, abra o arquivo **Web. config** na raiz do projeto **TodoListService** .
1. Substitua o valor do parâmetro `ida:ClientId` pela **ID do cliente (ID do aplicativo)** do aplicativo que você acabou de registrar no portal de registro do aplicativo.

### <a name="add-the-new-scope-to-the-todolistclients-appconfig"></a>Adicionar o novo escopo ao app. config do *TodoListClient*

1. Abra o arquivo **app. config** localizado na pasta raiz do projeto **TodoListClient** e cole a **ID do aplicativo** do aplicativo que você acabou de registrar para o *TodoListService* em `TodoListServiceScope` parâmetro, substituindo a cadeia de caracteres `{Enter the Application ID of your TodoListService from the app registration portal}`.

   > Observação: Verifique se ele usa o seguinte formato:
   >
   > `api://{TodoListService-Application-ID}/access_as_user` 
   >
   >(em que {TodoListService-Application-ID} é o GUID que representa a ID do aplicativo para seu TodoListService).

## <a name="register-the-client-app-todolistclient"></a>Registrar o aplicativo cliente (TodoListClient)

Nesta etapa, você configura o projeto *TodoListClient* registrando um novo aplicativo no portal de registro de aplicativos. Nos casos em que o cliente e o servidor são considerados *o mesmo aplicativo* , você também pode apenas reutilizar o mesmo aplicativo registrado na ' etapa 2. '. Usar o mesmo aplicativo será necessário se você quiser que os usuários entrem com contas pessoais da Microsoft

### <a name="register-the-todolistclient-application-in-the-application-registration-portal"></a>Registrar o aplicativo *TodoListClient* no *portal de registro de aplicativos*

1. Navegue até a página da plataforma Microsoft Identity para desenvolvedores [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Selecione **novo registro**.
1. Quando a **página Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
   - Na secção **Nome**, introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `NativeClient-DotNet-TodoListClient`.
   - Altere os **tipos de conta com suporte** para **contas em qualquer diretório organizacional**.
   - Selecione **Registar** para criar a aplicação.
1. Na página Visão geral do aplicativo, selecione a seção **autenticação** .
   - Na seção **redirecionar uris** | **URIs de redirecionamento sugeridos para clientes públicos (móvel, área de trabalho)** , marque **https://login.microsoftonline.com/common/oauth2/nativeclient**
   - Selecione **Guardar**.
1. Selecione a seção **permissões de API**
   - Clique no botão **Adicionar uma permissão** e, em seguida,
   - Selecione a guia **minhas APIs** .
   - Na lista de APIs, selecione o `AppModelv2-NativeClient-DotNet-TodoListService API`ou o nome que você inseriu para a API da Web.
   - Verifique a permissão de **access_as_user** se ela ainda não estiver marcada. Use a caixa de pesquisa, se necessário.
   - Selecione o botão **adicionar permissões**

### <a name="configure-your-todolistclient-project"></a>Configurar seu projeto do *TodoListClient*

1. No *portal de registro de aplicativos*, na página **visão geral** , copie o valor da **ID do aplicativo (cliente)**
1. Abra o arquivo **app. config** localizado na pasta raiz do projeto **TodoListClient** e cole o valor no valor do parâmetro `ida:ClientId`

## <a name="run-your-project"></a>Executar o seu projeto

1. Pressione `<F5>` para executar o projeto. Seu *TodoListClient* deve ser aberto.
1. Selecione **entrar** no canto superior direito e entre com o mesmo usuário que você usou para registrar seu aplicativo ou um usuário no mesmo diretório.
1. Neste ponto, se você estiver entrando pela primeira vez, talvez seja solicitado a concordar com a API Web do *TodoListService* .
1. A entrada também solicita o token de acesso para o escopo de *access_as_user* para acessar a API Web *TodoListService* e manipular a lista de *tarefas pendentes* .

## <a name="pre-authorize-your-client-application"></a>Pré-autorizar o aplicativo cliente

Uma das maneiras de permitir que os usuários de outros diretórios acessem sua API Web é *pré-autorizando* os aplicativos cliente a acessar sua API Web adicionando as IDs de aplicativo dos aplicativos cliente na lista de aplicativos *previamente autorizados* para sua API Web. Ao adicionar um cliente previamente autorizado, você não precisará que o usuário consentisse para usar sua API Web. Siga as etapas abaixo para autorizar previamente seu aplicativo Web::

1. Volte para o *portal de registro do aplicativo* e abra as propriedades do seu **TodoListService**.
1. Na seção **expor uma API** , clique em **Adicionar um aplicativo cliente** na seção *aplicativos cliente autorizados* .
1. No campo *ID do cliente* , Cole a ID do aplicativo `TodoListClient` aplicativo.
1. Na seção *escopos autorizados* , selecione o escopo para esta API Web `api://<Application ID>/access_as_user`.
1. Pressione o botão **Adicionar aplicativo** na parte inferior da página.

## <a name="run-your-project"></a>Executar o seu projeto

1. Pressione `<F5>` para executar o projeto. Seu *TodoListClient* deve ser aberto.
1. Selecione **entrar** na parte superior direita (ou limpe o cache/entrada) e, em seguida, entre usando um conta Microsoft pessoal (live.com ou hotmail.com) ou uma conta corporativa ou de estudante.

## <a name="optional-restrict-sign-in-access-to-your-application"></a>Opcional: restringir o acesso de entrada ao seu aplicativo

Por padrão, quando você baixa este exemplo de código e configura o aplicativo para usar o ponto de extremidade Azure Active Directory v2 seguindo as etapas anteriores, as contas pessoais, como outlook.com, live.com e outras, bem como contas corporativas ou de estudante de qualquer as organizações integradas ao Azure AD podem solicitar tokens e acessar sua API da Web. 

Para restringir quem pode entrar em seu aplicativo, use uma das opções:

### <a name="option-1-restrict-access-to-a-single-organization-single-tenant"></a>Opção 1: restringir o acesso a uma única organização (locatário único)

Você pode restringir o acesso de entrada para seu aplicativo somente a contas de usuário que estejam em um único locatário do Azure AD, incluindo *contas de convidado* desse locatário. Esse cenário é comum para *aplicativos de linha de negócios*:

1. Abra o arquivo **App_Start \startup.auth** e altere o valor do ponto de extremidade de metadados que é passado para o `OpenIdConnectSecurityTokenProvider` para `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` (você também pode usar o nome do locatário, como `contoso.onmicrosoft.com`).
2. No mesmo arquivo, defina a propriedade `ValidIssuer` no `TokenValidationParameters` como `"https://sts.windows.net/{Tenant ID}/"` e o argumento `ValidateIssuer` como `true`.

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Opção 2: usar um método personalizado para validar os emissores

Você pode implementar um método personalizado para validar os emissores usando o parâmetro **IssuerValidator** . Para obter mais informações sobre como usar esse parâmetro, leia sobre a [classe TokenValidationParameters](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o cenário da API Web protegida com suporte da plataforma de identidade da Microsoft:
> [!div class="nextstepaction"]
> [Cenário da API Web protegida](scenario-protected-web-api-overview.md)
