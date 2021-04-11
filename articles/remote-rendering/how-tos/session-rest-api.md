---
title: A gestão da sessão REST API
description: Descreve como gerir sessões
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 4323884b2dee3eeccfe71ec7817d92467450e88e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950030"
---
# <a name="use-the-session-management-rest-api"></a>Utilizar a API REST de gestão de sessões

Para utilizar a funcionalidade de renderização remota Azure, precisa de criar uma *sessão*. Cada sessão corresponde a um servidor alocado no Azure ao qual um dispositivo cliente pode ligar-se. Quando um dispositivo se conecta, o servidor presta os dados solicitados e serve o resultado como um fluxo de vídeo. Durante a criação [da](../reference/vm-sizes.md) sessão, escolheu o tipo de servidor que pretende executar, o que determina o preço. Uma vez que a sessão não seja mais necessária, deve ser interrompida. Se não parar manualmente, será desligado automaticamente quando o tempo de *locação* da sessão expirar.

## <a name="rest-api-reference"></a>Referência da API REST

A referência REST API pode ser encontrada [aqui](/rest/api/mixedreality/2021-01-01preview/remoterendering) e as definições de swagger [aqui.](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality)
Fornecemos um script PowerShell no [repositório de amostras ARR](https://github.com/Azure/azure-remote-rendering) na pasta *Scripts,* chamada *RenderingSession.ps1*, o que demonstra o uso do nosso serviço. O script e a sua configuração são descritos aqui: [Exemplo PowerShell scripts](../samples/powershell-example-scripts.md).
Também fornecemos SDKs para [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/remoterendering/Azure.MixedReality.RemoteRendering/README.md) e [Java.](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/remoterendering/azure-mixedreality-remoterendering/README.md)

> [!IMPORTANT]
> A latência é um fator importante na utilização da renderização remota. Para a melhor experiência crie sessões na região mais próxima. O [Teste de Latência Azure](https://www.azurespeed.com/Azure/Latency) pode ser usado para determinar qual a região mais próxima de si.

> [!IMPORTANT]
> É necessário um SDK de execução ARR para que um dispositivo cliente se conecte a uma sessão de renderização. Estes SDKs estão disponíveis em [.NET](/dotnet/api/microsoft.azure.remoterendering) e [C++](/cpp/api/remote-rendering/). Além da ligação ao serviço, estes SDKs também podem ser usados para iniciar e parar sessões.

## <a name="next-steps"></a>Passos seguintes

* [Utilização das APIs frontendas Azure para autenticação](frontend-apis.md)
* [Scripts do PowerShell de exemplo](../samples/powershell-example-scripts.md)