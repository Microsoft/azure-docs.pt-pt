---
title: Consultar dados de um ambiente Azure Time Series Insights GA usando C# código | Microsoft Docs
description: Este artigo descreve como consultar dados de um ambiente de Azure Time Series Insights codificando um aplicativo personalizado escrito na linguagem C# .net (C-Sharp).
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
reviewer: jasonwhowell, kfile, tsidocs
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: 353e3463c2be552210847ac3fe17bbfe2cec58c7
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958122"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Consultar dados do ambiente Azure Time Series Insights GA usandoC#

Este C# exemplo demonstra como consultar dados do ambiente Azure Time Series insights ga.

Este exemplo mostra vários exemplos básicos de utilização da API de Consulta:

1. Como uma etapa de preparação, adquira o token de acesso por meio da API Azure Active Directory. Passe esse token no cabeçalho `Authorization` de cada solicitação de API de consulta. Para configurar aplicativos não interativos, consulte [autenticação e autorização](time-series-insights-authentication-and-authorization.md). Além disso, verifique se todas as constantes definidas no início do exemplo estão definidas corretamente.
1. A lista de ambientes aos quais o usuário tem acesso é obtida. Um dos ambientes é escolhido como o ambiente de interesse e outros dados são consultados para esse ambiente.
1. Como um exemplo de pedido HTTPS, os dados de disponibilidade são necessários para o ambiente de interesse.
1. Como um exemplo do pedido de socket Web, o evento agrega dados necessários para o ambiente de interesse. Os dados são necessários para o intervalo de tempo de toda a disponibilidade.

> [!NOTE]
> O código de exemplo está disponível em [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="project-dependencies"></a>Dependências do projeto

Adicione pacotes NuGet `Microsoft.IdentityModel.Clients.ActiveDirectory` e `Newtonsoft.Json`.

## <a name="c-example"></a>C#exemplo

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre consultas, leia a [referência de API de consulta](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Leia como [conectar um aplicativo JavaScript de página única](tutorial-create-tsi-sample-spa.md) para Time Series insights.