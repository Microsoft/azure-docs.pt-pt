---
title: Utilize a autenticação Azure AD para aceder à API azure Media Services com .NET [ Microsoft Docs
description: Este tópico mostra como utilizar a autenticação Azure Ative Directory (Azure AD) para aceder à API Azure Media Services (AMS) com .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.custom: has-adal-ref
ms.openlocfilehash: 8fbe8e0cbf2768af973a0ccc9e237fb770b27a74
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612304"
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Utilize a autenticação Azure AD para aceder à API azure Media Services com .NET

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [a orientação de migração da v2 para a v3](../latest/migrate-from-v2-to-v3.md)

Começando pelo windowsazure.mediaservices 4.0.0.4, a Azure Media Services suporta a autenticação com base no Azure Ative Directory (Azure AD). Este tópico mostra como usar a autenticação Azure AD para aceder à API azure Media Services com a Microsoft .NET.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Para mais detalhes, consulte [o teste gratuito do Azure.](https://azure.microsoft.com/pricing/free-trial/)
- Uma conta dos Media Services. Para mais informações, consulte [Criar uma conta Azure Media Services utilizando o portal Azure](media-services-portal-create-account.md).
- O mais recente pacote [NuGet.](https://www.nuget.org/packages/windowsazure.mediaservices)
- Familiaridade com o tema [Accessing Azure Media Services API com visão geral da autenticação Azure AD.](media-services-use-aad-auth-to-access-ams-api.md)

Quando estiver a utilizar a autenticação Azure AD com a Azure Media Services, pode autenticar de uma de duas formas:

- **A autenticação** do utilizador autentica uma pessoa que está a usar a app para interagir com os recursos da Azure Media Services. A aplicação interativa deve primeiro solicitar ao utilizador credenciais. Um exemplo é uma aplicação de consola de gestão que é usada por utilizadores autorizados para monitorizar trabalhos de codificação ou streaming ao vivo.
- **A autenticação do diretor** de serviço autentica um serviço. Aplicações que usam comumente este método de autenticação são aplicações que executam serviços de daemon, serviços de nível médio ou empregos programados, tais como aplicações web, aplicações de função, aplicações lógicas, APIs ou microserviços.

>[!IMPORTANT]
>O Azure Media Service suporta atualmente um modelo de autenticação do Serviço de Controlo de Acesso Azure. No entanto, a autorização de Controlo de Acesso vai ser deprecinada no dia 22 de junho de 2018. Recomendamos que emigra para um modelo de autenticação de Diretório Ativo Azure o mais rapidamente possível.

## <a name="get-an-azure-ad-access-token"></a>Obter um token de acesso do Azure AD

Para ligar à API Azure Media Services com a autenticação Azure AD, a aplicação de clientes precisa de solicitar um sinal de acesso Azure AD. Quando utiliza o Cliente SDK dos Media Services .NET, muitos dos detalhes sobre como adquirir um token de acesso Azure AD são embrulhados e simplificados para si nas classes [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) e [AzureAdTokenCredentials.](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs)

Por exemplo, não precisa de fornecer a autoridade azure AD, recursos de serviços de mídia URI, ou detalhes nativos da aplicação Azure AD. Estes são valores bem conhecidos que já estão configurados pela classe de fornecedor de acesso a ad.ida e média.

Se não estiver a utilizar o Azure Media Service .NET SDK, recomendamos que utilize a Biblioteca de [Autenticação AD Azure](../../active-directory/azuread-dev/active-directory-authentication-libraries.md). Para obter valores para os parâmetros que precisa de utilizar com a Biblioteca de Autenticação AD Azure, consulte [Utilize o portal Azure para aceder às definições de autenticação Azure AD](media-services-portal-get-started-with-aad.md).

Também tem a opção de substituir a implementação padrão do **AzureAdTokenProvider** pela sua própria implementação.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Instalar e configurar os Serviços de Mídia Azure .NET SDK

>[!NOTE]
>Para utilizar a autenticação Azure AD com o Media Services .NET SDK, precisa de ter o mais recente pacote [NuGet.](https://www.nuget.org/packages/windowsazure.mediaservices) Além disso, adicione uma referência ao **conjunto Microsoft.IdentityModel.Clients.ActiveDirectory.** Se estiver a utilizar uma aplicação existente, inclua o **conjunto Microsoft.WindowsAzure.MediaServices.Client.Common.Common.Authentication.dll.**

1. Crie uma nova aplicação de consola C# no Estúdio Visual.
2. Utilize o pacote NuGet [windowsazure.mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) para instalar **o Azure Media Services .NET SDK**.

    Para adicionar referências utilizando o NuGet, tome os seguintes passos: no **Solution Explorer,** clique no nome do projeto e, em seguida, selecione **'Gerir pacotes NuGet**' . Em seguida, procure **windowsazure.mediaservices** e selecione **Instalar**.

    -ou-

    Executar o seguinte comando na **Consola de Gestor de Pacotes** em Estúdio Visual.

        Install-Package windowsazure.mediaservices -Version 4.0.0.4

3. Adicione **usando** o seu código fonte.

        using Microsoft.WindowsAzure.MediaServices.Client;

## <a name="use-user-authentication"></a>Utilizar a autenticação do utilizador

Para ligar à API do Serviço De Mídia Azure com a opção de autenticação do utilizador, a aplicação do cliente necessita de solicitar um token Azure AD utilizando os seguintes parâmetros:

- Ponto final do inquilino da AD Azure. A informação do inquilino pode ser recuperada do portal Azure. Paire sobre o utilizador inscrito no canto superior direito.
- Recurso URI dos Serviços de Media.
- Id do cliente de aplicação de Media Services (nativo).
- A aplicação de Media Services (nativa) redireciona o URI.

Os valores para estes parâmetros podem ser encontrados em **AzureEnvironments.AzureCloudEnvironment**. A constante **AzureEnvironments.AzureCloudEnvironment** é um ajudante no .NET SDK para obter as configurações variáveis ambientais certas para um Centro de Dados Azure público.

Contém configurações ambientais pré-definidas para aceder apenas aos Serviços de Media nos centros de dados públicos. Para regiões de nuvem soberana ou governamental, pode utilizar **o AzureChinaCloudEnvironment**, **AzureUsGovernmentEnvironment**ou **AzureGermanCloudEnvironment,** respectivamente.

O seguinte exemplo de código cria um símbolo:

    var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

Para iniciar a programação contra os Serviços de Media, é necessário criar uma instância **CloudMediaContext** que represente o contexto do servidor. O **CloudMediaContext** inclui referências a importantes coleções, incluindo empregos, ativos, ficheiros, políticas de acesso e localizadores.

Também precisa de passar o **recurso URI para Serviços de Descanso** de Mídia para o construtor **CloudMediaContext.** Para obter o recurso URI para Serviços Media REST, inscreva-se no portal Azure, selecione a sua conta Azure Media Services, selecione **acesso API**e, em seguida, selecione **Connect to Azure Media Services com autenticação do utilizador.**

O exemplo de código que se segue cria uma instância **CloudMediaContext:**

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

O exemplo que se segue mostra como criar o símbolo da AD Azure e o contexto:

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

>[!NOTE]
>Se tiver uma exceção que diga "O servidor remoto devolveu um erro: (401) Não autorizado", consulte a secção de controlo de [acesso](media-services-use-aad-auth-to-access-ams-api.md#access-control) da API de Acesso aos Serviços de Comunicação Social Azure com visão geral da autenticação Azure AD.

## <a name="use-service-principal-authentication"></a>Utilizar a autenticação principal do serviço

Para ligar à API azure Media Services com a opção principal de serviço, a sua aplicação de nível médio (Web API ou aplicação web) precisa de solicitar um token Azure AD com os seguintes parâmetros:

- Ponto final do inquilino da AD Azure. A informação do inquilino pode ser recuperada do portal Azure. Paire sobre o utilizador inscrito no canto superior direito.
- Recurso URI dos Serviços de Media.
- Valores de aplicação Azure AD: o ID do **cliente** e **o segredo do cliente.**

Os valores para os parâmetros **secretos** de ID do **Cliente** e Cliente podem ser encontrados no portal Azure. Para mais informações, consulte [Iniciar-se com a autenticação Azure AD através do portal Azure](media-services-portal-get-started-with-aad.md).

O seguinte exemplo de código cria um símbolo utilizando o construtor **AzureAdTokenCredenciais** que toma o **AzureAdClientSymmetricKey** como parâmetro:

    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                                new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"),
                                AzureEnvironments.AzureCloudEnvironment);

    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

Também pode especificar o construtor **AzureAdTokenCredenciaque** que toma o **AzureAdClientCertificate** como parâmetro.

Para obter instruções sobre como criar e configurar um certificado de forma a ser utilizada pela Azure AD, consulte [Autenticação para AD Azure em aplicações daemon com certificados - passos](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)de configuração manual .

    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                                new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"),
                                AzureEnvironments.AzureCloudEnvironment);

Para iniciar a programação contra os Serviços de Media, é necessário criar uma instância **CloudMediaContext** que represente o contexto do servidor. Também precisa de passar o **recurso URI para Serviços de Descanso** de Mídia para o construtor **CloudMediaContext.** Também pode obter o valor uri **de recursos para serviços** de descanso de mídia do portal Azure.

O exemplo de código que se segue cria uma instância **CloudMediaContext:**

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

O exemplo que se segue mostra como criar o símbolo da AD Azure e o contexto:

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

## <a name="next-steps"></a>Passos seguintes

Começa com [o upload de ficheiros para a sua conta.](media-services-dotnet-upload-files.md)
