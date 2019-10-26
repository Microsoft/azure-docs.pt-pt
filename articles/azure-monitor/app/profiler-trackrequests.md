---
title: Escrever código para controlar solicitações com o Aplicativo Azure insights | Microsoft Docs
description: Escreva o código para rastrear solicitações com Application Insights para que você possa obter perfis para suas solicitações.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 3f449c98ed44f13fb6b3849ef2457cd8fbd916de
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900018"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Escrever código para rastrear solicitações com Application Insights

Para exibir perfis para seu aplicativo na página desempenho, Aplicativo Azure insights precisa controlar as solicitações para seu aplicativo. Application Insights pode controlar automaticamente as solicitações de aplicativos criados em estruturas já instrumentadas. Dois exemplos são ASP.NET e ASP.NET Core. 

Para outros aplicativos, como funções de trabalho dos serviços de nuvem do Azure e Service Fabric APIs sem monitoração de estado, você precisa escrever código para informar Application Insights onde suas solicitações começam e terminam. Depois de escrever esse código, solicitações de telemetria são enviadas para Application Insights. Você pode exibir a telemetria na página desempenho e os perfis são coletados para essas solicitações. 

Para controlar manualmente as solicitações, faça o seguinte:

  1. No início do tempo de vida do aplicativo, adicione o seguinte código:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Para obter mais informações sobre essa configuração de chave de instrumentação global, consulte [usar Service Fabric com Application insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  1. Para qualquer parte do código que você deseja instrumentar, adicione um `StartOperation<RequestTelemetry>` **usando** a instrução em volta dele, conforme mostrado no exemplo a seguir:

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

        Não há suporte para a chamada de `StartOperation<RequestTelemetry>` dentro de outro escopo de `StartOperation<RequestTelemetry>`. Em vez disso, você pode usar `StartOperation<DependencyTelemetry>` no escopo aninhado. Por exemplo:  
        
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
