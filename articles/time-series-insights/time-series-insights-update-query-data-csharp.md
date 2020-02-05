---
title: Consultar dados de um ambiente de visualização C# usando-Azure Time Series insights | Microsoft Docs
description: Saiba como consultar dados de um ambiente de Azure Time Series Insights usando um aplicativo escrito em C#.
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
ms.openlocfilehash: 76e3ac85a6725976ebd14dac1805079613c94ec6
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76980992"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Consultar dados do ambiente de visualização de Azure Time Series Insights usandoC#

Este C# exemplo demonstra como consultar dados das APIs de acesso a dados de [pré-visualização](https://docs.microsoft.com/rest/api/time-series-insights/preview) em ambientes de pré-visualização da Série de Tempo Azure Insights.

> [!TIP]
> Ver C# amostras de código de pré-visualização em [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample).

## <a name="summary"></a>Resumo

O código da amostra abaixo demonstra as seguintes características:

* Suporte para a geração automática SDK a partir do [Azure AutoRest](https://github.com/Azure/AutoRest).
* Como adquirir um token de acesso através do Diretório Ativo Azure utilizando [o Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* Como passar aquele símbolo de acesso adquirido no `Authorization` cabeçalho dos pedidos subsequentes da API de Acesso a Dados. 
* A amostra fornece uma interface de consola demonstrando como os pedidos http são feitos para:

    * [Pré-visualização Ambientes API](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [Obtenha API de disponibilidade](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) de ambientes e [obtenha evento Schema API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [Pré-visualização Consulta API](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [Obtenha eventos API,](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents) [Obtenha Série API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)e [Obtenha API série agregada](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [Modelo de Série de Tempo APIs](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * [Obtenha hierarquias API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get) e [Hierarquias Lote API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch)
        * [Obtenha tipos API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get) e [Tipos De Lote API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch)
        * [Obtenha Instâncias API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get) e [Instâncias Lote API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch)
* Capacidades avançadas de [pesquisa](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features) e [TSX.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

## <a name="prerequisites-and-setup"></a>Pré-requisitos e configuração

Conclua as etapas a seguir antes de compilar e executar o código de exemplo:

1. Fornecer um ambiente [de Insights da Série de Tempo Azure.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment)
1. Configure seu ambiente de Azure Time Series Insights para Azure Active Directory conforme descrito em [autenticação e autorização](time-series-insights-authentication-and-authorization.md). 
1. Executar o [GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat) conforme especificado no [Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md) para gerar as dependências do cliente de Pré-visualização da Série De Tempo.
1. Abra a solução `TSIPreviewDataPlaneclient.sln` e coloque `DataPlaneClientSampleApp` como o projeto padrão no Estúdio Visual.
1. Instale as dependências de projeto sinuosas utilizando os passos [descritos abaixo](#project-dependencies) e compilar o exemplo para um ficheiro `.exe` executável.
1. Faça o ficheiro `.exe` clicando duas vezes nele.

## <a name="project-dependencies"></a>Dependências do projeto

Recomenda-se que use a mais recente versão do Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -versão 16.4.2 +

O código da amostra tem várias dependências necessárias que podem ser vistas no ficheiro [embalagem.config.](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config)

Descarregue os pacotes no Visual Studio 2019 selecionando a opção **Build** > **Build Solution.** 

Em alternativa, adicione cada pacote utilizando [NuGet 2.12+](https://www.nuget.org/). Por exemplo:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C#código de exemplo

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * A amostra de código pode ser executada sem alterar as variáveis ambientais padrão.
> * A amostra de código irá compilar para uma aplicação de consola executável .NET.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre consultas, leia a [referência de API de consulta](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Leia como [conectar um aplicativo JavaScript usando o SDK do cliente](https://github.com/microsoft/tsiclient) para Time Series insights.
