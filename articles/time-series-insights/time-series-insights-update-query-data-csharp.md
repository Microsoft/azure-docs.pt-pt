---
title: Dados de consulta de um ambiente da Gen2 usando C# - Azure Time Series Insights | Microsoft Docs
description: Aprenda a consultar dados de um ambiente Azure Time Series Insights Gen2 utilizando uma aplicação escrita em C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: aecd18fd0d568904f9704b749525204ced05f3ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463430"
---
# <a name="query-data-from-the-azure-time-series-insights-gen2-environment-using-c-sharp"></a>Dados de consulta do ambiente Azure Time Series Insights Gen2 usando C Sharp

Este exemplo C# demonstra como consultar dados das APIs de [Acesso a Dados](/rest/api/time-series-insights/reference-data-access-overview) da Gen2 em ambientes Azure Time Series Insights Gen2.

> [!TIP]
> Ver amostras de código Gen2 C# em [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen2-sample/csharp-tsi-gen2-sample) .

## <a name="summary"></a>Resumo

O código de amostra abaixo demonstra as seguintes características:

* Suporte para a geração automática SDK da [Azure AutoRest](https://github.com/Azure/AutoRest).
* Como adquirir um token de acesso através do Azure Ative Directory utilizando [o Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* Como passar o token de acesso adquirido no `Authorization` cabeçalho dos pedidos subsequentes da API de Acesso a Dados.
* A amostra fornece uma interface de consola que demonstra como os pedidos HTTP são feitos para o seguinte:
  * [API de Ambientes Gen2](/rest/api/time-series-insights/reference-environments-apis)
    * [Obter Disponibilidade de Ambientes API](/rest/api/time-series-insights/dataaccessgen2/query/getavailability) e [Obter API de Schema de Eventos](/rest/api/time-series-insights/dataaccessgen2/query/geteventschema)
  * [API de Consulta Gen2](/rest/api/time-series-insights/reference-query-apis)
    * [Obter Eventos API](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents), [Obter API série](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries), e [Obter API série agregada](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
  * [APIs modelo série de tempo](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)
    * [Obtenha hierarquias API](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies) e [Hierarquias Lote API](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    * [Obtenha API de tipos](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes) e [tipos de lote API](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    * [Obter API de Instâncias](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances) e [Instâncias Lote API](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)

* [Procura](/rest/api/time-series-insights/reference-model-apis#search-features) Avançada e capacidades [de TSX.](/rest/api/time-series-insights/reference-time-series-expression-syntax)

## <a name="prerequisites-and-setup"></a>Pré-requisitos e configuração

Complete os seguintes passos antes de compilar e executar o código de amostra:

1. [Provisionar um ambiente gen2 Azure Time Series Insights.](./how-to-create-environment-using-portal.md)
1. Configure o seu ambiente Azure Time Series Insights para o Azure Ative Directory, conforme descrito na [Autenticação e autorização](time-series-insights-authentication-and-authorization.md).
1. Executar o [GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/GenerateCode.bat) conforme especificado no [Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/Readme.md) para gerar as dependências do cliente Azure Time Series Insights Gen2.
1. Abra a `TSIPreviewDataPlaneclient.sln` solução e definir `DataPlaneClientSampleApp` como o projeto padrão no Estúdio Visual.
1. Instale as dependências de projetos necessárias utilizando os passos [descritos abaixo](#project-dependencies) e compile o exemplo para um ficheiro executável. `.exe`
1. Executar o `.exe` ficheiro clicando duas vezes nele.

## <a name="project-dependencies"></a>Dependências de projetos

Recomenda-se que utilize a versão mais recente do Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - Versão 16.4.2+

O código de amostra tem várias dependências necessárias que podem ser vistas no [ficheiropackages.config.](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/packages.config)

Descarregue os pacotes no Visual Studio 2019 selecionando a opção **Build**  >  **Build Solution.**

Em alternativa, adicione cada pacote utilizando [o NuGet 2.12+](https://www.nuget.org/). Por exemplo:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>Código de amostra C#

Consulte o [Azure Time Series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen2-sample/csharp-tsi-gen2-sample) para aceder ao código de amostra C#.

> [!NOTE]
>
> * A amostra de código pode ser executada sem alterar as variáveis ambientais padrão.
> * A amostra de código compila-se-á para uma aplicação de consola executável .NET.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre consultas, leia a [referência API de Consulta.](/rest/api/time-series-insights/reference-query-apis)

* Leia como [ligar uma aplicação JavaScript utilizando o cliente SDK](https://github.com/microsoft/tsiclient) a Azure Time Series Insights.