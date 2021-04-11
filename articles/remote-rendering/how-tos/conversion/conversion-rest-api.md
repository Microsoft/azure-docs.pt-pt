---
title: A conversão de ativos REST API
description: Descreve como converter um ativo através da API REST
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: e3db4b9c9b4a05142f1327f681b067748cb1a2f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951645"
---
# <a name="use-the-model-conversion-rest-api"></a>Utilizar a API REST do modelo de conversão

O serviço [de conversão de modelos](model-conversion.md) é controlado através de uma [API REST](https://en.wikipedia.org/wiki/Representational_state_transfer). Esta API pode ser usada para criar conversões, obter propriedades de conversão e listar as conversões existentes.

## <a name="rest-api-reference"></a>Referência da API REST

A documentação de referência da API de renderização remota rest pode ser consultada [aqui,](/rest/api/mixedreality/2021-01-01/remoterendering)e as definições de swagger [aqui](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality).

Fornecemos um script PowerShell no [repositório de amostras ARR](https://github.com/Azure/azure-remote-rendering) na pasta *Scripts,* chamada *Conversion.ps1*, o que demonstra o uso do nosso serviço. O script e a sua configuração são descritos aqui: [Exemplo PowerShell scripts](../../samples/powershell-example-scripts.md). Também fornecemos SDKs para [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/remoterendering/Azure.MixedReality.RemoteRendering/README.md) e [Java.]( https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/remoterendering/azure-mixedreality-remoterendering/README.md)

## <a name="next-steps"></a>Passos seguintes

- [Utilizar o Armazenamento de Blobs do Azure para conversão de modelos](blob-storage.md)
- [Conversão de modelo](model-conversion.md)