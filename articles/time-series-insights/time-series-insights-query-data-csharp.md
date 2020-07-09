---
title: Dados de consulta de um ambiente GA usando código C# - Azure Time Series Insights Microsoft Docs
description: Saiba como consultar dados de um ambiente Azure Time Series Insights utilizando uma aplicação personalizada escrita em C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: 754d1b80236d138693987cccee7a218ccd96b16b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81383891"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Dados de consulta do ambiente Azure Time Series Insights GA utilizando C #

Este exemplo C# demonstra como utilizar as [APIs de consulta](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) de APIs de consulta a partir de ambientes GA da Série de TempoS Azure.

> [!TIP]
> Ver amostras de código GA C# em [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample) .

## <a name="summary"></a>Resumo

O código de amostra abaixo demonstra as seguintes características:

* Como adquirir um token de acesso através do Azure Ative Directory utilizando [o Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Como passar o token de acesso adquirido no `Authorization` cabeçalho dos pedidos subsequentes da API. 

* A amostra chama cada uma das APIs de consulta de GA que demonstra como os pedidos HTTP são feitos para:
    * [Obter Ambientes API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api) para devolver os ambientes a que o utilizador tem acesso
    * [Obter Disponibilidade ambiental API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [Obtenha API de Metadados ambientais](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api) para recuperar metadados ambientais
    * [Obter Eventos de Ambiente API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [Obter Agregados ambientais API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* Como interagir com as APIs de consulta ga-ury usando WSS para enviar uma mensagem:

   * [Get Environment Events Streamed API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [Obter Agregados ambientais Streamed API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Pré-requisitos e configuração

Complete os seguintes passos antes de compilar e executar o código de amostra:

1. [Provisionar um ambiente GA Azure Time Series Insights.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)
1. Configure o seu ambiente Azure Time Series Insights para o Azure Ative Directory, conforme descrito na [Autenticação e autorização](time-series-insights-authentication-and-authorization.md). 
1. Instale as dependências de projetos necessárias.
1. Edite o código de amostra abaixo substituindo cada **#DUMMY#** pelo identificador de ambiente apropriado.
1. Execute o código dentro do Estúdio Visual.

## <a name="project-dependencies"></a>Dependências de projetos

Recomenda-se que utilize a versão mais recente do Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - Versão 16.4.2+

O código de amostra tem duas dependências necessárias:

* [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) - pacote 3.13.9.
* [Newtonsoft.Js-](https://www.nuget.org/packages/Newtonsoft.Json) pacote 9.0.1.

Descarregue os pacotes no Visual Studio 2019 selecionando a opção **Build**  >  **Build Solution.**

Em alternativa, adicione os pacotes utilizando [o NuGet 2.12+](https://www.nuget.org/)

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>Código de amostra C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre consultas, leia a [referência API de Consulta.](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)

- Leia como [ligar uma aplicação JavaScript utilizando o cliente SDK](https://github.com/microsoft/tsiclient) a Time Series Insights.
