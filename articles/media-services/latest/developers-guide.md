---
title: V3 SDKs - Azure dos serviços de multimédia do Azure
description: Este artigo fornece uma descrição geral de como começar a desenvolver com a API do serviços de multimédia v3 com SDKs.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 04/11/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 9fb4d1561a661387f759aada9e776d43a95aa5c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60732455"
---
# <a name="develop-against-media-services-v3-api-using-sdks"></a>Desenvolver em relação a serviços de multimédia v3 API através de SDKs

Como desenvolvedor, pode utilizar os serviços de multimédia [REST API](https://aka.ms/ams-v3-rest-ref) ou bibliotecas de cliente que permitem-lhe interagir com a API de REST para facilmente criar, gerir e manter fluxos de trabalho de suporte de dados personalizado. O [serviços de multimédia v3](https://aka.ms/ams-v3-rest-sdk) API baseia-se a especificação de OpenAPI (anteriormente conhecida como um Swagger).

> [!NOTE]
> Não são garantidos que os SDKs do Media Services do Azure v3 ser thread-safe. Ao desenvolver um aplicativo multithreaded, deve adicionar a sua própria lógica de sincronização de thread para proteger o cliente ou utilize um novo objeto de AzureMediaServicesClient por thread. Também deve ter cuidado dos problemas de multithreading introduzidos por objetos opcionais fornecidos pelo seu código para o cliente (como uma instância do HttpClient no .NET).

Este tópico fornece ligações para os SDKs, ferramentas, guias de procedimentos.

## <a name="prerequisites"></a>Pré-requisitos

Para começar a desenvolver em relação a serviços de multimédia, terá de:

- Uma subscrição ativa do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [Saiba mais sobre os conceitos fundamentais](concepts-overview.md)
- Revisão [desenvolver com o suporte de dados dos serviços de v3 APIs](media-services-apis-overview.md)
- [Criar uma conta de Media Services - CLI](create-account-cli-how-to.md)

## <a name="start-developing-with-sdks"></a>Começar a desenvolver com SDKs

### <a name="net"></a>.NET

Uso [.NET SDK](https://aka.ms/ams-v3-dotnet-sdk) para [ligar a serviços de multimédia](configure-connect-dotnet-howto.md).

Explore os serviços de multimédia [referência de .NET](https://aka.ms/ams-v3-dotnet-ref) documentação.

### <a name="java"></a>Java

Uso [SDK de Java](https://aka.ms/ams-v3-java-sdk) para [ligar a serviços de multimédia](configure-connect-java-howto.md).

Reveja os serviços de multimédia [referência de Java](https://aka.ms/ams-v3-java-ref) documentação.

### <a name="nodejs"></a>Node.js

Uso [SDK node. js](https://aka.ms/ams-v3-nodejs-sdk) ao [ligar a serviços de multimédia](configure-connect-nodejs-howto.md).

Explore os serviços de multimédia [referência de node. js](https://aka.ms/ams-v3-nodejs-ref) documentação e check-out [exemplos](https://github.com/Azure-Samples/media-services-v3-node-tutorials) que mostram como utilizar a API de serviços de multimédia com node. js.

### <a name="python"></a>Python

Uso [Python SDK](https://aka.ms/ams-v3-python-sdk).

Reveja os serviços de multimédia [referência de Python](https://aka.ms/ams-v3-python-ref) documentação.

### <a name="go"></a>Ir

Uso [Go SDK](https://aka.ms/ams-v3-go-sdk).

Reveja os serviços de multimédia [ir referência](https://aka.ms/ams-v3-go-ref) documentação.

### <a name="ruby"></a>Ruby

Uso [Ruby SDK](https://aka.ms/ams-v3-ruby-sdk).

## <a name="azure-media-services-explorer"></a>Explorador dos Serviços de Multimédia do Azure

[Explorador dos serviços de multimédia do Azure](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) é uma ferramenta disponível para os clientes do Windows que desejam saber mais sobre os serviços de multimédia. AMSE é uma Winforms /C# aplicação que carregar, transferir, codificar, transmitir em fluxo VOD e live conteúdo com os Media Services. A ferramenta AMSE é para os clientes que desejam testar os serviços de multimédia sem escrever nenhum código. O código AMSE é fornecido como um recurso para os clientes que pretendem desenvolver com os serviços de multimédia.

AMSE é um projeto de código-fonte aberto, o suporte é fornecido pela Comunidade (problemas podem ser reportados para https://github.com/Azure/Azure-Media-Services-Explorer/issues). Este projeto adotou o [Microsoft Open Source Code of Conduct (Código de Conduta do Microsoft Open Source)](https://opensource.microsoft.com/codeofconduct/). Para obter mais informações, consulte a [código de conduta FAQ](https://opensource.microsoft.com/codeofconduct/faq/) ou contacte opencode@microsoft.com com quaisquer perguntas ou comentários adicionais.

## <a name="next-steps"></a>Passos Seguintes

[Descrição geral](media-services-overview.md)
