---
title: Criar uma definição de OpenAPI para uma função de API Management do Azure
description: Crie uma definição de OpenAPI que permite que outras aplicações e serviços chamem a sua função no Azure.
services: functions
keywords: OpenAPI, Swagger, aplicações na nuvem, serviços cloud,
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: glenga
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 3ad304bc8f038d4009352dae72d70079828c26ba
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141449"
---
# <a name="create-an-openapi-definition-for-a-function-with-azure-api-management"></a>Criar uma definição de OpenAPI para uma função de API Management do Azure

REST APIs são muitas vezes descritas com uma definição de OpenAPI. Esta definição contém informações sobre as operações que estão disponíveis numa API e a forma como os dados de pedido e resposta para a API devem ser estruturados.

Neste tutorial, vai criar uma função que determina se uma reparação de emergência numa turbina eólica é rentável. Em seguida, criar uma definição de OpenAPI para a aplicação de função com [gestão de API do Azure](../api-management/api-management-key-concepts.md) para que a função pode ser chamada a partir de outras aplicações e serviços.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma função no Azure
> * Gerar uma definição de OpenAPI com a API Management do Azure
> * Testar a definição, chamando a função

## <a name="create-a-function-app"></a>Criar uma aplicação de função

Precisa de uma aplicação Function App para alojar a execução das suas funções. Uma aplicação function app permite agrupar funções como uma unidade lógica para facilitar a gestão, implementação, dimensionamento e a partilha de recursos.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>Criar a função

Este tutorial utiliza uma função acionada por HTTP, que usa dois parâmetros:

* O tempo estimado para fazer uma turbina reparação, em horas.
* A capacidade da turbina, em quilowatts. 

Em seguida, a função calcula o custo da reparação e a receita que a turbina poderá gerar num período de 24 horas. Para criar o HTTP função acionada por no [portal do Azure](https://portal.azure.com).

1. Expanda a aplicação de funções e selecione o botão **+** junto a **Funções**. Selecione **-portal** > **continuar**.

1. Selecione **mais modelos...** , em seguida, selecione **Finish e vista de modelos**

1. Selecionar acionador HTTP, escreva `TurbineRepair` para a função **Name**, escolha `Function` para  **[nível de autenticação](functions-bindings-http-webhook.md#http-auth)** e, em seguida, selecione  **Criar**.  

    ![Criar função HTTP para o OpenAPI](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. Substitua os conteúdos do csx C# ficheiro pelo seguinte código de script, em seguida, escolha **salvar**:

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    const double revenuePerkW = 0.12;
    const double technicianCost = 250;
    const double turbineCost = 100;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        // Get query strings if they exist
        int tempVal;
        int? hours = Int32.TryParse(req.Query["hours"], out tempVal) ? tempVal : (int?)null;
        int? capacity = Int32.TryParse(req.Query["capacity"], out tempVal) ? tempVal : (int?)null;
    
        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
    
        // Use request body if a query was not sent
        capacity = capacity ?? data?.capacity;
        hours = hours ?? data?.hours;
    
        // Return bad request if capacity or hours are not passed in
        if (capacity == null || hours == null){
            return new BadRequestObjectResult("Please pass capacity and hours on the query string or in the request body");
        }
        // Formulas to calculate revenue and cost
        double? revenueOpportunity = capacity * revenuePerkW * 24;  
        double? costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;
    
        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        };
    
        return (ActionResult)new OkObjectResult(new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix
        });
    }
    ```

    Este código de função devolve uma mensagem `Yes` ou `No` para indicar se uma reparação de emergência é rentável, bem como a oportunidade de receita que a turbina representa e o custo para reparar a turbina.

1. Para testar a função, clique em **Testar** na extremidade direita para expandir o separador de teste. Introduza o seguinte valor para o **Corpo do pedido** e, em seguida, clique em **Executar**.

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![Testar a função no portal do Azure](media/functions-openapi-definition/test-function.png)

    O seguinte valor é devolvido no corpo da resposta.

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

Agora, tem uma função que determina a rentabilidade das reparações de emergência. Em seguida, gerar uma definição de OpenAPI para a aplicação de funções.

## <a name="generate-the-openapi-definition"></a>Gerar a definição de OpenAPI

Agora, está pronto para gerar a definição de OpenAPI.

1. Selecione a aplicação de função e, em seguida, selecione **funcionalidades de plataforma**, **todas as definições**

    ![Testar a função no portal do Azure](media/functions-openapi-definition/select-all-settings-openapi.png)

1. Desloque para baixo, em seguida, escolha **gestão de API** > **criar novo** para criar uma nova instância de gestão de API.

    ![Função de ligação](media/functions-openapi-definition/link-apim-openapi.png)

1. Utilize as definições de gestão de API conforme especificado na tabela abaixo da imagem.

    ![Criar novo serviço de gestão de API](media/functions-openapi-definition/new-apim-service-openapi.png)

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome globalmente exclusivo | É gerado um nome com base no nome da sua aplicação de funções. |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual este novo recurso é criado. |  
    | **[Grupo de Recursos](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | O mesmo recurso que a sua aplicação de função, o que deve obter definido por si. |
    | **Localização** | EUA Oeste | Escolha a localização E.U.A. oeste |
    | **Nome da organização** | Contoso | O nome da organização utilizada no portal do programador e para notificações por e-mail. |
    | **E-mail do administrador** | o seu e-mail | Mensagem de correio eletrónico recebido notificações do sistema de gestão de API. |
    | **Escalão de preço** | Consumo (pré-visualização) | Para obter os detalhes de preços completos, veja o [página de preços de gestão de API](https://azure.microsoft.com/pricing/details/api-management/) |
    | **Application Insights** | A instância | Utilize o Application Insights, mesmo que é utilizado pela sua aplicação function app. |

1. Escolher **criar** para criar a instância de gestão de API, que poderá demorar vários minutos.

1. Selecione **ativar o Application Insights** para enviar registos ao mesmo local que a aplicação de função, em seguida, aceite as restantes predefinições e selecione **ligação API**.

1. O **as funções do Azure de importação** abre-se com o **TurbineRepair** função realçada. Escolher **selecione** para continuar.

    ![Importar as funções do Azure para gestão de API](media/functions-openapi-definition/import-function-openapi.png)

1. Na **criar da aplicação de função** página, aceite as predefinições e selecione **Create**

    ![Criar a partir da Aplicação de Função](media/functions-openapi-definition/create-function-openapi.png)

A API está agora criada para a função.

## <a name="test-the-openapi-definition"></a>Testar a definição de OpenAPI

Antes de utilizar a definição de API, deve verificar-se de que ele funciona.

1. Sobre o **teste** separador da sua função, selecione **POST** operação

1. Introduza os valores para **horas** e **capacidade**

```json
{
"hours": "6",
"capacity": "2500"
}
```

1. Clique em **enviar**, em seguida, ver a resposta HTTP.

    ![Testar a função de API](media/functions-openapi-definition/test-function-api-openapi.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma função no Azure
> * Gerar uma definição de OpenAPI com a API Management do Azure
> * Testar a definição, chamando a função

Avance para o próximo tópico para saber mais sobre a gestão de API.

> [!div class="nextstepaction"]
> [Gestão de API](../api-management/api-management-key-concepts.md)
