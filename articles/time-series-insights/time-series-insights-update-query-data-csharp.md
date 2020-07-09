---
title: Dados de consulta de um ambiente de pré-visualização usando C# - Azure Time Series Insights Microsoft Docs
description: Aprenda a consultar dados de um ambiente Azure Time Series Insights utilizando uma aplicação escrita em C#.
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
ms.openlocfilehash: fbc2cbc29cb23a21e7d3713091fc22f01bb1b15a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81379821"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Dados de consulta do ambiente de pré-visualização da Série de TempoS Azure utilizando C #

Este exemplo C# demonstra como consultar dados a partir das APIs de [pré-visualização](https://docs.microsoft.com/rest/api/time-series-insights/preview) do acesso a dados em ambientes de pré-visualização de séries de tempo Azure.

> [!TIP]
> Ver amostras de código pré-visualização C# em [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample) .

## <a name="summary"></a>Resumo

O código de amostra abaixo demonstra as seguintes características:

* Suporte para a geração automática SDK da [Azure AutoRest](https://github.com/Azure/AutoRest).
* Como adquirir um token de acesso através do Azure Ative Directory utilizando [o Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* Como passar o token de acesso adquirido no `Authorization` cabeçalho dos pedidos subsequentes da API de Acesso a Dados. 
* A amostra fornece uma interface de consola que demonstra como os pedidos HTTP são feitos para:

    * [Pré-visualização ambientes API](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [Obter Disponibilidade de Ambientes API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) e [Obter API de Schema de Eventos](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [Consulta de pré-visualização API](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [Obter Eventos API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents), [Obter API série](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries), e [Obter API série agregada](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [APIs modelo série de tempo](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * [Obtenha hierarquias API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get) e [Hierarquias Lote API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch)
        * [Obtenha API de tipos](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get) e [tipos de lote API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch)
        * [Obter API de Instâncias](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get) e [Instâncias Lote API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch)
* [Procura](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features) Avançada e capacidades [de TSX.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

## <a name="prerequisites-and-setup"></a>Pré-requisitos e configuração

Complete os seguintes passos antes de compilar e executar o código de amostra:

1. [Provisionar um ambiente de Pré-visualização Azure Time Series Insights.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment)
1. Configure o seu ambiente Azure Time Series Insights para o Azure Ative Directory, conforme descrito na [Autenticação e autorização](time-series-insights-authentication-and-authorization.md). 
1. Executar o [GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat) conforme especificado no [Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md) para gerar as dependências do cliente de Previsão de Insights de Séries Tempo.
1. Abra a `TSIPreviewDataPlaneclient.sln` solução e definir `DataPlaneClientSampleApp` como o projeto padrão no Estúdio Visual.
1. Instale as dependências de projetos necessárias utilizando os passos [descritos abaixo](#project-dependencies) e compile o exemplo para um ficheiro executável. `.exe`
1. Executar o `.exe` ficheiro clicando duas vezes nele.

## <a name="project-dependencies"></a>Dependências de projetos

Recomenda-se que utilize a versão mais recente do Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - Versão 16.4.2+

O código de amostra tem várias dependências necessárias que podem ser vistas no [ficheiropackages.config.](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config)

Descarregue os pacotes no Visual Studio 2019 selecionando a opção **Build**  >  **Build Solution.** 

Em alternativa, adicione cada pacote utilizando [o NuGet 2.12+](https://www.nuget.org/). Por exemplo:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>Código de amostra C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * A amostra de código pode ser executada sem alterar as variáveis ambientais padrão.
> * A amostra de código compila-se-á para uma aplicação de consola executável .NET.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre consultas, leia a [referência API de Consulta.](https://docs.microsoft.com/rest/api/time-series-insights/preview-query)

- Leia como [ligar uma aplicação JavaScript utilizando o cliente SDK](https://github.com/microsoft/tsiclient) a Time Series Insights.
