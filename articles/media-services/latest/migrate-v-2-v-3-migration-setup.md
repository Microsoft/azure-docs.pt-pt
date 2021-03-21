---
title: Comunicação social v2 para v3 configuração de migração
description: Este artigo irá ajudá-lo a configurar o seu ambiente para migrar da Azure Media Services v2 para v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: serviços de mídia azul, migração, stream, transmissão, ao vivo, SDK
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2021
ms.author: inhenkel
ms.openlocfilehash: 792f4f81d84b61f0f78c53e62168921221b4f330
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503169"
---
# <a name="step-3---set-up-to-migrate-to-the-v3-rest-api-or-client-sdk"></a>Passo 3 - Configurar para migrar para a V3 REST API ou cliente SDK

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![etapas de migração 2](./media/migration-guide/steps-3.svg)

O seguinte descreve os passos a tomar para configurar o seu ambiente para utilizar a API dos Serviços de Mídia V3.

## <a name="sdk-model"></a>Modelo SDK

Na V2 API, existiam dois SDKs clientes diferentes, um para a gestão da API, que permitiu a criação programática de contas, e um para gestão de recursos.

Anteriormente, os desenvolvedores trabalhariam com um ID principal do serviço Azure e segredo de cliente, juntamente com um ponto final específico V2 REST API para a sua conta AMS.

A V3 API é baseada em Azure Resource Management (ARM). Utiliza iDs principais de serviço do Azure Ative (Azure AD) e chaves para ligar à API. Os desenvolvedores terão de criar princípios de serviço ou identidades geridas para se conectarem à API. Na API V3, a API utiliza pontos finais ARM standard, utiliza um modelo semelhante e consistente a todos os outros serviços Azure.

Os clientes que utilizem previamente a versão 2015-10-01 da API de gestão ARM para gerir as suas contas V2 deverão utilizar a versão 2020-05-01 da API de gestão ARM suportada para o acesso à API V3.

## <a name="create-a-new-media-services-account-for-testing"></a>Criar uma nova conta de serviços de comunicação para testes

Siga os passos de arranque rápido para [configurar o seu ambiente](how-to-set-azure-subscription.md?tabs=portal) utilizando o portal Azure. Selecione a autenticação principal de acesso e serviço da API para gerar um novo ID de aplicação AD Azure e segredos para uso com esta conta de teste.

[Criar uma conta de serviços de comunicação](create-account-howto.md?tabs=portal)social.
[Obtenha credenciais para aceder à API dos Serviços de Mídia.](access-api-howto.md?tabs=portal)

## <a name="download-client-sdk-of-your-choice-and-set-up-your-environment"></a>Baixe o cliente SDK à sua escolha e crie o seu ambiente

- SDKs disponíveis para [.NET](/dotnet/api/overview/azure/mediaservices/management), .NET Core, [Node.js](/javascript/api/overview/azure/mediaservices/management), [Python,](/python/api/overview/azure/mediaservices/management) [Java,](/java/api/overview/azure/mediaservices/management) [Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media)e [Ruby.](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
- [Azure CLI](/cli/azure/ams)   integração para suporte simples scripting.

> [!NOTE]
> Uma comunidade PHP SDK já não está disponível para a Azure Media Services na V3. Se estiver a utilizar PHP em V2, deverá migrar diretamente para a API REST no seu código.

## <a name="open-api-specifications"></a>Abrir especificações da API

- A V3 baseia-se numa superfície unificada da API, que expõe a funcionalidade de gestão e operações construída no Azure Resource Manager. Os modelos do Azure Resource Manager podem ser usados para criar e implementar transformações, streaming de pontos finais, eventos ao vivo e muito mais.

- O documento [OpenAPI Specification](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01) (anteriormente chamado Swagger) explica o esquema para todos os componentes de serviço.

- Todos os SDKs de cliente são derivados e gerados a partir da especificação Open API publicada no GitHub. No momento da publicação deste artigo, as mais recentes especificações da API aberta são mantidas publicamente no GitHub. A versão 2020-05-01 é o [mais recente lançamento estável](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01).

## <a name="rest"></a>[REST](#tab/rest)

Use [carteiro](./media-rest-apis-with-postman.md) para serviços de mídia v3 REST chamadas API.
Leia as [páginas de referência da API REST](/rest/api/media/).

Deve utilizar a cadeia de versão 2020-05-01 na coleção Do Carteiro.

## <a name="net"></a>[.NET](#tab/net)

Leia o artigo, [Ligue-se aos Serviços de Mídia v3 API com .NET](configure-connect-dotnet-howto.md) para configurar o seu ambiente.

Se simplesmente pretender instalar o SDK mais recente utilizando o PackageManager, utilize o seguinte comando:

```Install-Package Microsoft.Azure.Management.Media```

Ou para instalar o SDK mais recente utilizando o .NET CLI utilizar o seguinte comando:

```dotnet add package Microsoft.Azure.Management.Media```

Adicionalmente, as amostras completas .NET estão disponíveis em [Azure-Samples/media-services-v3-dotnet](https://github.com/Azure-Samples/media-services-v3-dotnet) para vários cenários. Os projetos neste repositório mostram como implementar diferentes cenários da Azure Media Services utilizando a versão v3.

### <a name="get-started-adjusting-your-code"></a>Começa a ajustar o teu código

Pesse na sua base de código por exemplos de `CloudMediaContext` utilização para iniciar o processo de atualização para a API V3.

O código que se segue mostra como a API v2 foi previamente acedida utilizando o V2 .NET SDK. Os desenvolvedores começariam por criar um `CloudMediaContext` e criar um caso com um `AzureAdTokenCredentials` objeto.

```dotnet

class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

```

## <a name="java"></a>[Java](#tab/java)

Leia o artigo, [Ligue-se aos Serviços de Media v3 API com a Java](configure-connect-java-howto.md) para configurar o seu ambiente.

## <a name="python"></a>[Python](#tab/python)

Leia o artigo, [Connect to Azure Media Services v3 API - Python](configure-connect-python-howto.md) para configurar o seu ambiente.

## <a name="nodejs"></a>[Node.js](#tab/nodejs)

Leia o artigo [Ligue-se ao Azure Media Services v3 API - Node.js](configure-connect-nodejs-howto.md) para configurar o seu ambiente.

## <a name="ruby"></a>[Ruby](#tab/ruby)

- Pegue o [SDK Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md) no GitHub.

## <a name="go"></a>[Ir](#tab/go)

Descarregue o [Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media) SDK.

---

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]