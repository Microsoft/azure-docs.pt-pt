---
title: Escreva código para rastrear pedidos com a Azure Application Insights | Microsoft Docs
description: Escreva código para rastrear pedidos com Insights de Aplicação para que possa obter perfis para os seus pedidos.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.custom: devx-track-csharp
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: aaa1d6df9faa20b1a561bfccdfea682af7645c18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88930252"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Escreva código para rastrear pedidos com Insights de Aplicação

Para visualizar perfis para a sua aplicação na página Performance, o Azure Application Insights precisa de acompanhar os pedidos da sua aplicação. O Application Insights pode rastrear automaticamente os pedidos de aplicações que são construídas sobre quadros já instrumentados. Dois exemplos são ASP.NET e ASP.NET Core. 

Para outras aplicações, como as funções de trabalhadores da Azure Cloud Services e APIs apátridas de tecido de serviço, é necessário escrever código para dizer à Application Insights onde os seus pedidos começam e terminam. Depois de ter escrito este código, solicita que a telemetria seja enviada para a Application Insights. Pode ver a telemetria na página Performance e os perfis são recolhidos para esses pedidos. 

Para rastrear manualmente os pedidos, faça o seguinte:

  1. No início da vida útil da aplicação, adicione o seguinte código:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Para obter mais informações sobre esta configuração global da chave de instrumentação, consulte [Use Service Fabric com Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  1. Para qualquer pedaço de código que pretenda instrumentar, adicione uma declaração `StartOperation<RequestTelemetry>` **de utilização** à sua volta, como mostra o seguinte exemplo:

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

        Ligar `StartOperation<RequestTelemetry>` dentro de outro âmbito não é `StartOperation<RequestTelemetry>` suportado. Em vez disso, pode `StartOperation<DependencyTelemetry>` utilizar-se no âmbito aninhado. Por exemplo:  
        
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
