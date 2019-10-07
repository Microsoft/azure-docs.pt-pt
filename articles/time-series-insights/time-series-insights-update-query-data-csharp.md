---
title: Consultar dados de um ambiente de visualização de C# Azure Time Series insights usando código | Microsoft Docs
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
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 6d2395dcfae5d512412048a683a7f1397967499e
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978654"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Consultar dados do ambiente de visualização de Azure Time Series Insights usandoC#

Este C# exemplo demonstra como consultar dados do ambiente de visualização Azure Time Series insights.

Este exemplo mostra vários exemplos básicos de utilização da API de Consulta:

1. Como uma etapa de preparação, adquira o token de acesso por meio da API Azure Active Directory. Passe esse token no cabeçalho `Authorization` de cada solicitação de API de consulta. Para configurar aplicativos não interativos, consulte [autenticação e autorização](time-series-insights-authentication-and-authorization.md). Além disso, verifique se todas as constantes definidas no início do exemplo estão definidas corretamente.
1. A lista de ambientes aos quais o usuário tem acesso é obtida. Um dos ambientes é escolhido como o ambiente de interesse e outros dados são consultados para esse ambiente.
1. Como um exemplo de pedido HTTPS, os dados de disponibilidade são necessários para o ambiente de interesse.
1. Como um exemplo do pedido de socket Web, o evento agrega dados necessários para o ambiente de interesse. Os dados são necessários para o intervalo de tempo de toda a disponibilidade.

> [!NOTE]
> Esse código de exemplo também está disponível em [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample).

## <a name="c-example"></a>C#exemplo

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> O exemplo de código acima pode ser executado sem alterar os valores de ambiente padrão.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre consultas, leia a [referência de API de consulta](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Leia como [conectar um aplicativo JavaScript usando o SDK do cliente](https://github.com/microsoft/tsiclient) para Time Series insights.