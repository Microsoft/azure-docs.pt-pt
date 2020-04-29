---
title: Escreva código para acompanhar pedidos com Insights de Aplicação Azure [ Microsoft Docs
description: Escreva código para rastrear pedidos com Insights de Aplicação para que possa obter perfis para os seus pedidos.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: c59cbe852a91a91c7b3adb4452328700ec718a82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77671601"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Escreva código para acompanhar pedidos com Insights de Aplicação

Para ver os perfis da sua aplicação na página performance, o Azure Application Insights necessita de acompanhar os pedidos para a sua aplicação. Os Insights de Aplicação podem acompanhar automaticamente os pedidos de aplicações que são construídas em quadros já instrumentados. Dois exemplos são ASP.NET e ASP.NET Core. 

Para outras aplicações, como as funções de trabalhador da Azure Cloud Services e as APIs apátridas do Service Fabric, é necessário escrever código para dizer insights de aplicação onde os seus pedidos começam e terminam. Depois de ter escrito este código, os pedidos de telemetria são enviados para a Aplicação Insights. Pode ver a telemetria na página Performance e os perfis são recolhidos para esses pedidos. 

Para rastrear manualmente os pedidos, faça o seguinte:

  1. No início do tempo de vida da aplicação, adicione o seguinte código:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Para obter mais informações sobre esta configuração global da chave de instrumentação, consulte [use Service Fabric com Insights de Aplicação](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  1. Para qualquer peça de código que pretenda instrumentar, adicione uma `StartOperation<RequestTelemetry>` declaração de **utilização** à sua volta, como mostra o seguinte exemplo:

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

        Ligar `StartOperation<RequestTelemetry>` para `StartOperation<RequestTelemetry>` outro âmbito não é suportado. Em vez `StartOperation<DependencyTelemetry>` disso, podes usar no âmbito aninhado. Por exemplo:  
        
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
