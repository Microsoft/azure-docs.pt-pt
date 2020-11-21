---
title: Dados de consulta de um ambiente Gen1 usando código C# - Azure Time Series Insights Gen1 [ Microsoft Docs
description: Saiba como consultar dados de um ambiente Azure Time Series Insights Gen1 utilizando uma aplicação personalizada escrita em C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 724bc80f8887ff2c47db93ecfc4cd2e20be7e7f8
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020049"
---
# <a name="query-data-from-the-azure-time-series-insights-gen1-environment-using-c-sharp"></a>Dados de consulta do ambiente Azure Time Series Insights Gen1 usando C Sharp

> [!CAUTION]
> Este é um artigo da Gen1.

Este exemplo C# demonstra como usar as [APIs de consulta da Gen1 Para](/rest/api/time-series-insights/gen1-query) consultar dados dos ambientes da Azure Time Series Insights Gen1.

> [!TIP]
> Ver amostras de código Gen1 C# em [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen1-sample/csharp-tsi-gen1-sample) .

## <a name="summary"></a>Resumo

O código de amostra abaixo demonstra as seguintes características:

* Como adquirir um token de acesso através do Azure Ative Directory utilizando [o Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Como passar o token de acesso adquirido no `Authorization` cabeçalho dos pedidos subsequentes da API.

* A amostra chama cada uma das APIs de consulta da Gen1 que demonstram como os pedidos HTTP são feitos para:
  * [Obter Ambientes API](/rest/api/time-series-insights/gen1-query-api#get-environments-api) para devolver os ambientes a que o utilizador tem acesso
  * [Obter Disponibilidade ambiental API](/rest/api/time-series-insights/gen1-query-api#get-environment-availability-api)
  * [Obtenha API de Metadados ambientais](/rest/api/time-series-insights/gen1-query-api#get-environment-metadata-api) para recuperar metadados ambientais
  * [Obter Eventos de Ambiente API](/rest/api/time-series-insights/gen1-query-api#get-environment-events-api)
  * [Obter Agregados ambientais API](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api)

* Como interagir com as APIs de consulta da Gen1 usando wSS para enviar uma mensagem:

  * [Get Environment Events Streamed API](/rest/api/time-series-insights/gen1-query-api#get-environment-events-streamed-api)
  * [Obter Agregados ambientais Streamed API](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Pré-requisitos e configuração

Complete os seguintes passos antes de compilar e executar o código de amostra:

1. [Provisionar um ambiente Gen1 Azure Time Series Insights.](./time-series-insights-get-started.md)
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

Consulte o [Azure Time Series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen1-sample/csharp-tsi-gen1-sample/Program.cs)] repo para aceder ao código de amostra C#.

## <a name="next-steps"></a>Próximos passos

* Para saber mais sobre consultas, leia a [referência API de Consulta.](/rest/api/time-series-insights/gen1-query-api)

* Leia como [ligar uma aplicação JavaScript utilizando o cliente SDK](https://github.com/microsoft/tsiclient) a Time Series Insights.
Azure-Samples/Azure-Time-Series-Insights/gen1-sample/csharp-tsi-gen1-sample/Program.cs