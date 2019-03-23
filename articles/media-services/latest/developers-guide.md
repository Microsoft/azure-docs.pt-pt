---
title: V3 SDKs - Azure dos serviços de multimédia do Azure
description: Este artigo fornece uma descrição geral de como começar a desenvolver com a API do serviços de multimédia v3 com ferramentas/SDKs.
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
ms.date: 03/20/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: cda6c9cd1f9c8b9305349f0904aeb744ba373711
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58350254"
---
# <a name="start-developing-with-media-services-v3-api-using-sdkstools"></a>Comece a desenvolver com os serviços de multimédia v3 API usando ferramentas/SDKs

Como desenvolvedor, pode utilizar os serviços de multimédia [REST API](https://aka.ms/ams-v3-rest-ref) ou bibliotecas de cliente que permitem-lhe interagir com a API de REST para facilmente criar, gerir e manter fluxos de trabalho de suporte de dados personalizado. O [serviços de multimédia v3](https://aka.ms/ams-v3-rest-sdk) API baseia-se a especificação de OpenAPI (anteriormente conhecida como um Swagger).

Este tópico fornece ligações para os SDKs, ferramentas, documentação. Ele também fornece algumas informações úteis para env de desenvolvimento diferentes.

## <a name="prerequisites"></a>Pré-requisitos

Para começar a desenvolver em relação a serviços de multimédia, terá de:

- Uma subscrição ativa do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [Saiba mais sobre os conceitos fundamentais](concepts-overview.md)
- [Criar uma conta de serviços de multimédia](create-account-cli-how-to.md)

## <a name="start-developing"></a>Começar a desenvolver

Serviços de multimédia do Azure suporta as seguintes ferramentas/SDKs 

- [CLI do Azure](https://aka.ms/ams-v3-cli) 
- [SDK do .NET](https://aka.ms/ams-v3-dotnet-sdk)
- [SDK Java](https://aka.ms/ams-v3-java-sdk)
- [Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk)
- [Python SDK](https://aka.ms/ams-v3-python-sdk)
- [Go SDK](https://aka.ms/ams-v3-go-sdk)
- [SDK Ruby](https://aka.ms/ams-v3-ruby-sdk)

### <a name="azure-media-services-explorer"></a>Explorador dos Serviços de Multimédia do Azure

[Explorador dos serviços de multimédia do Azure](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) é uma ferramenta disponível para os clientes do Windows que desejam saber mais sobre os serviços de multimédia. AMSE é uma Winforms /C# aplicação que carregar, transferir, codificar, transmitir em fluxo VOD e live conteúdo com os Media Services. A ferramenta AMSE é para os clientes que desejam testar os serviços de multimédia sem escrever nenhum código. O código AMSE é fornecido como um recurso para os clientes que pretendem desenvolver com os serviços de multimédia.

AMSE é um projeto de código-fonte aberto, o suporte é fornecido pela Comunidade (problemas podem ser reportados para https://github.com/Azure/Azure-Media-Services-Explorer/issues). Este projeto adotou o [Microsoft Open Source Code of Conduct (Código de Conduta do Microsoft Open Source)](https://opensource.microsoft.com/codeofconduct/). Para obter mais informações, consulte a [código de conduta FAQ](https://opensource.microsoft.com/codeofconduct/faq/) ou contacte opencode@microsoft.com com quaisquer perguntas ou comentários adicionais.

## <a name="rest"></a>REST

Para começar a desenvolver com as APIs de REST de serviços de multimédia, instale um cliente REST. Por exemplo **Postman**, **Visual Studio Code** com o plug-in do REST, ou **Telerik Fiddler**. Estamos a utilizar [Postman](media-rest-apis-with-postman.md) para exemplos do serviços de multimédia v3.

Explore os serviços de multimédia [ref de REST API](https://aka.ms/ams-v3-rest-ref) documentação e confira estes exemplos:

- [Tutorial: Um ficheiro remoto com base no URL de codificar e transmitir o vídeo - REST](stream-files-tutorial-with-rest.md)
- [Carregar ficheiros para uma conta de Media Services - REST](upload-files-rest-how-to.md)
- [Criar filtros com serviços de multimédia - REST](filters-dynamic-manifest-rest-howto.md)
- [API REST baseada no Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates)

<!-- ## CLI -->
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

Explore os serviços de multimédia [ref da CLI do Azure](https://aka.ms/ams-v3-cli-ref) documentação e confira estes exemplos:

- [Unidades - CLI de reservadas de multimédia de dimensionamento](media-reserved-units-cli-how-to.md)
- [Criar uma conta de Media Services - CLI](./scripts/cli-create-account.md) 
- [Repor as credenciais da conta - CLI](./scripts/cli-reset-account-credentials.md)
- [Criar recursos - CLI](./scripts/cli-create-asset.md)
- [Carregar um ficheiro - CLI](./scripts/cli-upload-file-asset.md)
- [Criar transformações - CLI](./scripts/cli-create-transform.md)
- [Criar tarefas - CLI](./scripts/cli-create-jobs.md)
- [Criar EventGrid - CLI](./scripts/cli-create-event-grid.md)
- [Publicar um elemento - CLI](./scripts/cli-publish-asset.md)
- [Filtro - CLI](filters-dynamic-manifest-cli-howto.md)

## <a name="net"></a>.NET

Explore os serviços de multimédia [.NET ref](https://aka.ms/ams-v3-dotnet-ref) documentação e confira estes exemplos:

- [Tutorial: Carregar, codificar e transmitir vídeos - .NET](stream-files-tutorial-with-api.md) 
- [Tutorial: Stream em direto com o serviços de multimédia v3 - .NET](stream-live-tutorial-with-api.md)
- [Tutorial: Analisar vídeos com os serviços de multimédia v3 - .NET](analyze-videos-tutorial-with-api.md)
- [Criar uma entrada da tarefa a partir de um ficheiro local - .NET](job-input-from-local-file-how-to.md)
- [Criar uma entrada da tarefa a partir de um URL HTTPS - .NET](job-input-from-http-how-to.md)
- [Codificar com uma transformação personalizados - .NET](customize-encoder-presets-how-to.md)
- [Utilizar a encriptação dinâmica de AES-128 e o serviço de entrega de chave - .NET](protect-with-aes128.md)
- [Utilizar DRM dinâmico licença e de encriptação de serviço de entrega - .NET](protect-with-drm.md)
- [Obter uma chave de assinatura da política existente - .NET](get-content-key-policy-dotnet-howto.md)
- [Criar filtros com serviços de multimédia - .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Advanced vídeo sob demanda exemplos de funções do Azure v2 com serviços de multimédia v3](https://aka.ms/ams3functions)

## <a name="java"></a>Java

Reveja os serviços de multimédia [Java ref](https://aka.ms/ams-v3-java-ref) documentação.

## <a name="nodejs"></a>Node.js

Explore os serviços de multimédia [ref de node. js](https://aka.ms/ams-v3-nodejs-ref) documentação e check-out [exemplos](https://github.com/Azure-Samples/media-services-v3-node-tutorials) que mostram como utilizar a API de serviços de multimédia com node. js.

## <a name="python"></a>Python

Reveja os serviços de multimédia [Python ref](https://aka.ms/ams-v3-python-ref) documentação.

## <a name="go"></a>Ir

Reveja os serviços de multimédia [Go ref](https://aka.ms/ams-v3-go-ref) documentação.

## <a name="next-steps"></a>Passos Seguintes

- [Criar uma conta - CLI](create-account-cli-how-to.md)
- [Aceder a APIs - CLI](access-api-cli-how-to.md)

