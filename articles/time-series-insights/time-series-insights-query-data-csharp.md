---
title: Consultar dados de um ambiente Azure Time Series Insights GA usando C# código | Microsoft Docs
description: Este artigo descreve como consultar dados de um ambiente de Azure Time Series Insights codificando um aplicativo personalizado escrito na linguagem C# .net (C-Sharp).
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: 23e4ad5667019b5bc38f9ba11d14632d4d55d5b7
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989967"
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

Adicione os pacotes NuGet `Microsoft.IdentityModel.Clients.ActiveDirectory` e `Newtonsoft.Json`.

## <a name="c-example"></a>C#exemplo

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre consultas, leia a [referência de API de consulta](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Leia como [conectar um aplicativo JavaScript usando o SDK do cliente](https://github.com/microsoft/tsiclient) para Time Series insights.