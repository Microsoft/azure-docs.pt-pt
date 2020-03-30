---
title: Dados de consulta de um ambiente GA usando código C# - Azure Time Series Insights [ Microsoft Docs
description: Saiba como consultar dados de um ambiente azure Time Series Insights usando uma aplicação personalizada escrita em C#.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987976"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Dados de consulta do ambiente Azure Time Series Insights GA usando C #

Este exemplo C# demonstra como usar as [APIs](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) de consulta ga para consultar dados de ambientes GA da Série De Tempo Azure.

> [!TIP]
> Ver amostras de [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)código GA C# em .

## <a name="summary"></a>Resumo

O código da amostra abaixo demonstra as seguintes características:

* Como adquirir um token de acesso através do Diretório Ativo Azure utilizando [o Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Como passar aquele símbolo de acesso `Authorization` adquirido no cabeçalho dos pedidos subsequentes da API de Consulta. 

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

Complete os seguintes passos antes de compilar e executar o código da amostra:

1. Fornecer um ambiente [ga azure time series Insights.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)
1. Configure o seu ambiente de Insights da Série De Tempo Azure para o Diretório Ativo Azure, conforme descrito na [Autenticação e autorização.](time-series-insights-authentication-and-authorization.md) 
1. Instale as dependências necessárias do projeto.
1. Editar o código da amostra abaixo substituindo cada **#DUMMY#** com o identificador ambiente apropriado.
1. Execute o código dentro do Estúdio Visual.

## <a name="project-dependencies"></a>Dependências do projeto

Recomenda-se que use a mais recente versão do Visual Studio:

* [Estúdio Visual 2019](https://visualstudio.microsoft.com/vs/) - Versão 16.4.2+

O código da amostra tem duas dependências necessárias:

* [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) - pacote 3.13.9.
* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) - pacote 9.0.1.

Descarregue os pacotes no Visual Studio 2019 selecionando a opção **Build** > **Build Solution.**

Em alternativa, adicione os pacotes utilizando [nuGet 2.12+](https://www.nuget.org/)

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>Código da amostra C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre consulta, leia a referência da [Consulta API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Leia como [ligar uma aplicação JavaScript utilizando o SDK do cliente](https://github.com/microsoft/tsiclient) para time series Insights.
