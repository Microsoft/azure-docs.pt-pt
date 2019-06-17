---
title: Consultar dados de um ambiente de disponibilidade geral do Azure Time Series Insights com C# código | Documentos da Microsoft
description: Este artigo descreve como consultar dados a partir de um ambiente do Azure Time Series Insights codificando uma aplicação personalizada escrita na linguagem c# (C-sharp) .NET.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
reviewer: jasonwhowell, kfile, tsidocs
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 250dd691c3ef3146d6768123de52bf0628b10e42
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66728953"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Consultar dados a partir do ambiente de disponibilidade geral do Azure Time Series Insights utilizandoC#

Isso C# exemplo demonstra como consultar dados a partir do ambiente de disponibilidade geral do Azure Time Series Insights.

Este exemplo mostra vários exemplos básicos de utilização da API de Consulta:

1. Como passo de preparação, adquira o token de acesso pela API do Azure Active Directory. Transmitir este token no `Authorization` cabeçalho de cada pedido de API de consulta. Para configurar aplicativos não interativos, consulte [autenticação e autorização](time-series-insights-authentication-and-authorization.md). Além disso, certifique-se de todas as constantes definidas no início do exemplo estão definidas corretamente.
1. É obter a lista de ambientes em que o utilizador tem acesso. Um dos ambientes é escolhido como o ambiente de interesse, e ainda mais os dados são consultados para este ambiente.
1. Como um exemplo de pedido HTTPS, os dados de disponibilidade são necessários para o ambiente de interesse.
1. Como um exemplo do pedido de socket Web, o evento agrega dados necessários para o ambiente de interesse. Os dados são necessários para o intervalo de tempo de toda a disponibilidade.

> [!NOTE]
> O código de exemplo está disponível em [ https://github.com/Azure-Samples/Azure-Time-Series-Insights ](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="project-dependencies"></a>Dependências do projeto

Adicionar pacotes NuGet `Microsoft.IdentityModel.Clients.ActiveDirectory` e `Newtonsoft.Json`.

## <a name="c-example"></a>Exemplo do c#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre a consulta, leia os [referência da API de consulta](/rest/api/time-series-insights/ga-query-api).

- Leia como a [ligar uma aplicação de página única de JavaScript](tutorial-create-tsi-sample-spa.md) para Time Series Insights.