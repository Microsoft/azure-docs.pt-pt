---
title: Utilize a autenticação Azure AD para aceder à Azure Media Services API com .NET | Microsoft Docs
description: Este tópico mostra como utilizar a autenticação do Azure Ative Directory (Azure AD) para aceder à Azure Media Services (AMS) API com .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: fd19bce05d18eb8f9b8fd3806082caeebe98bdad
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013997"
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Utilize a autenticação Azure AD para aceder à Azure Media Services API com .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Começando pelos windowsazure.mediaservices 4.0.0.4, a Azure Media Services suporta a autenticação com base no Azure Ative Directory (Azure AD). Este tópico mostra-lhe como utilizar a autenticação Azure AD para aceder à Azure Media Services API com a Microsoft .NET.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Para mais detalhes, consulte [o julgamento gratuito do Azure.](https://azure.microsoft.com/pricing/free-trial/)
- Uma conta dos Media Services. Para mais informações, consulte [Criar uma conta Azure Media Services utilizando o portal Azure](media-services-portal-create-account.md).
- O último pacote [nuGet.](https://www.nuget.org/packages/windowsazure.mediaservices)
- Familiaridade com o tema [Acesso a Azure Media Services API com visão geral de autenticação AD Azure](media-services-use-aad-auth-to-access-ams-api.md).

Quando estiver a utilizar a autenticação AD AZure com a Azure Media Services, pode autenticar de duas formas:

- **A autenticação do utilizador** autentica uma pessoa que está a usar a app para interagir com os recursos do Azure Media Services. A aplicação interativa deve primeiro solicitar ao utilizador credenciais. Um exemplo é uma aplicação de consola de gestão que é usada por utilizadores autorizados para monitorizar trabalhos de codificação ou streaming ao vivo.
- **A autenticação principal do serviço** autentica um serviço. As aplicações que normalmente utilizam este método de autenticação são aplicações que executam serviços daemon, serviços de nível médio ou trabalhos programados, tais como aplicações web, apps de funções, apps lógicas, APIs ou microserviços.

>[!IMPORTANT]
>A Azure Media Service suporta atualmente um modelo de autenticação do Serviço de Controlo de Acesso Azure. No entanto, a autorização do Controlo de Acesso vai ser depreovada no dia 22 de junho de 2018. Recomendamos que emigre para um modelo de autenticação Azure Ative Directory o mais rapidamente possível.

## <a name="get-an-azure-ad-access-token"></a>Obter um token de acesso do Azure AD

Para se ligar à Azure Media Services API com a autenticação AZURE AD, a aplicação do cliente precisa de solicitar um token de acesso AD Azure. Quando utiliza o cliente SDK dos Media Services .NET, muitos dos detalhes sobre como adquirir um token de acesso AD AD Azure são embrulhados e simplificados para si nas aulas [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) e [AzureAdTokenCredentials.](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs)

Por exemplo, não precisa de fornecer a autoridade AD AD Azure, recursos de media URI ou detalhes da aplicação AZure AD nativa. Estes são valores bem conhecidos que já estão configurados pela classe de fornecedor de acesso Azure AD.

Se não estiver a utilizar o Azure Media Service .NET SDK, recomendamos que utilize a Biblioteca de [Autenticação AD Azure](../../active-directory/azuread-dev/active-directory-authentication-libraries.md). Para obter valores para os parâmetros que necessita de utilizar com a Biblioteca de Autenticação AD Azure, consulte [utilizar o portal Azure para aceder às definições de autenticação AD Azure](media-services-portal-get-started-with-aad.md).

Também tem a opção de substituir a implementação padrão do **AzureAdTokenProvider** pela sua própria implementação.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Instalar e configurar a Azure Media Services .NET SDK

>[!NOTE]
>Para utilizar a autenticação AZure AD com os Serviços de Mídia .NET SDK, precisa de ter o mais recente pacote [NuGet.](https://www.nuget.org/packages/windowsazure.mediaservices) Além disso, adicione uma referência ao conjunto **Microsoft.IdentityModel.Clients.ActiveDirectory.** Se estiver a utilizar uma aplicação existente, inclua o **conjuntoMicrosoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll.**

1. Crie uma nova aplicação de consola C# no Visual Studio.
2. Utilize o pacote [NuGet windowsazure.mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) para instalar **o Azure Media Services .NET SDK**.

    Para adicionar referências utilizando o NuGet, tome os seguintes passos: no **Solution Explorer,** clique com o nome do projeto à direita e, em seguida, selecione **Gerir pacotes NuGet**. Em seguida, procure **por windowsazure.mediaservices** e selecione **Instalar**.

    -ou-

    Executar o seguinte comando na **Consola Package Manager** em Estúdio Visual.

    ```console
    Install-Package windowsazure.mediaservices -Version 4.0.0.4
    ```

3. Adicione **o uso** do seu código fonte.

    ```csharp
    using Microsoft.WindowsAzure.MediaServices.Client;
    ```

## <a name="use-user-authentication"></a>Utilize a autenticação do utilizador

Para se ligar à Azure Media Service API com a opção de autenticação do utilizador, a aplicação do cliente precisa de solicitar um token AD Azure utilizando os seguintes parâmetros:

- Azure AD inquilino ponto final. A informação do inquilino pode ser recuperada a partir do portal Azure. Passe por cima do utilizador inscrito no canto superior direito.
- Recursos de mídia URI.
- Identificação do cliente de aplicação dos Media Services (nativo).
- Aplicação media Services (nativa) redireciona URI.

Os valores para estes parâmetros podem ser encontrados em **AzureEnvironments.AzureCloudEnvironment**. A constante **AzureEnvironments.AzureCloudEnvironment** é um ajudante no .NET SDK para obter as definições variáveis do ambiente certo para um Centro de Dados Azure público.

Contém configurações de ambiente pré-definidas para aceder apenas aos Serviços de Comunicação Social nos centros de dados públicos. Para regiões de nuvem soberana ou governamental, você pode usar **AzureChinaCloudEnonment**, **AzureUsGovernmentEnvironment**, ou **AzureGermanCloudEnvironment,** respectivamente.

O seguinte exemplo de código cria um símbolo:

```csharp
var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
```

Para iniciar a programação contra os Media Services, é necessário criar uma instância **CloudMediaContext** que represente o contexto do servidor. O **CloudMediaContext** inclui referências a importantes coleções, incluindo empregos, ativos, ficheiros, políticas de acesso e localizadores.

Também precisa de passar o **recurso URI para serviços de rest de mídia** para o construtor **CloudMediaContext.** Para obter o recurso URI para Serviços de DESCANSO de Mídia, faça o sômed para o portal Azure, selecione a sua conta Azure Media Services, selecione **acesso API** e, em seguida, selecione **Connect to Azure Media Services com autenticação do utilizador.**

O seguinte exemplo de código cria uma instância **CloudMediaContext:**

```csharp
CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
```

O exemplo a seguir mostra como criar o token AD Azure e o contexto:

```csharp
namespace AzureADAuthSample
{
    class Program
    {
        static void Main(string[] args)
        {
            // Specify your Azure AD tenant domain, for example "microsoft.onmicrosoft.com".
            var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
            CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

            var assets = context.Assets;
            foreach (var a in assets)
            {
                Console.WriteLine(a.Name);
            }
        }
    }
}
```

>[!NOTE]
>Se receber uma exceção que diz "O servidor remoto devolveu um erro: (401) Não Autorizado", consulte a secção de controlo de [acesso](media-services-use-aad-auth-to-access-ams-api.md#access-control) da Azure Media Services API com visão geral da autenticação Azure AD.

## <a name="use-service-principal-authentication"></a>Utilizar a autenticação principal do serviço

Para se ligar à Azure Media Services API com a opção principal de serviço, a sua aplicação de nível médio (Web API ou aplicação web) precisa de solicitar um token AD Azure com os seguintes parâmetros:

- Azure AD inquilino ponto final. A informação do inquilino pode ser recuperada a partir do portal Azure. Passe por cima do utilizador inscrito no canto superior direito.
- Recursos de mídia URI.
- Valores de aplicação AD Azure: o **ID do Cliente** e **o segredo do Cliente.**

Os valores para o **ID** do Cliente e parâmetros **secretos** do Cliente podem ser encontrados no portal Azure. Para obter mais informações, consulte [Começar com a autenticação Azure AD utilizando o portal Azure.](media-services-portal-get-started-with-aad.md)

O seguinte exemplo de código cria um símbolo utilizando o construtor **AzureAdTokenCredentials** que toma **a AzureAdClientSymmetricKey** como parâmetro:

```csharp
var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                        new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"),
                        AzureEnvironments.AzureCloudEnvironment);

var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
```

Também pode especificar o construtor **AzureAdTokenCredentials** que toma **O AzureAdClientCertificate** como parâmetro.

Para obter instruções sobre como criar e configurar um certificado de forma a poder ser utilizado pela Azure AD, consulte [Autenticação a AD AD em aplicações danémonas com certificados - etapas de configuração manual](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2).

```csharp
var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                        new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"),
                        AzureEnvironments.AzureCloudEnvironment);
```

Para iniciar a programação contra os Media Services, é necessário criar uma instância **CloudMediaContext** que represente o contexto do servidor. Também precisa de passar o **recurso URI para serviços de rest de mídia** para o construtor **CloudMediaContext.** Também pode obter o **valor URI de recursos para serviços de mídia** do portal Azure.

O seguinte exemplo de código cria uma instância **CloudMediaContext:**

```csharp
CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
```

O exemplo a seguir mostra como criar o token AD Azure e o contexto:

```csharp
namespace AzureADAuthSample
{
    class Program
    {
        static void Main(string[] args)
        {
            var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                                        new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"),
                                        AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
            CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

            var assets = context.Assets;
            foreach (var a in assets)
            {
                Console.WriteLine(a.Name);
            }

            Console.ReadLine();
        }
    }
}
```

## <a name="next-steps"></a>Passos seguintes

Começa com [o upload de ficheiros para a tua conta.](media-services-dotnet-upload-files.md)
