---
title: A gestão da sessão REST API
description: Descreve como gerir sessões
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 9eb1bb87792dbc61e7c85dbc20c136499f23f67c
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530217"
---
# <a name="use-the-session-management-rest-api"></a>Utilizar a API REST de gestão de sessões

Para utilizar a funcionalidade de renderização remota Azure, precisa de criar uma *sessão*. Cada sessão corresponde a um servidor alocado no Azure ao qual um dispositivo cliente pode ligar-se. Quando um dispositivo se conecta, o servidor presta os dados solicitados e serve o resultado como um fluxo de vídeo. Durante a criação [da](../reference/vm-sizes.md) sessão, escolheu o tipo de servidor que pretende executar, o que determina o preço. Uma vez que a sessão não seja mais necessária, deve ser interrompida. Se não parar manualmente, será desligado automaticamente quando o tempo de *locação* da sessão expirar.

## <a name="rest-api-reference"></a>Referência da API REST

A referência REST API pode ser encontrada [aqui](https://docs.microsoft.com/rest/api/mixedreality/2021-01-01preview/remoterendering) e as definições de swagger [aqui.](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality)
Fornecemos um script PowerShell no [repositório de amostras ARR](https://github.com/Azure/azure-remote-rendering) na pasta *Scripts,* chamada *RenderingSession.ps1*, o que demonstra o uso do nosso serviço. O script e a sua configuração são descritos aqui: [Exemplo PowerShell scripts](../samples/powershell-example-scripts.md).
Também fornecemos SDKs para [.NET,](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/mixedreality/Azure.MixedReality.RemoteRendering)Java e Python.

> [!IMPORTANT]
> A latência é um fator importante na utilização da renderização remota. Para a melhor experiência crie sessões na região mais próxima. O [Teste de Latência Azure](https://www.azurespeed.com/Azure/Latency) pode ser usado para determinar qual a região mais próxima de si.

> [!IMPORTANT]
> É necessário um SDK de execução ARR para que um dispositivo cliente se conecte a uma sessão de renderização. Estes SDKs estão disponíveis em [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering?view=remoterendering) e [C++](https://docs.microsoft.com/cpp/api/remote-rendering/). Além da ligação ao serviço, estes SDKs também podem ser usados para iniciar e parar sessões.

## <a name="next-steps"></a>Passos seguintes

* [Utilização das APIs frontendas Azure para autenticação](frontend-apis.md)
* [Scripts do PowerShell de exemplo](../samples/powershell-example-scripts.md)
