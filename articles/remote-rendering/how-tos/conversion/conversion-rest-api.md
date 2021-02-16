---
title: A conversão de ativos REST API
description: Descreve como converter um ativo através da API REST
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 4f8ac72e2b598a6c7631d691cc1bfb82cdba7599
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530268"
---
# <a name="use-the-model-conversion-rest-api"></a>Utilizar a API REST do modelo de conversão

O serviço [de conversão de modelos](model-conversion.md) é controlado através de uma [API REST](https://en.wikipedia.org/wiki/Representational_state_transfer). Esta API pode ser usada para criar conversões, obter propriedades de conversão e listar as conversões existentes.

## <a name="rest-api-reference"></a>Referência da API REST

A documentação de referência da API de renderização remota rest pode ser consultada [aqui,](https://docs.microsoft.com/rest/api/mixedreality/2021-01-01preview/remoterendering)e as definições de swagger [aqui](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality).

Fornecemos um script PowerShell no [repositório de amostras ARR](https://github.com/Azure/azure-remote-rendering) na pasta *Scripts,* chamada *Conversion.ps1*, o que demonstra o uso do nosso serviço. O script e a sua configuração são descritos aqui: [Exemplo PowerShell scripts](../../samples/powershell-example-scripts.md). Também fornecemos SDKs para [.NET,](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/mixedreality/Azure.MixedReality.RemoteRendering)Java e Python.

## <a name="next-steps"></a>Passos seguintes

- [Utilizar o Armazenamento de Blobs do Azure para conversão de modelos](blob-storage.md)
- [Conversão de modelo](model-conversion.md)
