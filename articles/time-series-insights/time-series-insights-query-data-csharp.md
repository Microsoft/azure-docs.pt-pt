---
title: Dados de consulta de C# um ambiente GA usando código - Azure Time Series Insights  Microsoft Docs
description: Saiba como consultar dados de um ambiente de Insights da Série C#De Tempo Azure usando uma aplicação personalizada escrita em .
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 9f7819974e3548baf5e10f0bf9a2d656d9412beb
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987976"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Dados de consulta do ambiente Azure Time Series Insights GA usandoC#

Este C# exemplo demonstra como usar as [APIs](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) de consulta ga para consultar dados de ambientes Ga Da Série De Tempo Azure.

> [!TIP]
> Ver C# amostras de código GA em [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="summary"></a>Resumo

O código da amostra abaixo demonstra as seguintes características:

* Como adquirir um token de acesso através do Diretório Ativo Azure utilizando [o Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Como passar que adquiriu ficha de acesso no `Authorization` cabeçalho dos pedidos subsequentes da API de Consulta. 

* A amostra chama cada uma das APIs de consulta da GA demonstrando como os pedidos http são feitos para:
    * [Obtenha API ambientes](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api) para devolver os ambientes a que o utilizador tem acesso
    * [Obtenha API de disponibilidade ambiental](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [Obtenha API de Metadados ambientais](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api) para recuperar metadados ambientais
    * [Obtenha Eventos de AmbienteS API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [Obter API agregados ambientais](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* Como interagir com as APIs de consulta ga usando wSS para enviar o:

   * [Obtenha eventos ambientais transmitidos API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [Obter agregados ambientais streamed API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Pré-requisitos e configuração

Conclua as etapas a seguir antes de compilar e executar o código de exemplo:

1. [Provisione um ambiente de Azure Time Series insights GA](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started) .
1. Configure seu ambiente de Azure Time Series Insights para Azure Active Directory conforme descrito em [autenticação e autorização](time-series-insights-authentication-and-authorization.md). 
1. Instale as dependências de projeto necessárias.
1. Editar o código da amostra abaixo substituindo cada **#DUMMY#** com o identificador ambiente apropriado.
1. Execute o código dentro do Estúdio Visual.

## <a name="project-dependencies"></a>Dependências do projeto

Recomenda-se que use a mais recente versão do Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -versão 16.4.2 +

O código de exemplo tem duas dependências necessárias:

* [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) - pacote 3.13.9.
* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) - pacote 9.0.1.

Descarregue os pacotes no Visual Studio 2019 selecionando a opção **Build** > **Build Solution.**

Em alternativa, adicione os pacotes utilizando [nuGet 2.12+](https://www.nuget.org/)

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>C#código de exemplo

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre consultas, leia a [referência de API de consulta](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Leia como [conectar um aplicativo JavaScript usando o SDK do cliente](https://github.com/microsoft/tsiclient) para Time Series insights.
