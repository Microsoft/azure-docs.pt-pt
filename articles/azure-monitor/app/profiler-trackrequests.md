---
title: Escrever código para monitorizar os pedidos com o Azure Application Insights | Documentos da Microsoft
description: Escreva código para monitorizar os pedidos com o Application Insights para que possa obter os perfis para seus pedidos.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 4782e560b580b7f565724dbb35ed9876bffdc256
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730859"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Escrever código para monitorizar os pedidos com o Application Insights

Para ver perfis para a sua aplicação na página de desempenho, o Azure Application Insights tem de monitorizar os pedidos para a sua aplicação. O Application Insights automaticamente pode monitorizar os pedidos para as aplicações que são criadas com a estruturas já instrumentada. Dois exemplos são ASP.NET e ASP.NET Core. 

Para outros aplicativos, como funções de trabalho de serviços Cloud do Azure e APIs sem monitoração de estado do Service Fabric, terá de escrever código para dizer ao Application Insights, onde os pedidos de início e de fim. Depois que escreveu esse código, telemetria de pedidos é enviada para o Application Insights. Pode ver a telemetria na página de desempenho e perfis são recolhidos para esses pedidos. 

Para monitorizar manualmente os pedidos, faça o seguinte:

  1. No início do tempo de vida do aplicativo, adicione o seguinte código:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Para obter mais informações sobre esta configuração de chave de instrumentação globais, consulte [utilizar o Service Fabric com o Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  1. Para qualquer parte do código que deseja instrumentar, adicione uma `StartOperation<RequestTelemetry>` **usando** instrução em torno dele, conforme mostrado no exemplo a seguir:

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        A invocar `StartOperation<RequestTelemetry>` dentro de outra `StartOperation<RequestTelemetry>` âmbito não é suportado. Pode usar `StartOperation<DependencyTelemetry>` aninhada no escopo em vez disso. Por exemplo:  
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```
